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

Python 自动流程支持传统的 ``Burnup/StepN/GammaSource`` 标量源，也支持直接读取
``Burnup/StepN/FETGammaSource`` 全阶源。FET 源保留粗区内的空间模态，分步和自动计算的用法见
:ref:`section_r2s_fet_source`。

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

-  **Step**\ ：取用第几个燃耗/活化步的衰变光子源作为第二步光子源。取值为非负整数，不得超过
   输入的照射与衰变步数；所选步须有可用的衰变光子源。缺省取最后一步，包含末尾的冷却步。

-  **Population**\ ：第二步光子输运的粒子数，须为至少 101 的整数。
   缺省取第一步 \ **FixedSource**\ 的 ``population``。

-  **Spectrum**\ ：第二步光子源的能谱形式。

   - ``fine``\ （缺省）：使用逐条谱线。标量源生成 Distribution ``type = 1``；FET 全阶源通过
     ``FETGAMMASOURCE`` 直接读取；
   - ``bin``\ ：仅用于标量源，按 \ **Energy**\ 给定的能量边界分箱（Distribution ``type = 4``），
     此时必须给出 \ **Energy**\ 选项。FET 自动计算只支持 ``fine``。

-  **Energy**\ ：当 ``Spectrum = bin`` 时给出分箱的能量边界，单位为 MeV，至少两个值，
   须为有限、非负且严格递增的数列。FET 自动计算不设置此项。

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
  ``Burnup/StepN/GammaSource``，FET 全阶源位于 ``Burnup/StepN/FETGammaSource``；
- 第二步（光子输运）的结果归档在 ``archive/r2s_photonN/`` 下（``N`` 为所取的活化步），剂量率位于其中
  的 ``.Tally`` 文件；
- 运行日志中会打印该步的衰变光子源总强度，例如
  ``R2S: total decay photon source strength at Step4 = 2.614183E+08``。


.. _section_r2s_fet_source:

FET 全阶衰变光子源
--------------------------

FET 燃耗的 \ **DECAYSOURCE**\ 默认关闭；只有在第一步 \ **BURNUP**\ 模块中显式写入该卡时，程序才会将
全阶 FET 衰变光子源输出到 ``<inp>.Result.h5`` 的 ``Burnup/StepN/FETGammaSource``。当前支持范围为固定源
``SOURCERATE`` 活化、``COUPLEMODE 2``（SIMPLE）和离散光子谱线。用于直接输运时不要在
\ **DECAYSOURCE**\ 中设置 ``ENERGY``，``CUSTOMBIN`` 应省略或设为 ``0``。当前直接采样器支持局部有界
多区域源，各区域采用径向二阶、轴向一阶展开（R2-A1）；所有有源区域必须使用相同的轴和原点、
在 R-A 源空间中不重叠，且局部坐标必须与全局坐标一致。无变换的嵌套 universe 可以使用；含平移、
旋转或 lattice 实例的场不支持直接输运。
其它受支持阶数仍可输出全阶衰变源，但不能由该接口直接输运。

第二步按全阶 FET 源密度采样 ``r^2`` 和轴向位置，方位角均匀，出射方向各向同性。为保证源密度非负，
读取时会逐能量线按需统一缩放高阶系数，保持各谱线的体积积分源强及总源强不变。

``Burnup/StepN/FETGammaSource/<field>`` 的 ``EnergyMeV`` 为谱线能量；
``CoefficientPhotonsPerSecond`` 按“能量在外、模态在内”排列，单位为 photon/s。
``ModeOrders`` 给出模态顺序，R2-A1 为 (0,0)、(0,1)、(1,0)、(1,1)、(2,0)、(2,1)。
每条谱线的零阶系数表示该区域在此能量下的总光子发射率，与 ``VolumeIntegratedPhotonsPerSecond`` 中
对应的数值相等。局部源密度由全部模态重构后除以 ``VolumeCm3``，单位为 photon/(s·cm³)。
每区总源强存于 ``TotalVolumeIntegratedPhotonsPerSecond``，所有区域的合计存于集合属性
``TotalPhotonsPerSecond``。无谱线的 field 保留 ``EnergyCount=0`` 和零总源强，不写能量及谱系数数组。
``DirectTransportReady=1`` 表示该步源满足直接输运接口要求。

以下接续 :ref:`section_burn_fet` 中栅元 4 照射 0.1 d、冷却 10 d 的示例，选取冷却结束的 ``Step2``。
假定第一步输入文件名为 ``activation``，两步输入位于同一目录。
第二步保留相同的几何和材料，移除 \ **BURNUP**\ 模块，并采用以下光子输运、外源及计数器输入：

.. code-block:: none

    FixedSource
    Particle population = 1000000

    ExternalSource
    FETGAMMASOURCE FILE=activation.Result.h5 STEP=2

    Physics
    ParticleMode P
    Photon PPRODUCEE=1 TTB=1

    Tally
    CellTally 2 Type=1 Particle=2 Cell=4 Volume=26.4050862534
        Energy=0.01 0.1 0.5 1 2 5 10 20 DoseRate=ICRP-119-ISO

栅元 4 的半径为 0.41 cm、高为 50 cm，体积为 26.4050862534 cm\ :sup:`3`。
``CellTally 2`` 统计该栅元内的光子通量，并采用 ISO 系数输出剂量率；选择 AP 时将
``DoseRate`` 改为 ``ICRP-119-AP``。

``FILE`` 指向第一步的 ``Result.h5``，路径不能包含空白字符；相对路径以第二步输入卡所在目录为基准。
``FILE=...`` 和 ``STEP=...`` 内部不加空格。程序输出的建议卡只使用
Result 文件名；若两步不在同一目录，应改成正确的相对或绝对路径。``STEP`` 为非负整数，选择
``Burnup/StepN/FETGammaSource`` 中的 ``N``。每个 \ **EXTERNALSOURCE**\ 模块只能定义一张
\ **FETGAMMASOURCE**\ 卡，且只能用于仅光子固定源计算；不能与 ``SOURCE``、``SURFSRCREAD``、
``DISTRIBUTION``、``H5SOURCEINFO`` 或 ``TRANSFORM`` 混用。

为兼容旧流程，已生成的 ``RMC_FET_GAMMA_EXTERNAL_SOURCE_V1`` source-ready sidecar 仍可省略 ``STEP``：

.. code-block:: none

    ExternalSource
    FETGAMMASOURCE FILE=fet_gamma_source_ready.h5

此 FILE-only 形式只用于旧 sidecar；新的计算应优先使用 ``Result.h5 + STEP``，避免额外的源转换步骤。

自动计算沿用相同的 Python \ **R2S**\ 控制模块。采用同一示例时，在第一步输入中保留中子源及
\ **BURNUP**\ 模块，将下面的光子计数器加入 \ **Tally**\ 模块，并添加 \ **R2S**\ 控制卡：

.. code-block:: none

    Tally
    CellTally 2 Type=1 Particle=2 Cell=4 Volume=26.4050862534
        Energy=0.01 0.1 0.5 1 2 5 10 20 DoseRate=ICRP-119-ISO

    R2S
    Step = 2
    Population = 1000000
    Spectrum = fine
    DoseTally = cell 2

FET 自动流程仅支持 ``Spectrum = fine``，不要在 \ **R2S**\ 或 \ **DECAYSOURCE**\ 中设置源能群
``Energy``；剂量计数器自身的 ``Energy`` 能群仍需给出。选定的 cell/mesh 计数器沿用
``DOSERATE=ICRP-119-AP`` 或 ``DOSERATE=ICRP-119-ISO`` 设置，未指定时默认 AP。

控制模块读取所选步的 ``FETGammaSource``，检查 ``DirectTransportReady`` 和总源强，自动生成
``FETGAMMASOURCE FILE=... STEP=...``。空间系数及源强归一化由 RMC 直接读取，不转为标量源，也不重复乘
衰变常数。缺少全阶源或不满足直接输运条件时停止并报错。

自动生成的 ``FILE`` 使用 ``archive/burnup/`` 中结果文件的绝对路径，因此第二步输入归档至
``archive/r2s_photonN/`` 后仍可单独运行。独立运行需保留该 HDF5 文件；若移动归档或复制到其他机器，应
一并复制源文件并更新 ``FILE``。光子阶段继续采用第一步输入中的材料组成。

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

光子输运（第二步）需要材料元素的光子及配套电子数据。库版本由 ``xsdir`` 中匹配的条目决定，
例如光子库可以使用 ``.04p`` 或 ``.80p``。
中子活化（第一步）使用中子数据（如 ``.32c``、``.71c`` 或 ``.830c``）。两步可共用同一份 ``xsdir``：
光子输运会按元素匹配相应光子/电子库，无需为光子输运单独改写材料。
