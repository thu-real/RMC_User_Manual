=============================================
Reactor Monte Carlo code RMC·Document
=============================================

The **Reactor Monte Carlo code (RMC)** is independently developed 
by the Reactor Engineering Analysis Laboratory (`REAL`_) at Institute
of Nuclear Energy Science and Engineering Management (INESEM), 
Department of Engineering Physics, Tsinghua University, which is a 
three-dimensional particle transport Monte Carlo code for 
reactor simulations and analysis.

Addressing the fundamental requirements in reactor computational 
analysis while incorporating the characteristics of advanced and 
novel reactor designs, including flexible geometric structures, 
complex neutron energy spectra, diverse material compositions, 
anisotropy, strong leakage (under certain specific conditions), etc., 
RMC serves as the physical computation core of the numerical 
analysis platform for multi-physics multi-scale coupled nuclear 
power systems.

The development of RMC began in 2001. Till now, RMC is capable of 
handling complex geometric structures and employing continuous
energy cross-sections to analyze complex energy spectra and 
materials. RMC can perform calculations for criticality problems, 
including eigenvalue and eigenfunction calculations, detailed burnup
simulations of depletion chains, neutron kinetics and transient process
analysis, on-the-fly nuclear cross-section parallel processing, 
neutron-photon coupled transport, homogenization and group collapsing, 
S/U analysis, neutronics and thermal-hydraulics coupling, etc., 
as required by practical issues. In response to the characteristics 
of the Monte Carlo method, several geometric processing techniques 
were developed and applied in RMC, such as geometric processing 
acceleration, nuclear cross-section processing optimization, 
new methods for transport process simulation (including hybrid 
Monte Carlo), source convergence diagnosis and acceleration, 
tally optimization, large-scale tally and comprehensive parallelism, 
model visualization, and visual modeling, etc., to improve 
computational efficiency.

Current RMC version: RMC 3.5.0

Branch (git-sha):

|

.. figure:: logo/logo_real.png
   :width: 2.4 in
   :align: center

.. only:: html

========
Content
========

.. toctree::
   :maxdepth: 1

   install/index
   usersguide/index


.. _REAL: http://www.reallab.org.cn
