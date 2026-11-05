.. _section_r2s:

R2S 停堆剂量率计算
====================

.. _section_r2s_intro:

计算流程概述
--------------------------

R2S（Rigorous Two-Step，严格两步法）用于计算停堆后的光子剂量率，由 RMC 的 Python 控制模块自动完成
两步计算：

1. **第一步（中子输运 + 活化）**\ ：以中子固定源驱动燃耗/活化计算，得到各活化栅元的活化产物，并输出
   停堆时的衰变光子源（写入 ``<inp>.Result.h5``）。
2. **第二步（光子输运）**\ ：自动读取第一步的衰变光子源作为光子固定源，复用相同的几何与材料进行光子
   输运，并由计数器直接给出剂量率。第二步光子输运目前仅支持沿用初始输入卡中的材料组成。

四者的关系为：中子输运给出中子通量 → 活化计算给出活化产物及其衰变 → 衰变光子源作为第二步的光子源
→ 光子输运给出光子通量并换算为剂量率。

用户只需准备 **一张** 第一步（活化）输入卡，并在其中加入 :ref:`R2S 控制卡 <section_r2s_card>` ；
第二步的光子输运输入卡由程序自动生成、自动运行，无需手动整理衰变源。

.. _section_r2s_step1_input:

第一步输入卡要求
--------------------------

R2S 的第一步必须是固定源-燃耗（活化）计算，而不是临界燃耗计算。输入文件中至少应包含：

-  \ **FixedSource**\ ：给出第一步中子输运的粒子数；
-  \ **ExternalSource**\ ：定义中子固定源；
-  \ **BURNUP**\ ：使用 \ **ActivationCell**\ 指定活化栅元，使用 \ **TimeStep**\ 给出活化/衰变步长，
   使用 \ **SourceRate**\ 给出各步中子源强，并用 \ **DecaySource**\ 指定需要输出衰变光子源的栅元；
-  \ **Tally**\ ：定义第二步光子剂量率所需的光子 cell 或 mesh 计数器，并在 \ **R2S**\ 卡中通过
   \ **DoseTally**\ 引用它。

``neutron_hdf5`` 衰变光子谱的更新约定为谱线强度已包含 λ，使用缺省的 ``LibType = 0`` 即可，无需显式填写。
\ **R2S**\ 卡中的 ``Spectrum = fine`` 或 ``Spectrum = bin`` 控制第二步光子固定源的生成方式：
``fine`` 使用 ``.Result.h5`` 中的逐条衰变光子谱线，``bin`` 则按 \ **R2S**\ 卡中 \ **Energy**\ 给出的
能量边界生成分箱光子源。也就是说，\ **DecaySource**\ 负责把衰变光子源写入结果文件，\ **R2S**\
负责选择该源在第二步输入卡中的精细或分箱表达。用于自动 R2S 时，\ **DecaySource**\ 必须不设置
``CustomBin`` 或设置 ``CustomBin = 0``，以便 ``.Result.h5`` 保留精细谱线；若用户仅需要在燃耗结果文件中
直接输出分箱衰变源，可在 \ **DecaySource**\ 中设置 ``CustomBin = 1`` 和 ``Energy``。

一个第一步输入卡的关键片段如下：

.. code-block:: none

    FixedSource
    Particle population = 100000

    ExternalSource
    Source 1 particle = 1 position = 5 15 5 energy = 14

    BURNUP
    ActivationCell  1 2 3 4 5
    TimeStep        3.5 1.5 5 5
    SourceRate      1e10 1e10 2.5e10 2.5e10
    CoupleMode      2
    DecaySource type = 1 cell = 1 2 3 4 5

    Tally
    Meshtally 2 type = 1 particle = 2 Geometry = 1 Bound = 0 20 0 20 0 10 Scope = 2 2 1
        energy = 0.01 0.02 0.05 0.1 0.2 0.5 1 2 4 8 10

    R2S
    Step       = 4
    Population = 1000000
    Spectrum   = fine
    DoseTally  = mesh 2

.. _section_r2s_card:

R2S 控制卡
--------------------------

在第一步输入卡中加入 \ **R2S**\ 模块即可触发自动化流程。该卡仅由 Python 控制模块读取，不会写入交给
RMC 主程序的输入卡。

.. code-block:: none

    R2S
    Step       = 4
    Population  = 1e7
    Spectrum    = fine
    DoseTally   = mesh 2

各选项说明：

-  **Step**\ ：取用第几个燃耗/活化步的衰变光子源作为第二步光子源。缺省为最后一个活化步。

-  **Population**\ ：第二步光子输运的粒子数。缺省取第一步 \ **FixedSource**\ 的 ``population``。

-  **Spectrum**\ ：第二步光子源的能谱形式。

   - ``fine``\ （缺省）：使用逐条谱线的精细光子源（Distribution ``type = 1``）；
   - ``bin``\ ：将光子源按 \ **Energy**\ 给定的能量边界分箱（Distribution ``type = 4``），此时必须给出
     \ **Energy**\ 选项。

-  **Energy**\ ：当 ``Spectrum = bin`` 时给出分箱的能量边界（单位 MeV，递增数列）。

-  **DoseTally**\ ：指定最终用于剂量率计算的计数器，格式为 ``DoseTally = <类型> <编号>`` ，其中类型
   为 ``cell`` 或 ``mesh`` ，编号为对应计数器的 id。见下文 :ref:`section_r2s_dosetally` 。

.. _section_r2s_dosetally:

剂量率计数器的指定
--------------------------

剂量率由计数器在第二步光子输运中直接统计。用户需要：

1. 在第一步输入卡的 \ **Tally**\ 模块中定义用于剂量率的 **光子** 计数器（``particle = 2``），给出几何
   范围与能量分组。既可以是 cell 计数器，也可以是 mesh 计数器；
2. 在 \ **R2S**\ 卡中用 \ **DoseTally**\ 指明该计数器的类型与编号。

在所选的 CellTally 或 MeshTally 上，通过 ``DOSERATE`` 选择 ICRP-119 注量-剂量转换系数：

- ``DOSERATE = ICRP-119-AP``：前后照射（AP）；
- ``DOSERATE = ICRP-119-ISO``：各向同性照射（ISO）。

R2S 自动生成第二步输入卡时保留用户的选择；所选计数器未指定 ``DOSERATE`` 时，默认添加
``ICRP-119-AP``。分开运行光子阶段时，直接在计数器上指定该选项。同一输入中的不同计数器
可分别选择 AP 或 ISO。

例如，在下方 MeshTally 2 的能量分组后添加 ``doserate = ICRP-119-ISO`` 即选择 ISO，
``DoseTally = mesh 2`` 保持不变。CellTally 使用相同的 ``doserate`` 写法。

剂量率计数器须采用光子通量（``type = 1 particle = 2``），
``Energy`` 为有限、正值、严格递增的能群上限。不支持 ``Time``、``Integral``、``Bin`` 引用或 ``Nest``，
也不能同时使用 ``Dose``、``Gaussian``、``Multiplier``、``Attenuator``、``Absolute`` 或 ``MCNPFormatOutput``。
栅元优先使用计数器 ``Volume``，未指定时使用几何 ``Vol``；体积须为有限正值。
使用几何 ``Vol = 1 cm^3`` 时给出 warning 后继续运行，与燃耗处理一致；未指定 ``Vol`` 时默认取此值。
网格须具有有限的边界和正体积。
MeshTally 的 ``DOSERATE`` 仅支持径迹长度估计器（``Estimator = 1``，缺省值）。

以 2×2 网格剂量率为例：

.. code-block:: none

    R2S
    Step      = 4
    Spectrum  = fine
    DoseTally = mesh 2

    Tally
    Meshtally 2 type = 1 particle = 2 Geometry = 1 Bound = 0 20 0 20 0 10 Scope = 2 2 1
        energy = 0.01 0.015 0.02 0.03 0.04 0.05 0.06 0.08 0.1 0.2 0.4 0.6 0.8 1 2 4 6 8 10

计数器结果输出到第二步的 ``.Tally`` 文件中。对每个 cell/网格单元，在能量分组与 ``Tot`` 行之后给出一行：

.. code-block:: none

    Dose Rate (ICRP-119-AP) =   6.3655E-03 +/- 4.9759E-05 Sv/hr   (RE = 0.0078)

输出中的 ``ICRP-119-AP`` 或 ``ICRP-119-ISO`` 标识所选系数；剂量率均值与总误差均使用同一选择。

剂量率按 ``剂量率 = (通量/体积) × 源强 × 转换因子(E) × 3.6e-9`` 计算，单位为 Sv/hr，源强为第一步
衰变光子源的总强度，体积取对应 cell 或网格单元的体积。换算会考虑计分路径实际已进行的
体积归一化，保证体积只除一次。
总剂量误差按每条光子历史的能群加权和统计，包含能群间相关性；源强和源能谱视为给定值。

.. _section_r2s_run:

运行与输出
--------------------------

R2S 计算通过 RMC 的 Python 模块运行（与普通燃耗计算的运行方式一致），程序检测到输入卡含 \ **R2S**\
模块后会自动依次完成两步：

- 第一步（活化）的结果归档在 ``archive/burnup/`` 下，衰变光子源位于 ``<inp>.burnup.Result.h5`` 的
  ``Burnup/StepN/GammaSource``；
- 第二步（光子输运）的结果归档在 ``archive/r2s_photonN/`` 下（``N`` 为所取的活化步），剂量率位于其中
  的 ``.Tally`` 文件；
- 运行日志中会打印该步的衰变光子源总强度，例如
  ``R2S: total decay photon source strength at Step4 = 2.614183E+08``。

.. _section_r2s_database:

衰变光子谱数据库的选择
--------------------------

燃耗/活化与独立点燃耗均从 ``neutron_hdf5`` 的 ``GammaSpectrum/spectrum`` 读取衰变光子谱。
能量单位为 MeV；更新后的谱线强度单位为光子/(原子·秒)，已包含衰变常数 λ，程序不再补乘 λ。

第一步 \ **BURNUP**\ 的 \ **DecaySource**\ 卡可用 \ **LibType**\ 指定谱强度约定
（详见 :ref:`section_burn_cards` ）：

-  **LibType = 0**\ （缺省）：谱线强度已包含 λ，适用于更新后的 ``neutron_hdf5`` 库；
-  **LibType = 1**\ ：仅兼容强度为光子/衰变、不含 λ 的旧库，由程序补乘 λ。

独立点燃耗的 \ **GAMMASPECTRUM**\ 使用已含 λ 的谱库；其 \ **GammaLibPath**\ 应指向与
燃耗/活化所用库一致的 ``neutron_hdf5`` 目录。

.. important:: ``LibType`` 指定强度单位，不能由目录名判断。对已含 λ 的谱使用 ``LibType = 1`` 会重复乘 λ。

光子输运（第二步）需要材料中各核素的光子（``.04p``）与电子（``.03e``）数据；中子活化（第一步）使用
中子数据（如 ``.32c``/``.60c``）。两步可共用同一份 ``xsdir``：光子输运会按元素自动将中子核素 ID 映射到
对应的光子/电子库（如 ``26056.32c`` → ``26000.04p``），无需为光子输运单独改写材料。
