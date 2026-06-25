.. _section_ais_database_usage:

AIS数据库使用
===============

RMC支持使用HDF5格式AIS核数据库，是最新开发的用以替换ACE核数据库的新格式核数据库，在可读性、可拓展性、代码可维护性方面均实现了突破，
目前该数据库支持连续能量中子的临界源与固定源中子输运计算，和多群中子的临界输运计算，连续能量中子支持的功能有：
所有截面在线处理功能，栅元、面、网格计数器(type 1-8)，截面计数器，点计数器，材料微扰，UFS方法，源收敛诊断与加速，敏感性及不确定性分析。
使用该功能后输入卡的所有涉及材料模块卡的书写格式均有所变化。所有HDF5格式的AIS数据库在RMC中的使用集成在本模块。
使用基于该功能计算时，需要HDF5格式AIS核数据库，与可执行文件放到同一文件夹下，目录格式为：/AISNucDatabase/ENDFB XX。
HDF5格式AIS核数据库有两种获取方式：1.通过AISG软件手动将需要计算的ACE数据库转化成对应的AIS数据库；
2.可以在gitlab平台中thu-real/AISNuclearDatabase仓库中下载ENDFB7.1、8.0和5.0的数据
压缩包，解压到对应位置即可。

.. _section_aismat_mat:

基于HDF5格式AIS数据库的材料输入卡
----------------------------------------

目前，HDF5格式AIS数据库支持连续能量和多群计算。在功能上，目前支持临界源及固定源中子输运计算，支持的功能有：所有截面在线处理功能，
栅元、面、网格计数器(type 1-8)，截面计数器，点计数器，材料微扰，UFS方法，源收敛诊断与加速。

普通材料的输入卡为：

.. code-block:: none

  Mat <mat_id> <density>
      <IsoSym> <FRACTION=...> <ENDFVERSION=...> <TMP=...>
      <IsoSym> <FRACTION=...> <ENDFVERSION=...> <TMP=...>
      ……



其中，

-  **Mat**\ 为材料输入卡关键词。

-  **mat_id**\ 为材料编号，与Cell输入卡当中的填充材料相对应。

-  **density**\ 为材料总密度。\ **density > 0**\ 表示原子密度，单位为10\ :sup:`24`\ 原子/cm\ :sup:`3`\ ；
   \ **density < 0**\ 表示质量密度，单位为g/cm\ :sup:`3`\ ；\ **density = 0**\ 则程序会自动计算材料密度。

-  **IsoSym**\ 指定同位素标识，HDF5格式AIS核数据库是以同位素标识命名的。

-  **FRACTION**\ 为核素在材料中所占的比例。若\ **fraction > 0**\ ，表示原子密度
   份额（相对值），若\ **fraction < 0**\ ，表示质量密度份额（相对值）。同一种材
   料中的\ **fraction**\ 必须具有相同符号。

-  **ENDFVERSION**\ 指定HDF5格式AIS核数据库所基于的数据评价核数据库版本，程序会去以该版本命名的目录下索引以
   \ **IsoSym**\ 命名的核数据文件。

-  **TMP**\ 指定所使用的某同位素核数据库的开尔文温度，程序在读入核数据的时候会根据用户输入的该温度值读入数据文件中
   偏差小于等于0.1K的温度下的核数据。

除普通材料输入卡以外，RMC还提供热化材料输入卡，为连续能量ACE截面指定相应的热化数据库。

.. code-block:: none

  Sab <mat_id>
        <IsoSym> <ENDFVERSION=...> <TMP=...>
        <IsoSym> <ENDFVERSION=...> <TMP=...>
        ……



其中，

-  **Sab**\ 为热化材料输入卡的关键词。

-  **mat_id**\ 为材料编号，与\ **Mat**\ 卡中的材料编号相匹配，必须换行后输入\ **IsoSym**\ 参数。

-  **IsoSym**\ 指定核素所使用的热化数据库标识。

-  **ENDFVERSION**\ 指定HDF5格式AIS核数据库所基于的数据评价核数据库版本，程序会去以该版本命名的目录下索引以
   \ **IsoSym**\ 命名的核数据文件。其中，ENDFVERSION=7.1，ENDFVERSION=8.0对应的是ENDF/B7.1与8.0的连续能量
   中子数据库，ENDFVERSION=5代表的是ENDF/B5.0的多群中子核数据库。

-  **TMP**\ 指定所使用的某同位素核数据库的开尔文温度，程序在读入核数据的时候会根据用户输入的该温度值读入数据文件中
   偏差小于等于0.1K的温度下的核数据。

.. _section_aismat_mg:

多群HDF5格式AIS截面相关输入卡
--------------------------------

若材料输入卡中的核素使用多群HDF5格式AIS截面，\ *用户必须使用以下输入卡为多群截面指定相关
参数选项*\ 。

.. code-block:: none

  MgAce [ErgGrp = <grp_neu>]


其中，

-  **MgAce**\ 为多群核截面相关输入卡的关键词。

-  **ErgGrp**\ 选项卡指定多群中子核截面的群数。

.. _section_aismat_su:

基于HDF5格式AIS数据库的敏感性分析与不确定度分析相关输入卡
----------------------------------------------------------

敏感性分析的输入示例：

.. code-block:: none

    Adjoint
    GENERAL
	BlockSize <Number>
	GPTMETHOD <Number>
	ResponseNu <IsoSym> <ENDFVERSION=...> <IsoSym> <ENDFVERSION=...>
    ResponseMT = <reaction_list_1, reaction _list_2>
    Ratio = <ratio_list_1, ratio _list_2>
    Nuclide <IsoSym> <ENDFVERSION=...>   ……
    Reaction= <reaction_list_1, reaction _list_2, …>   ……
    Constrain <flag_1, flag_2, flag_3,…>
	groupoption <Number>
    Uncertainty
    OUTPUTINTERVAL <Number>
    Cell = <Number>

其中，各选项卡的具体含义请参照 :ref:`section_su` 模块，使用AIS数据库仅
ResponseNu及Nuclide卡部分书写做了更改，书写方式与 :ref:`section_mat_mat` 模块一致。


随机抽样法不确定度分析的输入示例：

.. code-block:: none

    Sampling
    SAMPLESIZE=<Number>
    Nuclide <IsoSym> <ENDFVERSION=...>
    Reaction= <reaction_list_1, reaction _list_2>
    GROUPOPTION <Number>

其中，各选项卡的具体含义请参照 :ref:`section_su` 模块，使用AIS数据库仅
Nuclide卡部分书写做了更改，书写方式与 :ref:`section_mat_mat` 模块一致。


.. _section_aismat_example:

材料模块输入示例
--------------------

连续能量AIS数据库材料模块输入示例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在下面的材料模块中，首先通过\ **Mat**\ 输入卡分别定义了UO\ :sub:`2`\ 和H\ :sub:`2`\ O这两种材料。
UO\ :sub:`2`\ 的质量密度为-10.196g/cm\ :sup:`3`\ ，U235、U238和O16的原子比为0.03 : 0.97 : 2.0。
H\ :sub:`2`\ O的原子密度为0.9997bar\ :sup:`-1`\ cm\ :sup:`-1`\ ，H1和O16的原子比为2 : 1。
通过\ **Sab**\ 输入卡，为H\ :sub:`2`\ O中的H1（1001.30c）指定了热化数据库（h-h2o）。
所有数据库均使用来自ENDF-B8.0的HDF5格式AIS核数据库（程序会自动到数据库路径下的AISNucDatabase/ENDFB8.0查找对应的核数据文件），
该示例均使用温度均为293.6K下的核数据。
在\ **CeAce**\ 输入卡中，\ **pTable = 1**\ 表示使用概率表，\ **ErgBinHash = 1**\ 表示使用哈希表加速能量查找，
\ **DBRC = 1**\ 表示使用DBRC算法， \ **TMS = 0**\ 表示不使用TMS算法，
\ **OTFDB = 1**\ 表示使用高斯厄米特积分方法进行在线多普勒展宽。

.. code-block:: c

    MATERIAL
    mat 1 -10.196
        U235 fraction = 0.03 endfversion = 8.0 tmp = 293.6
        U238 fraction = 0.97 endfversion = 8.0 tmp = 293.6
        O16  fraction = 2.0  endfversion = 8.0 tmp = 293.6
    mat 2 0.9997
        H1  fraction = 2.0   endfversion = 8.0 tmp = 293.6
        O16 fraction = 1.0   endfversion = 8.0 tmp = 293.6
    Sab 2
        h-h2o endfversion = 8.0 tmp = 293.6
    CEACE pTable = 1 ErgBinHash = 1 DBRC = 1 TMS = 0 OTFDB = 1

多群AIS数据库材料模块输入示例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在下面的材料模块中，首先通过\ **Mat**\ 输入卡分别定义了U、He和H\ :sub:`2`\ O这三种材料。
U的原子密度为6.86463E-02bar\ :sup:`-1`\ cm\ :sup:`-1`\ ，U235、U235和U236的原子比
为6.11864e-06 : 7.18132E-04 : 3.29861E-06。
He的原子密度为2.68714E-05bar\ :sup:`-1`\ cm\ :sup:`-1`\  。
H\ :sub:`2`\ O的质量密度为0.661979g/cm\ :sup:`3`\ ，H1和O16的原子比为2 : 1。
通过\ **Mgace**\ 输入卡定义了群数目为30群。
所有数据库均使用来自ENDF-B5.0的HDF5格式AIS核数据库（程序会自动到数据库路径下的AISNucDatabase/ENDFB5.0查找对应的核数据文件），
该示例均使用温度均为293.6K下的核数据。

.. code-block:: c

    MATERIAL
    mat 1 6.86463E-02
        U234 fraction = 6.11864e-06 ENDFVersion = 5 tmp=293.6
        U235 fraction = 7.18132E-04 ENDFVersion = 5 tmp=293.6
        U236 fraction = 3.29861E-06 ENDFVersion = 5 tmp=293.6
    mat 2 2.68714E-05
        He4  fraction = 2.68714E-05 ENDFVersion = 5 tmp=293.6
    mat 3 -0.661979
        O16 fraction = 1          ENDFVersion = 5  tmp=293.6
        H1 fraction = 2           ENDFVersion = 5  tmp=293.6
    Mgace erggrp=30

在上面的材料模块中，通过\ **ErgGrp**\ 选项卡，指定了多群截面的能群数量。

AIS数据库敏感性分析模块输入示例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在下面的敏感性分析计算模块中，\ **GENERAL**\ 指定为广义响应分析；\ **GPTMETHOD**\ = 1选择超历史GEAR‑MC方法；
\ **BlockSize**\ = 10是伴随通量（广义伴随通量）收敛迭代的块大小；\ **ResponseNu**\ 定义相应函数所涉及的核素，这里是AIS格式的
U234和U235；\ **ResponseMT**\ = 18为上述核素依次指定反应类型，两者均为18（总裂变）；\ **Ratio**\ = 1 2 表示响应 = 序号1 ÷ 序号2，
即U234裂变率 / U235裂变率；\ **Nuclide**\ 列出需要分析敏感性的核素，同样为AIS格式的U234和U235；\ **Reaction**\ 按核素顺序，分别指定每个核素
要分析的反应类型；\ **groupoption**\ = 44 使用程序内嵌的44群能量网格输出敏感性系数；\ **OUTPUTINTERVAL**\ = 10每隔10代输出一次中间结果；
\ **OUTPUTTOTALSEN**\ = 1 要求额外输出能量积分的总敏感性系数；\ **UNCERTAINTY**\ 启动不确定度分析；\ **Cell**\ 定义响应量和敏感性
计算的几何作用域，这里指定为栅元编号1。


.. code-block:: c

    Adjoint
    GENERAL
    GPTMETHOD 1
    BlockSize 10
    ResponseNu
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
    ResponseMT =
        18,
        18
    Ratio =
        1 2
    Nuclide
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
    Reaction=
        2 18,
        4  18  102
    groupoption 44
    OUTPUTINTERVAL 10
    OUTPUTTOTALSEN 1
    UNCERTAINTY
    Cell
    CellTallyID 1 Cell = 1


AIS数据库不确定度分析模块输入示例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在下面的不确定度分析计算模块中，\ **SAMPLESIZE**\ = 50指定生成50个扰动样本；\ **Nuclide**\ 列出要对U234、U235、U238这三种
核素的截面进行扰动；\ **Reaction**\ 按核素顺序，分别指定每个核素要分析的反应类型（18只扰动裂变截面）；
\ **groupoption**\ = 44 使用程序内嵌的44群扰动因子数据库。


.. code-block:: c

    Sampling
    SAMPLESIZE=50
    Nuclide
        U234  ENDFVersion = 7.1
        U235  ENDFVersion = 7.1
        U238  ENDFVersion = 7.1
    Reaction=
        18,
        18,
        18
    groupoption 44

