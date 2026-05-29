.. _section_eng_refuelling:

Refuelling
====================

RMC hosts refuelling capabilities, with the options and parameters in the RMC input card as follows:

.. code-block:: none

    Refuelling
    Refuel step=<step1 step2 ... stepn> index=<index1 index2 ... indexn>
    File path.yaml

where,

-  **Refuelling** \ is the keyword for the refueling input option;

-  **Refuel** \ is a refueling parameter input option, which is used to specify the fuel refueling step after which the
   fuel is refueled. The step and index options correspond to the number of fuel steps corresponding to each refueling
   and the index value in the refueling yaml input card. If a step value is 0, it means that the fuel is refueled
   before the fuel calculation is performed;

-  **File** \ is the path option of the refuelling yaml file. RMC supports the use of relative path or absolute path to
   specify the path of the refuelling yaml file.


The contents of the refueling yaml file can be written by referring to the refueling.yaml or refueling.yml files
in tests/refuelling/ or RMC/controller/test/resources. The following is an example, and the meaning of each input
option is explained in the comments.

.. code-block:: yaml

    refuelling: !refuelling
      lists:  # there can be multiple !do_refuel refuels
      - !do_refuel
        step: 0  # This number needs to correspond to the index of the input card of RMC
        plan:
        - !refuel_univ
          universe: 1  # The universe number where the repeated geometry of the refuelling assembly is located
          # Position specifies the prefix universe expansion of the refuelling universe, which is a
          # two-dimensional array. The following writing is generally used when the refuelling universe is
          # contained in multiple cells;
          # If there is only one cell containing this universe, you can simply write one. For example, [[6, 31]]
          # means that the universe for material change is 6>31>1 (the last 1 is the universe above).
          position:
          - [3000]     #   Corresponding to the universe expansion of 3000>1
          - [3001]     #   Corresponding to the universe expansion of 3001>1
          - [3002]     #   Corresponding to the universe expansion of 3002>1
          - [3003]     #   Corresponding to the universe expansion of 3003>1
          - [3004]     #   Corresponding to the universe expansion of 3004>1
          - [3005]     #   Corresponding to the universe expansion of 3005>1
          - [3006]     #   Corresponding to the universe expansion of 3006>1
          - [3007]     #   Corresponding to the universe expansion of 3007>1
          - [3008]     #   Corresponding to the universe expansion of 3008>1
          - [3009]     #   Corresponding to the universe expansion of 3009>1
          - [3010]     #   Corresponding to the universe expansion of 3010>1
          - [3011]     #   Corresponding to the universe expansion of 3011>1
          pattern: univ_1  # Use the pattern name in the utilities input option below
          # Refuelling case
          # - 0 indicates no assembly
          # - Letters + numbers are moved from the original position of the assembly to the current position, and
          #   the 0 at the beginning of the number can be filled in at will;
          # - The assembly that starts with NEW_ is a new assembly, and the number that follows is the universe
          #   serial number of the new assembly in the RMC input card;
          # - Letter + number + "-number" means that the assembly that moved out from the position of "letter + number"
          #   in the refuelling cycle corresponding to the number after "-" is to be placed back into its current
          #   position. For example, A005-1 means that the assembly that moved out from the position A5 in the
          #   first cycle is put into its current position.
          # - Letters, numbers, and prefixes for new assemblies can all be set in the pattern of the utilities card.
          #   Strict alignment is not required when writing.
          mapping:
          - [0,      0,      0,      0,      L010,   NEW_57, NEW_56, NEW_57, NEW_56, NEW_57, E010,   0,      0,      0,      0     ]
          - [0,      0,      G010,   NEW_56, NEW_53, L002,   P012,   N003,   B012,   E002,   NEW_53, NEW_56, J010,   0,      0     ]
          - [0,      F009,   NEW_57, N002,   N010,   NEW_54, D011,   R010,   M011,   NEW_54, C010,   C002,   NEW_57, K009,   0     ]
          - [0,      NEW_56, P003,   L008,   NEW_55, M009,   E015,   G008,   L015,   D009,   NEW_55, H005,   B003,   NEW_56, 0     ]
          - [F005,   NEW_53, F003,   NEW_55, M004,   NEW_54, M003,   A010,   D003,   NEW_54, D004,   NEW_55, K003,   NEW_53, K005  ]
          - [NEW_57, P005,   NEW_54, G004,   NEW_54, N008,   R009,   G014,   A009,   H003,   NEW_54, J004,   NEW_54, B005,   NEW_57]
          - [NEW_56, D002,   E012,   A011,   N004,   G001,   B009,   H015,   J014,   J001,   C004,   R011,   L012,   M002,   NEW_56]
          - [NEW_57, N013,   F015,   H007,   F001,   B007,   A008,   F014,   R008,   P009,   K015,   H009,   K001,   C003,   NEW_57]
          - [NEW_56, D014,   E004,   A005-1, N012,   G015,   G002,   H001,   P007,   J015,   C012,   R005,   L004,   M014,   NEW_56]
          - [NEW_57, P011,   NEW_54, G012,   NEW_54, H013,   R007,   J002,   A007,   C008,   NEW_54, J012,   NEW_54, B011,   NEW_57]
          - [F011,   NEW_53, F013,   NEW_55, M012,   NEW_54, M013,   R006,   D013,   NEW_54, D012,   NEW_55, K013,   NEW_53, K011  ]
          - [0,      NEW_56, P013,   H011,   NEW_55, M007,   E001,   J008,   L001,   D007,   NEW_55, E008,   B013,   NEW_56, 0     ]
          - [0,      F007,   NEW_57, N014,   N006,   NEW_54, D005,   A006,   M005,   NEW_54, C006,   C014,   NEW_57, K007,   0     ]
          - [0,      0,      G006,   NEW_56, NEW_53, L014,   P004,   C013,   B004,   E014,   NEW_53, NEW_56, J006,   0,      0     ]
          - [0,      0,      0,      0,      L006,   NEW_57, NEW_56, NEW_57, NEW_56, NEW_57, E006,   0,      0,      0,      0     ]
        # The following three options are used to remove the neutron poison rod (which is often removed after the first cycle):
        poison_universe: [14, 25, 26, 34]  # The universe number of the assembly containing the poison
        guide_tube: 40  # Universe number of the coolant pipe
        poison_rod: 50  # The universe number of the poison rod
      utilities:  # Define some common settings
        universes:
          univ_1:
            alias:
              column: [R, P, N, M, L, K, J, H, G, F, E, D, C, B, A]  # Letter numbers for the columns from left to right
              row: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]  # Numbering of rows from top to bottom
              new: NEW_  # The prefix of the new assembly
            fixed:  # Generally used to define the positions of control rod groups, as these positions do not change as the assembly moves, and are therefore fixed.
              pattern1:  # The writing of multiple patterns is supported, making it convenient to define assemblies with the same fixed position in one pattern
                assemblies: [H004, D008, L008, H012]  # The location of the assembly where the fixed rod is located
                position:  # The position of the rod in the assembly that needs to be fixed
                - [3, 6]
                - [3, 9]
                - [3, 12]
                - [4, 4]
                - [4, 14]
                - [6, 3]
                - [6, 6]
                - [6, 9]
                - [6, 12]
                - [6, 15]
                - [9, 3]
                - [9, 6]
                - [9, 12]
                - [9, 15]
                - [12, 3]
                - [12, 6]
                - [12, 9]
                - [12, 12]
                - [12, 15]
                - [14, 4]
                - [14, 14]
                - [15, 6]
                - [15, 9]
                - [15, 12]
                # The default "rod" universe, where when the assembly with control rods is moved to other positions,
                # the control rod mesh cell position of the moved assembly needs to be filled with coolant pipe mesh
                # cells. The number here is the coolant pipe mesh cell.
                default_pin: 40
            # This card does not need to be filled out manually, nor is it recommended to be filled out manually.
            # RMC will automatically detect and generate the card
            #   Indicates the mapping relationship between the universe serial number of the full-stack repeated
            #   geometry filled in the RMC input card and the universe corresponding to the rod repeated geometry.
            assemblies:
            - [2, 0]
            - [3, 100]
            - [4, 101]
            - [5, 102]
            - [6, 103]
            - [7, 104]
            - [8, 105]
            - [9, 106]

