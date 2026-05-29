.. _database_obtain:

==========
Notes on database
==========

.. contents:: Content

How to obtain the database
--------------

RMC utilizes ACE format neutron cross section libraries (same as MCNP, 
OpenMC, etc.), which are processed from evaluated nuclear databases 
(e.g., ENDF/B) using nuclear data processing codes such as NJOY and RXSP.

Typically, the basic neutron cross section libraries are provided in 
the RMC package, which are processed from ENDF/B-VII.1 using NJOY. 
If you have further needs, please contact us (contact@reallab.org.cn
 and https://forum.reallab.org.cn).

You can also view or download nuclear data from the `National Nuclear Data Center`_,
such as `293.6K neutron cross section library`_, `300K neutron cross section library`_, 
`thermal neutron scattering cross section library`_, which are all based on 
ENDF/B-VII.1 (all with xsdir index file).


.. _National Nuclear Data Center: https://www.nndc.bnl.gov/
.. _293.6K neutron cross section library: https://www.nndc.bnl.gov/endf/b7.1/aceFiles/ENDF-B-VII.1-neutron-293.6K.tar.gz
.. _300K neutron cross section library: https://www.nndc.bnl.gov/endf/b7.1/aceFiles/ENDF-B-VII.1-neutron-300K.tar.gz
.. _thermal neutron scattering cross section library: https://www.nndc.bnl.gov/endf/b7.1/aceFiles/ENDF-B-VII.1-tsl.tar.gz
