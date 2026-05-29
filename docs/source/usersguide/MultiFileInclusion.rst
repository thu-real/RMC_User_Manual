.. _section_eng_include:

INCLUDE File Inclusion Feature
===================================

The INCLUDE feature provides a method for multi-file input. The target file can be included by simply adding the path of the included file 
after \ **INCLUDE** \. The format is as follows:

.. code-block:: none

    INCLUDE /path/to/file


or

.. code-block:: none

    INCLUDE
    /path/to/file


The file path can be either an absolute path or a relative path, and \ **spaces are allowed** \.

Note:

- The file path \ **does not need and should not be enclosed in quotation marks** \.
- The **INCLUDE** card must be surrounded by blank lines or file termination symbols, meaning it must be a standalone card. The cards in the included 
  file should also need to be complete. Currently, splitting a single card into multiple files is not supported.


