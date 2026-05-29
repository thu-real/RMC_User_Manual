.. _section_eng_particle_track:

Particle Event Tracking (Enterprise Version Only)
=====================================================

RMC supports the function of filtering particle events according to certain conditions and outputting them. Particle events include initial source, 
particle generation storage, surface crossing, collision, termination, etc. Conditions include the position of the particle when it reacts, neutron 
velocity, weight, whether it enters a specified cell or crosses a certain surface, contribution to specified tallies, etc. The output file name is 
the same as the output file name with the suffix .PTRAC added. The particle event tracking function is used to record the history of neutron reactions 
in fixed source calculations for further detailed research.

Particle Event Tracking Function Input Option
---------------------------------------------

.. code-block:: none

    PTRAC NEU=<flag>  ELEC=<flag> PHO=<flag> 
	  SRC=<flag> BNK=<flag> SUR=<flag> COL=<flag> TER=<flag> 
	  FILE=<flag>
	  WRTIE=<flag> 
	  MAX=<max_number_of_events_in_output>
	  BUFFER=<max_number_in_one_output> 
	  MEPH=<max_number_in_one_history>
	  X=<bmin> <bmax> Y=<bmin> <bmax> Z=<bmin> <bmax>
	  U=<bmin> <bmax> V=<bmin> <bmax> W=<bmin> <bmax>
	  ERG=<bmin> <bmax> WGT=<bmin> <bmax> VEL=<bmin> <bmax>
	  CELL=[cell_number_list]
	  SURFACE=[surface_number_list]
	  CELLTALLY=[celltally_number_list]
	  MESHTALLY=[meshtally_number_list]
	  SURFTALLY=[surftally_number_list]
	  POINTTALLY=[pointtally_number_list]
	  GRP=[grp_number_list]

Among them,
-  **NEU** \  specifies whether to record neutron events, 1 indicates record. If not recording, this option card is not needed.

-  **ELEC** \  specifies whether to record electron events, 1 indicates record. If not recording, this option card is not needed.

-  **PHO** \  specifies whether to record photon events, 1 indicates record. If not recording, this option card is not needed.

-  **SRC** \  specifies whether to record initial source events, 1 indicates record. If not recording, this option card is not needed.

-  **BNK** \  specifies whether to record BANK events, 1 indicates record. If not recording, this option card is not needed.

-  **SUR** \  specifies whether to record surface crossing events, 1 indicates record. If not recording, this option card is not needed.

-  **COL** \  specifies whether to record collision events, 1 indicates record. If not recording, this option card is not needed.

-  **TER** \  specifies whether to record termination events, 1 indicates record. If not recording, this option card is not needed.

-  **FILE** \  specifies the file format for recording, 1 indicates output results in ASCII code, 0 indicates output results in binary. 
   **This option card must be defined.**

-  **WRITE** \  specifies how to record particle events, 1 indicates output detailed particle state results. By default, only brief content of events is output. 
   If using the default value, this option card does not need to be defined.

-  **MAX** \   specifies the maximum number of events to be recorded in one output file. If there are too many events, it may result in a very large output file. 
   The default value is 10000. If using the default value, this option card does not need to be defined.

-  **BUFFER** \  specifies how many events to write to the file in one output process. The default value is 1000. If using the default value, this option card does not need to be defined.

-  **MEPH** \  specifies the maximum number of event outputs during a particle's lifetime. The default value is 100. If using the default value, this option card does not need to be defined.

-  **X** \  filter specifies the x-coordinate range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value
   must be smaller than the second value. If not using this filter, this option card is not needed.

-  **Y** \  filter specifies the y-coordinate range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value 
   must be smaller than the second value. If not using this filter, this option card is not needed.

-  **Z** \  filter specifies the z-coordinate range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value 
   must be smaller than the second value. If not using this filter, this option card is not needed.

-  **U** \  filter specifies the range of cosine values of the angle between the particle's velocity direction and the X-axis when it reacts. The first value is the lower 
   bound, and the second value is the upper bound. The absolute values of both must not exceed 1, and the first value must be smaller than the second value. If not using 
   this filter, this option card is not needed.

-  **V** \  filter specifies the range of cosine values of the angle between the particle's velocity direction and the Y-axis when it reacts. The first value is the lower 
   bound, and the second value is the upper bound. The absolute values of both must not exceed 1, and the first value must be smaller than the second value. If not using 
   this filter, this option card is not needed.

-  **W** \  filter specifies the range of cosine values of the angle between the particle's velocity direction and the Z-axis when it reacts. The first value is the lower 
   bound, and the second value is the upper bound. The absolute values of both must not exceed 1, and the first value must be smaller than the second value. If not using 
   this filter, this option card is not needed.

-  **ERG** \  filter specifies the energy range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value must 
   be smaller than the second value. The unit is MeV. If not using this filter, this option card is not needed.

-  **WGT** \  filter specifies the weight range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value must 
   be smaller than the second value. If not using this filter, this option card is not needed.

-  **VEL** \  filter specifies the velocity range when the particle reacts. The first value is the lower bound, and the second value is the upper bound. The first value must 
   be smaller than the second value. The unit is 1.0e08cm/s. If not using this filter, this option card is not needed.

-  **CELL** \  history filter monitors whether the particle has passed through any of the specified cell ranges in its history. If it has, output all events that pass all 
   filters in this particle's history; otherwise, no events of this particle are output. Cell numbers are separated by a space. If not using this filter, this option card 
   is not needed.

-  **SURFACE** \  history filter monitors whether the particle has crossed any of the specified surface ranges in its history.If it has, all events that pass all filters in 
   this particle's history are output; otherwise, no events of this particle are output. Surface numbers are separated by a space. If not using this filter, this option 
   card is not needed.

-  **CELLTALLY** \  tally filter monitors whether the particle has contributed to any of the specified Cell Tallies in its history. If it has, all events that pass all 
   filters in this particle's history are output; otherwise, no events of this particle are output. CellTally numbers are separated by a space. If not using this filter, this 
   option card is not needed.

-  **SURFTALLY** \  tally filter monitors whether the particle has contributed to any of the specified Surf Tallies in its history. If it has, all events that pass all 
   filters in this particle's history are output; otherwise, no events of this particle are output. SurfTally numbers are separated by a space. If not using this filter, this 
   option card is not needed.

-  **MESHTALLY** \  tally filter monitors whether the particle has contributed to any of the specified Mesh Tallies in its history. If it has, all events that pass all 
   filters in this particle's history are output; otherwise, no events of this particle are output. MeshTally numbers are separated by a space. If not using this filter, this 
   option card is not needed.

-  **POINTTALLY** \  tally filter monitors whether the particle has contributed to any of the specified Point Tallies in its history. If it has, all events that pass all 
   filters in this particle's history are output; otherwise, no events of this particle are output. PointTally numbers are separated by a space. If not using this filter, 
   this option card is not needed.

-  **GRP** \  filter specifies the energy group number when the particle reacts. All are energy group numbers, only used in multi-group calculations. If the particle's 
   energy group number during filtering is in the given energy group list, it passes this filter; otherwise, it does not pass. If not using this filter, this option card 
   is not needed.

-  **MCTT** \  filter is to be enabled when sorting the contribution of specified particles to specified tallies, for the number of particles that contribute the most to the 
   specified tally; if not enabling the filter to sort the contribution of specified particles to specified tallies, this option card is not needed.

- **MCNP** \ To require a MCNP-format output. Default output is RMC-format output, if a MCNP-format output is required, just set MCNP=1.

Particle Event Tracking Module Example
-------------------------------------------

.. code-block:: c

   PTRAC NEU=1 SUR=1 COL=1 MAX=1000 MEPH=2 ERG=1 4 WRITE=1 CELL=1 2 3

