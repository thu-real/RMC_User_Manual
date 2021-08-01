.. _installing:

==========
安装指南
==========

.. contents:: 目录


安装RMC程序说明
------------------

和许多科学计算程序一样，RMC是无窗口/界面的控制台程序，即程序的核心为二进制
可执行文件，可在命令行窗口、终端中以文本方式运行或控制。

RMC可执行文件是编译源代码生成而来，编译过程请参考编译相关章节文档（个人版本
暂不提供）。目前RMC分为个人版本和企业版本，对于企业版本，我们将按照合同要求
提供安装服务，对于个人版本的安装，请参看下面文档。

个人版本安装
------------------

使用Anaconda安装
=================================

**Note:**

#. 本方法适用于64位的 **Windows** 、 **Linux** 、 **MacOS** 系统；
#. 本方法目前在 **Windows 10** 、 **Ubuntu 18.04 LTS** 、 **MacOS 10.15** 、 **OpenSUSE 15.3** 上进行过测试， 如有在其他系统上使用出现问题的，欢迎进行反馈；
#. 本方法使用的 `Anaconda` 版本为基于 `Python 3.7` 的 `Anaconda3 5.3.1` 或 `miniconda3 3.7.3` 。


安装Anaconda环境
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Anaconda/miniconda` 的下载链接如下：

- `Anaconda3 5.3.1`
   - `Anaconda3 Windows <https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Windows-x86_64.exe>`_
   - `Anaconda3 Linux <https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh>`_
   - `Anaconda3 MacOS <https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-MacOSX-x86_64.sh>`_
- `miniconda3 3.7.3`
   - `miniconda3 Windows <https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-3.7.3-Windows-x86_64.exe>`_ ，使用时可能存在问题，建议使用`Anaconda3`
   - `miniconda3 Linux <https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-3.8.3-Linux-x86_64.sh>`_
   - `miniconda3 MacOS <https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-3.8.3-MacOSX-x86_64.sh>`_

下载后，`Windows` 上直接双击打开，按照指导进行安装即可，如在 `Linux` 或 `MacOS` 上，则可以在终端（terminal）中使用 `bash` 进行安装

.. code-block:: bash

    bash ./Anaconda3-5.3.1-Linux-x86_64.sh

而后按照提示进行安装即可， `Anaconda3` 的安装如有问题，可以在搜索引擎上查阅相关具体教程。

注意：如果希望后续使用更加方便，可以将 `Anaconda` 加入到 `Windows` 的 `path` 环境变量或 `Linux/MacOS` 的 `PATH` 环境变量中。

创建虚拟环境
~~~~~~~~~~~~~~~~~~~~~~

`Anaconda` 的很重要的用途就是使用虚拟环境，以实现环境配置的隔离，**强烈建议大家创建虚拟环境！**

打开 `Anaconda` 的终端

 - `Windows` 上的 `Anaconda Prompt`
 - 添加过 `path` 后的 `CMD` 和 `PowerShell`
 - `Linux/MacOS`上添加过 `PATH` 的 `terminal`

切换到用于存放这个虚拟环境的文件夹中，并执行创建virtual environment和激活virtual environments的命令

.. code-block:: bash

    cd folder_you_like
    conda create -p ./venv python=3.7  # 部分Windows上可能需要将"/"改成"\"
    conda activate ./venv  # 部分Windows上可能需要将"/"改成"\"


安装 `RMC`
~~~~~~~~~~~~~~~~~~~~~~

使用下面命令安装 `RMC 3.5`

.. code-block:: bash

    conda install -c thu_real rmc

如果安装失败，一般是 `channel` 问题，建议添加 `conda-forge` 后再次尝试上面命令，即：

.. code-block:: bash

    conda config --add channels conda-forge
    conda install -c thu_real rmc


使用
~~~~~~~~~~~~~

完成上面安装后，在不关闭终端的情况下，直接运行

.. code-block:: bash

    RMC --version

可以打印出相关信息，说明安装成功。后续在计算目录下准备好下面文件，即可进行计算

- 输入卡文件（假设主输入卡文件名为inp，目前RMC支持include功能，输入卡可能有多个文件）
- 数据库索引文件xsdir，如开启其他功能，可能还需要xsdir_sab等索引文件
- 燃耗相关文件DepthMainLib（燃耗计算时需要）

计算命令如下：

.. code-block:: bash

    # 注意RMC前面不需要加上"./"
    RMC inp  # 串行计算
    RMC -s 2 inp  # OpenMP并行计算，2线程并行
    mpirun -n 2 <path to RMC> inp  # <path to RMC>替换为RMC的实际路径，为venv虚拟环境下的bin文件夹中，MPI并行计算，2进程并行
    mpirun -n 2 <path to RMC> -s 2 inp  # MPI并行计算，2进程*2线程并行


后续想要再次使用RMC时，只需要打开 `Anaconda` 的终端（具体见上方），激活前面创建的虚拟环境，即可使用RMC

.. code-block:: bash

    conda activate <path to venv>  # <path to venv>替换成虚拟环境的路径，注意在Linux/MacOS上，相对路径需要以"./"开头


过去的安装方式
=====================

在过去的安装指南中，可以从RMC程序包中，拷贝RMC可执行文件到合适目录运行即可
，无需安装。而对于个人版本，后面会具体给出安装方法。

注意，根据编译系统、配置等不同，可执行文件具有多种版本，包括：

 - 操作系统：Windows / Linux / Mac OS
 - 系统位数： 32位 / 64位
 - 编译选项： Debug / Release
 - 是否并行： 串行版 / MPI并行版（包括不同MPI库版本，如MPICH，OpenMPI等）

应当根据系统、配置情况，选择合适的程序。而程序的运行方式与上面的计算命令相同。

程序输入相关文件
---------------------

RMC的输入卡编写，请参见 :ref:`usersguide` ，其他数据库相关文件一般在发布版本时附带提供，具体的可以参考 :ref:`database_obtain` 。
