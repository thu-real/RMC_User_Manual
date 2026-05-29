.. _section_eng_energy_deposition:

Energy Release and Deposition
================================

Although statistical information regarding energy release and deposition has been provided in :ref:`section_eng_tally`, this section will offer a more detailed discussion on 
the statistical methods related to energy release and deposition.

.. _section_eng_energy_release:

Energy Release
------------------

Currently, the term "energy release" typically refers to the energy released during neutron interactions with a medium. Depending on the type of neutron interactions, 
energy release can be broadly categorized into fission energy release and non-fission energy release (including reactions such as (n,g), (n,a), (n,p), etc.). In RMC, 
the statistics of energy release can be implemented by setting different tally types.

In earlier versions of RMC, such as RMC2.5.0, RMC only supported the tallying of fission energy release by setting the tally type to ``type=2``. The energy data released 
during each fission reaction was hard-coded directly into RMC's source code. The source of these data was unclear, but their physical significance was evident; they 
represented only the prompt energy from the fission process, excluding the energies from delayed neutrons, :math:`\gamma` rays, and :math:`\beta` particles. Furthermore, 
this energy release data was independent of the energy of the incident neutrons, meaning that the energy released during fission reactions for neutrons of different energies 
was the same. For example, for :math:`^{235}U` and :math:`^{238}U`, the prompt energies released during each fission reaction are 180.88 MeV and 181.31 MeV, respectively. 
This implies that using earlier versions of RMC leads to inaccurate statistics for fission energy release since it only accounts for prompt fission energy and ignores 
dependencies on the incident neutron energy. Additionally, earlier versions of RMC did not support tallying non-fission energy release.

In versions 3.5.0 and later, RMC's energy release statistics was updated to allow users to tally different types of energy release data based on their needs, considering
different physical meanings and computational costs. Compared to earlier versions of RMC, the updates mainly include:

1. Addressing the issue of unclear sources for built-in energy release data in earlier versions. The new version uses neutron reaction (both fission and non-fission) 
energy release data directly sourced from the ENDF evaluation nuclear database. The energy release data for different nuclides is stored in HDF5 files located in the 
``neutron_hdf5`` folder under the  ``RMC_DATA`` directory. Users can view the data within these files using third-party software like ``HDFViewer``. Additionally, users 
can modify the data within HDF5 files by writing corresponding Python scripts to meet their needs.

2. The latest version of RMC supports tallying fission energy release data related to incident neutron energy but does not support tallying non-fission energy release data
that depends on incident neutron energy. This is because the ENDF evaluation nuclear database provides fission energy release data at different incident neutron energies 
through ``MF1 MT458``, while non-fission reaction energy release data is given in ``MF 3`` as reaction Q-values, which do not depend on incident neutron energy. Therefore, the 
new version of RMC supports only tallying fission energy release data related to incident neutron energy.

3. The latest version of RMC supports tallying non-fission reaction energy releases. As mentioned earlier, this energy release data is independent of incident neutron energy.

Fission Energy Release Statistics
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Building upon the ``type=2`` fission energy statistics from previous versions of RMC (to maintain compatibility with user input files and code testing), the new version of RMC 
adds multiple methods for tallying fission energy releases. These are categorized into two types based on their relationship with incident neutron energies.

Fission Energy Statistics Independent of Incident Neutron Energy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:type 2:
  Inherited from earlier versions of RMC, this type tallies the prompt energy released during fission reactions without including delayed neutrons, delayed :math:`\gamma` rays, 
  :math:`\beta` particles particles, or neutrinos from fission.

:type 11:
  Kappa Fission; this type tallies the usable energy from fission reactions, with each fission event's released energy sourced from ENDF ``MF=3 MT=18`` reaction Q-values.
 

Fission Energy Statistics Related to Incident Neutron Energy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:type 10:
  Usable fission energy; this includes usable energies released during fission reactions consisting of contributions from fission fragments, prompt and delayed neutrons, prompt and 
  delayed :math:`\gamma` rays, and delayed :math:`\beta` particles but excludes neutrinos from fission.

:type 17:
  Prompt fission energy; this type tallies prompt energies released during fission reactions including contributions from fission fragments, prompt neutrons, and prompt :math:`\gamma` rays.

:type 18:
  In addition to the above two types of fission energy statistics, RMC also supports tallying individual components' energy releases during fission reactions including contributions from
  fission fragments, prompt and delayed neutrons, prompt and delayed :math:`\gamma` rays, and delayed :math:`\beta` particles, and neutrinos from fission. Different fission components are
  distinguished by setting the ``component`` option in the Tally as shown in the following table:

  .. table:: Correspondence of Fission Components
    :name: tally_component_eng

    +------------------+-----------------------+
    | component        | Fission Component     |
    +==================+=======================+
    | 1                | Fission Fragments     |
    +------------------+-----------------------+
    | 2                | Prompt Neutrons       |
    +------------------+-----------------------+
    | 3                | Delayed Neutrons      |
    +------------------+-----------------------+
    | 4                | Prompt :math:`\gamma` |
    +------------------+-----------------------+
    | 5                | Delayed :math:`\gamma`|
    +------------------+-----------------------+
    | 6                | Delayed :math:`\beta` |
    +------------------+-----------------------+
    | 7                | Fission Neutrinos     |
    +------------------+-----------------------+
    | 8                | Prompt Fission Energy |
    +------------------+-----------------------+
    | 9                | Usable Fission Energy |
    +------------------+-----------------------+
    | 10               | Total Fission Energy  |
    +------------------+-----------------------+
    

Non-Fission Energy Release Statistics
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For the statistics of non-fission reaction energy release, users can achieve this by setting type=15. This tally sums the total energy released during non-fission reactions 
for all nuclides in the target tally.

:type 15:
    Tallies the energy release from non-fission reactions, including (n,g), (n,a), (n,p), and other non-fission reactions.

Total Energy Release Statistics
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Based on fission energy release statistics and non-fission energy release statistics, RMC supports tallying total energy release data, which is the sum of fission energy release 
and non-fission energy release. Users can implement this by setting ``type=9``.

:type 9:
    Tallies total energy release, including the sum of fission energy release (Kappa fission) and non-fission energy release.

.. note:: The fission energy release data in total energy release statistics is the same as that in ``type=11``, which includes recoverable fission energy, and is related to the 
    energy of incident neutrons.

:type 20:
    Tallies total energy release, including recoverable fission energy and non-fission energy release.
    
.. note:: Unlike ``type=9``, this tally's fission energy release data is related to the energy of incident neutrons.


Energy Deposition
-------------------

In contrast to energy release, energy deposition statistics track how the energy released during neutron or photon interactions is deposited in the medium. In earlier versions of RMC, 
users could tally neutron and photon energy deposition using ``type=6``. However, this method was limited by the neutron heating database and photon transport models, resulting in imprecise 
statistics for energy deposition. Therefore, the current version of RMC introduces a refined energy deposition statistical model that eliminates errors from the neutron heating
database while achieving accurate statistics for neutron and photon energy deposition across different scales through an advanced photon transport model and photon energy deposition 
statistical methods.

The current refined energy deposition statistical model utilizes a newly created neutron heating database. This database is generated using the NJOY program to process neutron evaluation data
from the ENDF evaluation nuclear database. Neutron heating data at various temperature points are stored in HDF5 files for each nuclide within the ``neutron_hdf5`` folder under the ``RMC_DATA`` 
directory, specifically in the ``reactions/heating`` and ``reactions/heating_local`` groups. Currently, this database contains neutron heating data at eight temperature points: 0K, 200K, 294K, 
300K, 600K, 900K, 1200K, and 2500K. If a material's corresponding temperature does not exist in this database, RMC will notify users with an error message. Users needing additional 
temperature points can contact `REAL`_ for more neutron heating data. 

It is worth mentioning that the total energy release statistical methods provided in :ref:`section_eng_energy_release` can also be considered a form of energy deposition statistical method. 
In this statistical approach, all released energies are treated as being deposited locally, meaning that the positions of energy release and deposition are identical. Based on this foundation, 
two different methods for tallying energy deposition have been implemented in RMC: ``heating_local`` and ``heating``. In the former case, the energies of photons (products of neutron reactions) are
assumed to be deposited locally. In contrast, in the latter case, photon energies are deposited as they undergo transport processes during subsequent photon interactions.

heating_local
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In ``heating_local``, photon energies are assumed to be deposited locally. In this case, only pure neutron transport is required while ignoring the transport processes of photons produced by 
neutron reactions. Therefore, this statistical method has a lower computational cost. RMC implements this statistical method through a ``heating_local`` tally that users can activate by setting 
``type=13``.

:type 13:
  Energy deposition statistics where photon energies are assumed to be deposited locally. In this scenario, only pure neutron transport is conducted while ignoring photon transport from neutron reactions.

.. _REAL: https://forum.reallab.org.cn/

heating
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In ``heating``, photon energies are deposited as they undergo transport processes. Consequently, this statistical method requires enabling mixed photon transport. To ensure accuracy in photon energy 
deposition statistics, it is recommended that users utilize the latest version of the photon transport model. RMC controls this through an option added in :ref:`section_eng_material`, specifically
``newphoton 1``, which activates the advanced photon transport model. If users do not set this option, the traditional photon transport model will be used by default.

Compared to ``heating_local``, ``heating`` has a higher computational cost but provides more accurate statistics for energy deposition. To achieve precise tallying for photon energy statistics, parameter 
settings for its tally must also be more detailed. RMC implements this statistical method through a heating tally that users can activate by setting ``type=12``.

:type 12:
  Energy deposition statistics where photon energies are deposited during their transport processes. In this case, mixed photon transport must be enabled.

.. note:: Since mixed photon transport is enabled in ``heating`` , it is recommended that users activate options like TTB, Doppler, PProduceE, and COHERENT when setting up photon transport parameters 
    for accurate calculations; see :ref:`section_eng_transport_mode` .


Energy Deposition Statistics for Different Particles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In ``heating_local`` , all particle energies except neutrons are assumed to be deposited locally. In contrast, in ``heating``, due to mixed photon transport being enabled, secondary electrons and positrons 
generated during photon interactions will also have their energies deposited alongside neutrons and photons. Therefore, it is necessary to distinguish between different particles' energy deposition 
statistics. Users can achieve this by setting the ``particle`` option in their tallies.

.. note:: It should be noted that earlier versions of RMC did not distinguish between electrons and positrons; all tallies related to positrons were treated as tallies for electrons. However, for 
    precise statistics on energy deposition, it is essential to explicitly differentiate between positrons and electrons. Thus, when using the ``newphoton 1`` option card, users can specify ``particle=4`` 
    to tally positron energy deposition.

In certain cases where the user is not concerned about individual particles' contributions to energy deposition within a specific region or material but rather wish to obtain a cumulative measure of all particles'
contributions within that area or material, users can define ``particle=0``. In such cases, all particle energies will be tallied.

.. note:: Since only neutrons are transported in ``heating_local``, only tallies with ``particle=1`` will yield correct results; tallies for other options will all be zero.

Energy Deposition Statistics for Different Types of Photons
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In addition to distinguishing between different particles' energy deposition statistics, RMC also supports tallying the energy deposition from different types of secondary photons generated during 
neutron-induced fission reactions and capture reactions (all non-fission reactions producing photons). These secondary photons primarily include prompt fission photons, delayed fission photons, and 
capture photons. Users can set up their tallies with the ``attr`` option to tally these different types of photons' contributions to overall energy deposition. The correspondence between ``attr`` and particle 
attributes is shown in the following table:

    +------------------+-----------------------+
    | attr             | Particle Attribute    |
    +==================+=======================+
    | 0                | Undefined             |
    +------------------+-----------------------+
    | 1                | Prompt Fission Photon |
    +------------------+-----------------------+
    | 2                | Delayed Fission Photon|
    +------------------+-----------------------+
    | 3                | Capture Photon        |
    +------------------+-----------------------+

.. note:: If users do not define an ``attr`` option, the tally will not differentiate between particle attributes and will default to tallying all types of photons' energies.

Energy Deposition Statistics for Delayed Photons
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The statistics for delayed photons' energy deposition present a complex issue during mixed photon transport processes. In real physical processes, delayed photons from fission arise 
from :math:`\gamma` decay of precursor nuclei generated during fission reactions. The ACE database only stores data on yields, angular distributions, and energy distributions for prompt photons 
produced by neutrons. To achieve accurate statistics on delayed photons' energy deposition, RMC provides two different methods.

The first method simulates delayed photons' energy deposition using prompt photon data from the ACE database. This method assumes that delayed photons have similar angular and energy distributions 
as prompt photons; it adjusts yields based on ratios derived from prompt versus delayed fission energies given in ENDF evaluation nuclear databases under MT458 by calculating ratios corresponding 
to current incident neutron energies and scaling yields accordingly by a factor of :math:`(1+\alpha)` where :math:`\alpha` represents the aforementioned ratio. Furthermore, to account for both prompt and delayed
photons' contributions to overall energy deposition accurately using variance reduction techniques like splitting tricks produces one prompt photon with weight :math:`w` and one delayed photon with 
weight :math:`\alpha w` where :math:`w` represents sampled weights.

.. important:: To produce delayed photons accurately requires enabling the option under :ref:`section_eng_transport_mode`, specifically within  ``photon`` subcard as delayedphotonscaling = 1.

The second method involves obtaining spatial distributions and energetic profiles for delayed photons at specific moments through burnup calculations. By solving fixed-source equations further 
allows us to derive spatial distributions alongside associated energetic depositions accurately; however, this method incurs higher computational costs while yielding more precise results regarding
delayed photons' energetic contributions.

.. note:: This method can be activated via settings found under :ref:`section_eng_burnup`, under a tab labeled  `DecaySource`. Please refer back to :ref:`section_eng_burnup` for further details.

.. caution:: The second method has not been fully tested yet; its functional correctness remains unverified; settings related may change accordingly.