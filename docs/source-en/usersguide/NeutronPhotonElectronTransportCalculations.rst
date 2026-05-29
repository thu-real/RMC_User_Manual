.. _section_eng_transport_mode:

Neutron Transport Calculations
==============================

RMC currently supports neutron-photon-electron coupled transport calculations

Neutron Transport Calculation Physics Switch
--------------------------------------------

.. code-block:: none

    Physics
    ParticleMode < particle type >
    Neutron MinEnergy=<params>


where,

-  **Physics** \ is the keyword for calculating physical switches for neutron transport；

-  **ParticleMode** \ represents the particle type corresponding to the Particle Transport Calculation Physics Switch,
   where N represents neutrons, P represents photons, and E represents electrons;

-  **Neutron** \ is the keyword of the Neutron Physics Process Option.

-  **MinEnergy** \ refers to the minimum energy for neutron transport, where the units are in MeV, and the default value
   is 1.0E-20. If the neutron energy is found to be lower than MinEnergy during the neutron transport process, the
   neutron is killed.

Neutron Transport Calculation Physics Switch Input Example
----------------------------------------------------------

If the neutron energy during transport is lower than 1E-11MeV, the neutron will be killed.

.. code-block:: c


    Physics
    ParticleMode n
    Neutron MinEnergy=1E-11

|

Photon Transport Calculations
===============================

RMC currently supports neutron-photon-electron coupled transport calculations

Photon Transport Calculation Physics Switch
-------------------------------------------

.. code-block:: none

    Physics
    ParticleMode < particle type >
    Photon PPRODUCEE=<params> TTB=<params> ADTTB=<params> ANNIHILATION=<params> COHERENT=<params> PHOTONNUCLEUS=<params> DOPPLER=<params> UPERERG=<params> ERGCUTGMA=<params> ELECMULTITIMES=<params> DelayedPhotonScaling=<params> Newphoton=<params>



where,

-  **Physics** \ is the keyword for calculating physical switches for neutron-photon-electron coupled transport;

-  **ParticleMode** \ represents the particle type corresponding to the Particle Transport Calculation Physics Switch,
   where N represents neutrons, P represents photons, and E represents electrons;

-  **Photon** \ is the indicator of the Physics Switch for Photon Transport, which then corresponds to the Physics Switch
   for particle transport;

-  **PPRODUCEE** \ specifies whether electrons are generated during photon transport. A value of 1 means that electrons are
   generated, and 0 means that electrons are not generated. The default value is 0, that is, it is not turned on by
   default;

-  **TTB** \ specifies whether the simplified TTB model (which does not take into account the angular distribution of photons)
   is used to process electrons during photon transport. A value of 0 means that the simplified TTB model is not used, 1 means
   that the simplified TTB model is used, The default value is 0, that is, it is not turned on by default;

-  **ADTTB** \ specifies whether the ADTTB model (which takes into account the angular distribution of photons) is used to
   process electrons during photon transport. A value of 0 means that the ADTTB model is not used, 1 means that the ADTTB
   model is used, The default value is 0, that is, it is not turned on by default;(Note: The TTB and ADTTB options cannot
   be turned on at the same time)

-  **ANNIHILATION** \ is not in use at the moment. This option specifies whether the Annihilation Model is used to process
   electrons during photon transport. A value of 1 means that the Annihilation Model is used, while a value of 0
   indicates that the model is not being used. The default value is 0, that is, it is not turned on by default;

-  **COHERENT** \ specifies whether Thompson scattering is processed during photon transport. A value of 1 means that
   Thompson scattering is processed, and a value of 0 means that Thompson scattering is not processed. The default
   value is 1, that is, it is turned on by default;

-  **PHOTONNUCLEUS** \ specifies whether the photon nuclear reaction is processed during photon transport. A value of 1
   means that the photon nuclear reaction is processed, and 0 means that the photon nuclear reaction is not processed.
   The default value is 0, that is, it is not turned on by default;

-  **DOPPLER** \ specifies whether Doppler broadening is processed during photon transport. A value of 1 means that
   Doppler broadening is processed, and 0 means that Doppler broadening is not processed. The default value is 0, that
   is, it is not turned on by default;

-  **UPERERG** \ specifies the energy boundary value in the photon transport process. If the photon energy is greater than
   the energy boundary value, then the simple photon transport model is used instead. The unit for the energy boundary
   value is MeV, with the default value being 100MeV;

-  **ERGCUTGMA** \ specifies the lower energy limit during photon transport. The unit of the lower energy limit is MeV,
   and the default value is 1.0E-03 MeV;

-  **ELECMULTITIMES** \ specifies the multiplication factor of electrons generated during photon transport. The default
   value is 1.0.

-  **delayedphotonscaling**\  specifies the method for simulating delayed photons in the neutron photon production process by 
   adjusting the weight of photons. A value of 1 indicates that it is enabled (default), while 0 indicates that it is not enabled.

-  **newphoton**\  Specifies the use of a new photon transport process that considers atomic relaxation processes. A value of 1 
   indicates that it is enabled, while 0 indicates that it is not enabled (default). When using this option, users must ensure that 
   the ``photon_hdf5`` photon and atomic relaxation HDF5 nuclear database is present in the RMC_DATA directory to ensure proper calculation 
   by the RMC.

    .. note:: The current version of RMC is compatible with the MCNP photon ACE nuclear database from ``mcplib04p``. Additionally, RMC supports 
      HDF5 format databases for photons that consider atomic relaxation processes. The former simulates vacancies produced during Compton scattering 
      and photoelectric effects using a fluorescence approximation, which has certain limitations. The latter simulates atomic relaxation processes 
      based on photoelectric subshell data from the database, providing higher accuracy. Users are recommended to use the latter option.



Photon Transport Calculation Physics Switch Input Example
---------------------------------------------------------

The fixed source releases 10,000 source neutrons. The fixed source is a point source, located at the coordinates of the
origin point, and the source neutron energy is 0.1 MeV. During pure photon transport calculation, the photon transport
process does not produce electrons, and the simplified TTB model is not used to process electrons. Thompson scattering and
photonuclear reactions are processed, and Doppler broadening operations are not performed. The upper energy limit is
100MeV, the lower energy limit is 1.0E-3, and the multiplication factor of electrons generated by photon transport is
1.0.

.. code-block:: c

    Physics
    ParticleMode p
    Photon  PPRODUCEE = 0  TTB = 0  ANNIHILATION = 0  COHERENT = 1 PHOTONNUCLEUS =1  DOPPLER = 0  UPERERG = 100 ERGCUTGMA = 1.0E-3  ELECMULTITIMES = 1.0

|


Electron Transport Calculations
=================================

Electron Transport Calculation Physics Switch
---------------------------------------------------

.. code-block:: none

    Physics
    ParticleMode < particle type >
    Electron MaxEnergy=<params> MinEnergy=<params> EPRODUCEP=<params>  ErgLossStraggle=<params> BREMS=<params> BREMSANGLE=<params>  BREMSEACHSUBTEP=<params> BREMSERGLOSSMETHOD=<params>  BREMSPHOTONMULTITIMES=<params> XRAYMULTITIMES=<params> KNOCKONMULTITIMES=<params>



where,

-  **Physics** \ is the keyword for calculating physical switches for neutron-photon-electron coupled transport;

-  **ParticleMode** \ represents the particle type corresponding to the Particle Transport Calculation Physics Switch,
   where N represents neutrons, P represents photons, and E represents electrons;

-  **Electron** \ is the keyword for Electron Physics Process Input Option;

-  **EproduceP**\ is the option controls the generation of photons by electrons (default is true).

-  **MaxEnergy** \ represents the highest energy of an electron during electron transport, with the units in MeV. The
   default value is 100. At present, the RMC database requires that MaxEnergy cannot be greater than 100. If the
   electron energy is found to be greater than MaxEnergy during electron transport, an error will be reported;

-  **MinEnergy** \ represents the minimum energy for electron transport, with the units in MeV, and the default value as
   1.0E-3. If the electron energy is lower than MinEnergy during electron transport, the electron is killed;

-  **ErgLossStraggle** \ indicates if the blocking ability undergoes fluctuation.The default value is 1, indicating
   fluctuation. If there is no fluctuation, the blocking ability of electrons in the determined material and energy is
   a fixed value;

-  **BREMS** \ specifies if electrons caused by Bremsstrahlung are considered in the calculations;

-  **BREMSANGLE** \ specifies if the direction of the secondary Bremsstrahlung photons are to be sampled in detail. The
   default is 1, indicating detailed sampling. If the value is set to 0, then simple sampling will be performed instead;

-  **BREMSPHOTONMULTITIMES** \ indicates the number of secondary Bremsstrahlung photons generated in a single
   bremsstrahlung process. It should be a real number greater than or equal to 0, with the default value being 1. The
   weightage of the secondary Bremsstrahlung photons is divided by the value of BREMSPHOTONMULTITIMES accordingly.
   When BREMSPHOTONMULTITIMES is greater than 1, the variance of the secondary Bremsstrahlung photons can be reduced,
   and when it is less than 1, the simulation time of the secondary Bremsstrahlung photons can be reduced.

-  **BREMSEACHSUBTEP** \ specifies if the generation of secondary Bremsstrahlung photons is to be forced at each range
   sub-step. The value of this can be set to either 0 or 1, with the default value being 0. If the value is set to 1,
   BREMSPHOTONMULTITIMES = 1 is required, and the weightages of secondary Bremsstrahlung photons are adjusted as
   desired.

-  **BREMSERGLOSSMETHOD** \ controls the calculation method of the energy loss of electrons due to Bremsstrahlung, which
   can be set to either 0 or 1, with the default value being 1. A value of 1 means that the energy of the first
   Bremsstrahlung photon is used to calculate the electron energy loss, and 0 means that the average energy of all
   Bremsstrahlung photons is used to calculate the electron energy loss.

-  **XRAYMULTITIMES** \ represents the number of secondary X-ray fluorescence or Auger electrons generated during one
   X-ray fluorescence or Auger electron generation process. It should be a real number greater than or equal to 0, with
   the default value being 1. The weightage of the secondary X-ray fluorescence or Auger electron will be divided by the
   value of XRAYMULTITIMES accordingly. When XRAYMULTITIMES is greater than 1, the variance of the secondary X-ray
   fluorescence or Auger electron can be reduced, and when it is less than 1, the simulation time of the secondary
   X-ray fluorescence or Auger electron can be reduced.

-  **KNOCKONMULTITIMES** \ represents the number of secondary electrons produced during a single electron collision.
   It should be a real number greater than or equal to 0, with the default value being 1. The weightage of the
   secondary collided electrons will be divided by the value of XRAYMULTITIMES accordingly. When the value of
   KNOCKONMULTITIMES is greater than 1, the variance of collided electrons can be reduced, and when the value is less
   than 1, the simulation time of collided electrons can be reduced. If the number of electrons collided is large,
   the simulation time required for transport increases by approximately two hours after the Switch is turned on.




Electron Transport Calculation Physics Switch Input Example
-----------------------------------------------------------

Photoelectronic coupling transport calculation, where the upper limit of electron energy is 100MeV, the lower limit is
0.001 MeV, the electron transport process produces photons, prevents energy fluctuations, and produces Bremsstrahlung
electrons. The direction of the secondary Bremsstrahlung photons is sampled in detail, and the secondary
Bremsstrahlung photons are not forced to be generated at each range sub-step. The energy of the first Bremsstrahlung
photon is used to calculate the electron energy loss, and a secondary X-ray fluorescence or Auger electron is
generated during the generation of an X-ray fluorescence or Auger electron, and no collided electrons are generated.


.. code-block:: c

    Physics
    ParticleMode P E
    Photon    PPRODUCEE=1  TTB=0  ANNIHILATION=0  COHERENT=1
              PHOTONNUCLEUS=0  DOPPLER=1  UPERERG=100  ERGCUTGMA=1.0E-3  ELECMULTITIMES=1.0
    Electron  MaxEnergy=100  MinEnergy=1.0E-3  EPRODUCEP=1  ErgLossStraggle=1  BREMS=1
              BREMSANGLE=1  BREMSEACHSUBTEP=0  BREMSERGLOSSMETHOD=1  BREMSPHOTONMULTITIMES=1.0
              XRAYMULTITIMES=1.0  KNOCKONMULTITIMES=0.0

