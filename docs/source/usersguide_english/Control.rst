.. _section_eng_control:

CONTROL Option Card
=======================

CONTROL card provides control options for some of RMC's running characteristics. Its format is as follows:

.. code-block:: none

    Control
    Hash CRC64

Currently, the CONTROL card supports the selection of the Hash function, where:

-  **Hash** \   is the input card keyword for selecting the Hash function. The currently valid values are CRC64 and BIT_MOVE (case-insensitive), with the default value being BIT_MOVE.


CRC64 can be referenced at https://en.wikipedia.org/wiki/Cyclic_redundancy_check, while BIT_MOVE can be referenced 
at https://www.sciencedirect.com/science/article/pii/S0306454921002711. If using one of these results in a hash collision error, you can switch to the other.

