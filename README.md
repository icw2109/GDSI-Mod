%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% PMUT Chip GDSII Layout Generator
%
% This repository contains a Python script that uses the gdspy library to 
% programmatically generate a GDSII file for a custom PMUT 
% (Piezoelectric Micromachined Ultrasonic Transducer) chip. Each of the 15 
% layers and design features is carefully defined to comply with the foundry’s 
% design rules and simulation‐validated performance metrics.
%
% -------------------------------------------------------------------------
% INTRODUCTION
%
% The script is designed for advanced chip designers who require automated 
% layout generation for a PMUT device. The design is both hierarchical and 
% modular, yet extremely compact compared to other MEMS GDSII generators. While 
% some implementations may use several thousand lines of code for just five 
% layers, this solution uses compact code to manage 15 layers.
%
% NOTE: Without a thorough understanding of the foundry-specific design rules, 
% modifying this file may easily break these constraints.
%
% -------------------------------------------------------------------------
% KEY FEATURES
%
% - PMUT Array Generation: Automatically creates a 20x20 grid of PMUT elements
%   with configurable pitch and diameter.
%
% - Multi-Layer Routing: Supports over 15 distinct GDSII layers—covering metal 
%   electrodes, vias, ferroelectric regions, ILD fields, etc.
%
% - Custom Vias & Connections: Includes functions to add vias and route connection 
%   lines to bond pads with adjustable parameters.
%
% - Dicing Lane Generation: Automatically creates dicing lanes with hollow centers 
%   and additional overlays.
%
% - Layout Tiling: References the generated top cell into a final layout cell using 
%   a grid with configurable offsets.
%
% - Foundry Compliance: Each added shape conforms to both the foundry design rules 
%   and simulation-based performance requirements.
%
% -------------------------------------------------------------------------
% CODE OVERVIEW
%
% 1. LAYER AND CONFIGURATION SETUP
%
%    - Layer Definitions:
%      The code begins by importing necessary libraries and defining GDSII layers 
%      using a dictionary. Each key (e.g., "BE", "TE") corresponds to a 
%      descriptive layer name paired with its GDSII tuple (layer number, datatype).
%
%            layers = { 
%                "ZR": (1, 0),  % Zero Alignment
%                "BS": (2, 0),  % Backside etch
%                "BE": (3, 0),  % Bottom Electrode (platinum)
%                % ... (other layers)
%            };
%
%    - Configuration Class:
%      The PMUTArrayConfig class stores critical parameters (e.g., spacing, 
%      number of PMUTs, pad sizes, offsets, connection specifications) to 
%      centralize design parameters for easy modification.
%
% 2. GEOMETRY FUNCTIONS
%
%    A suite of functions is provided to generate various geometric elements:
%
%    - add_rectangles_with_xy_control:
%      Places groups of rectangles (typically for bond pads) with precise XY 
%      control, useful for positioning solder pads with customizable intra-group spacing.
%
%    - add_lines_with_horizontal_top:
%      Creates vertical lines with an additional horizontal segment offset 
%      slightly below the top, aiding in connection routing.
%
%    - create_backside_circles:
%      Generates circular backside etch patterns for each PMUT element in a grid.
%
%    - add_electrode_rectangles:
%      Depending on a flag, adds top or bottom electrode rectangles around via 
%      positions, adjusting locations using offsets and gaps.
%
% 3. PMUT ARRAY AND ELECTRODE DEFINITION
%
%    Additional functions add electrodes and support structures:
%
%    - add_electrodes:
%      Adds both top and bottom electrodes (modeled as circles) for each PMUT element.
%      The overlap and radii are tuned to meet design specifications.
%
%    - add_ibeam_ring:
%      Adds ring structures (IBEAM rings) for mechanical reinforcement around each 
%      PMUT element.
%
%    - add_electrode_connection_lines:
%      Draws connection lines from electrodes with adjustable widths and lengths 
%      for both top and bottom electrodes.
%
% 4. VIAS, CONNECTIONS & BOND PADS
%
%    Functions are provided for creating vias and routing them to bond pads:
%
%    - add_vias:
%      Adds multiple rows of square vias at specified offsets. Returns via positions 
%      for further connection routing.
%
%    - connect_vias_to_pads:
%      Connects each via to a bond pad using vertical lines. Connection parameters 
%      (line width, length, offset, direction) are configurable.
%
%    - add_connection_squares:
%      Places small rectangular connection squares at via positions to enhance connection reliability.
%
%    - add_bond_pad:
%      Adds a single bond pad based on the specified size and position.
%
% 5. DICING LANES AND IBEAM STRUCTURES
%
%    To ensure chip singulation and mechanical integrity, the following functions are used:
%
%    - add_dicing_lanes:
%      Creates a dicing lane using an outer rectangle minus an inner rectangle (resulting 
%      in a hollow center). Utilizes both trench (TR) and backside (BS) layers.
%
%    - add_dicing_lanes_with_fld:
%      Similar to the above, but overlays the FLD (Interlayer Dielectric Field) for additional 
%      design specifications.
%
%    NOTE: A commented-out section for IBEAM connection lines (mimicking metal bond pad 
%          connections) is also provided for further mechanical support.
%
% 6. FINAL LAYOUT TILING
%
%    The final section of the code arranges multiple copies of the generated design:
%
%    - Cell References:
%      The original design is referenced several times in a "final cell" using specific offsets.
%
%    - Exclusion Logic:
%      Specific tile indices are excluded from the final layout based on a defined exclusion list.
%
%    Finally, the complete GDS file is written to an output directory (by default, the user's 
%    Desktop) with a filename that includes a timestamp.
%
% -------------------------------------------------------------------------
% OUTPUT
%
% The generated GDS file (e.g., FINAL_LAYOUT_.gds) will be saved to your Desktop.
% You can open and view the file using any GDS viewer, such as KLayout.
%
% -------------------------------------------------------------------------
% CUSTOMIZATION
%
% All design parameters are defined at the beginning of the file or within the 
% PMUTArrayConfig class. You can modify:
%
% - PMUT Array Settings: Adjust spacing, the number of elements, and the PMUT diameter.
% - Electrode Properties: Change electrode radii, pad sizes, and connection parameters.
% - Via & Connection Settings: Customize via sizes, spacing, and routing line specifications.
% - Dicing Lanes & IBEAM Structures: Tweak dimensions and offsets to meet additional 
%   manufacturing constraints.
%
% -------------------------------------------------------------------------
% HAPPY CODING!
%
% If you have any questions, feedback, or suggestions for improvements, please 
% feel free to open an issue or submit a pull request.
%
% NOTE: This project was developed with foundry-specific design rules in mind. 
% Please ensure you fully understand these constraints before modifying the generated layout.
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


