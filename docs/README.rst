=============
文档docs说明
=============

RMC文档 `docs` 包括以下几类：

  + 理论方法( `source/theory` )
  + 安装编译( `source/install` )
  + 使用说明( `source/usersguide` )
  + 开发模式及规范( `source/developguide` )
  + 程序设计( `source/codedesign` )

其中，具体文件主要存放于 `docs/source` 目录下，由 `index` 索引，所有文档文件均采
用 `reStructuredText`_ 标记语言撰写，可在 `GitLab` 上直接查看，也可利
用 `Sphinx`_ 工具直接转换为其他文档格式，如

生成 `HTML` 格式，在 `docs` 目录下运行命令:

    linux系统：

    .. code-block:: sh

        make html

    windows系统：

    .. code-block:: sh

        make.bat html

生成的 `HTML` 网页文档在 `docs/build` 文件夹中。采用同样方式可以生成 `pdf` 格式。

此外，RMC程序设计报告采用 `Doxygen`_ 自动生成方式，在其他文档生成后，运行

    .. code-block:: sh

        doxygen Doxyfile

即会在 `docs/build/doxygen` 目录下生成设计报告。

具体操作过程请参考 `文档规范`_ 。

同时，支持在线浏览主仓库 `develop <https://gitlab.reallab.org.cn/thu-real/RMC/tree/develop>`_ 
分支所生成的 `文档 <https://thu-real.pages.reallab.org.cn/RMC>`_

.. _reStructuredText: http://docutils.sourceforge.net/rst.html
.. _Sphinx: http://www.sphinx-doc.org/
.. _pip: https://pypi.python.org/pypi/pip
.. _文档规范: source/developguide/文档规范.rst
.. _Doxygen: http://www.doxygen.org/index.html
