.. _section_eng_neutron_coincidence_tally:

Neutron Coincidence Tally (Enterprise Version Only)
=====================================================

RMC supports neutron coincidence tally calculations based on fixed source mode. It features the analysis of 
spontaneous fission neutron multiplicity and induced fission neutron multiplicity. Users can set an external 
neutron source of spontaneous fission type or combine it with other types of neutron sources in the same problem. 
It can provide multiplicity data for neutrons reacting with specified nuclides in specified cells, along with 
input parameters such as pre-delay time and gate width, thus yielding the calculated values of neutron coincidence tally.

Stochastic Neutron Dynamics Input Options
---------------------------------------------

.. code-block:: none

     FIXEDSOURCE
     FissMult  zaid=<nuclide_ID>  method=<number> data=<number> shift=<number>

     EXTERNALSOURCE
     source    particle=<5>

     TALLY     
     celltally <id> 
     particle=<1> 
     cell=<cell_vector_group> 
     cap=<capture_num> <nuclide_IDs> 
     gate=<pre_delay_time gate_width> 
     capmaxnum=<max_number> 
     capmaxmoment=<max_number> 
     normalization=<normal_style>
     rossialpha = <1>
     accumrossialpha = <1>
     timebins = <number>
     timeinterval = <number>
     totaltime = <number>


Among them,

-  **FIXEDSOURCE**\ is based on fixed source mode, with other parameters being the same as in fixed source mode, which will not be repeated here. 
   \ **FissMult**\ defines parameters related to neutron fission multiplicity. \ **EXTERNALSOURCE**\ defines a general source, with particle
   type 5 representing a spontaneous fission source, and other parameters being the same as in fixed source mode, which will not be repeated here.
   \ **TALLY**\ defines tally options, with parameters not written here being the same as those in the tally section, which will not be repeated here.
   Below are the usage methods for the \ **FissMult**\ option and the \ **celltally**\ option.

FissMult Option
~~~~~~~~~~~~~~~~~~~~~~

FissMult option specifies parameters related to neutron fission (including spontaneous and induced fission) multiplicity.

-  **zaid**\ New multiplicity nuclide ID.
-  **method**\ indicates Gaussian sampling algorithm. 0 represents using the sine/cosine sampling method; 1 represents using the Lestone method (matching order); 
   3 represents using the Ensslin/Santi/Beddingfield/Mayo method (1998-2004); 3 represents using the Ensslin/Santi/Beddingfield/Mayo method (1998-2004);5 represents the use of the FREYA code for neutron-induced and spontaneous fission;
   6 represents the use of the LLNL fission library for neutron-induced, spontaneous, and photofission;7 represents the use of the CGMF code for neutron-induced and spontaneous fission; the default option is 3.
-  **data**\ indicates Fission sampling algorithm. 0 represents using bounded integer fission sampling; 1 represents using Lestone's re-evaluated nuclide-related 
   Gaussian width; 2 represents using nuclide-related original Terrell Gaussian width; 3 represents using Ensslin/Santi/Beddingfield/Mayo Gaussian width; the default option is 3.
-  **shift**\ indicates algorithm for correcting the average number of fission neutrons: 0 represents each fission is an integer; 1 represents using the re-evaluated 
   Gaussian width to sample fission neutron multiplicity; 2 represents increasing the threshold of the average number of fission neutrons to maintain multiplicity conservation;
   3 represents sampling the Gaussian distribution without correction (this will cause overestimation); 4 represents using integer sampling in the case of spontaneous fission;
   the default option is 1.



celltally Option
~~~~~~~~~~~~~~~~~~~~~~

celltally option can specify nuclides in specified cells and input pre-delay time and gate width parameters.

-  **particle**\ specifies the particle type for coincidence tally, only supports neutrons currently, which is 1.
-  **cell**\ specifies the cells where capture reactions occur.
-  **cap**\ specifies the coincidence tally, \ ** capture_num**\ specifies the number of nuclides for coincidence tallying, followed by specific nuclide IDs: nuclide_IDs.
-  **gate**\  where\ ** pre_delay_time**\ is the pre-delay time for coincidence tallying, and \ **gate_width** \ is the gate width for coincidence tallying, both in seconds.
-  **capmaxnum**\ specifies the maximum number of coincidences to be tallied, default number is 21.
-  **capmaxmoment**\ specifies the maximum number of orders to be tallied, default number is 12.
-  **normalization**\ specifies Normalization method; 0 for historical normalization, 1 for normalization by the total number of initial source neutrons; the default option is 0.
-  **rossialpha**\ Specifies whether to output the Rossi-alpha value. 0 for no, 1 for yes. Default is 0.
-  **accumrossialpha**\ Specifies whether to output the cumulative Rossi-alpha value (Feynman-alpha). 0 for no, 1 for yes. Default is 0.
-  **timebins**\ The time bin width for the Rossi-alpha value. Default is 1e-5s.
-  **timeinterval**\ The time window width for the cumulative Rossi-alpha value, in seconds (s). Default is 0.001s.
-  **totaltime**\ The total duration for both Rossi-alpha and cumulative Rossi-alpha calculations, in seconds (s). Default is 0.2s.

Neutron Coincidence Tally Module Input Example
-----------------------------------------------

.. code-block:: none

     FIXEDSOURCE
     particle population=1000000
     FissMult zaid=92235 method=3 data=3 shift=1

     EXTERNALSOURCE
     source 1 fraction=1 particle=5 position=0 0 0 energy=2.348
     source 2 fraction=2 particle=1 position=0 0 0 energy=2.348

     TALLY     
     celltally 21  particle=1 cell=3 cap=3  6000  26056 12000 gate=100e-8  500e-8 capmaxnum=500
     celltally 22  particle=1 cell=4 cap=1  2003  gate=200  800 capmaxnum=100 capmaxmoment=16 normalization=1
      
               

This example defines the calculation parameters for neutron coincidence tallying, with a history count of 1,000,000, fission multiplicity settings of method=3 data=3 shift=1, 
two external sources: one spontaneous fission source and one ordinary neutron source, with a source strength ratio of 1:2. Two coincidence tallies are set: the first tallies three 
types of nuclides 6000, 26056, 12000 in cell 3, with a pre-delay time of 100e-8s, gate width of 500e-8s, and a maximum tally count of 500; the second tallies one nuclide 2003 in cell 4, 
with a pre-delay time of 200s, gate width of 800s, maximum tally count of 100, maximum order of 16, and normalization method according to the total number of initial source neutrons 
(due to the presence of a spontaneous fission source, the total number of initial source neutrons is not equal to the total history count).


