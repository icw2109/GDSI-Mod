PMUT Chip GDSII Layout Generator
This repository contains a Python script that uses the gdspy library to programmatically generate a GDSII file for a custom PMUT (Piezoelectric Micromachined Ultrasonic Transducer) chip. Each of the 15 layers and design features is carefully defined to comply with the foundry’s design rules and simulation‐validated performance metrics.

Introduction
The script is designed for advanced chip designers who require automated layout generation for a PMUT device. The design is both hierarchical and modular yet extremely compact compared to other MEMS GDSII generators. Whereas other implementations may use several thousand lines of code for only five layers, this solution uses compact code to manage 15 layers. However, without a thorough understanding of the foundry-specific design rules, modifying this file may easily break these constraints.

gPMUT generates a full GDSII file that includes:

A PMUT array with precise spacing and electrode definitions.

Multiple metal, via, and support layers including front-end, backside, and intermediate layers.

Customizable bond pad and connection line placement.

Dicing lanes and mechanical support (IBEAM) that ensure a manufacturable layout.

Key Features
PMUT Array Generation: Automatically creates a 20×20 grid of PMUT elements with configurable pitch and diameter.

Multi-Layer Routing: Supports over 15 distinct GDSII layers—covering metal electrodes, vias, ferroelectric regions, ILD fields, etc.

Custom Vias & Connections: Includes functions to add vias and route connection lines to bond pads with various adjustable parameters.

Dicing Lane Generation: Automatically creates dicing lanes with hollow centers and additional overlays.

Layout Tiling: References the generated top cell into a final layout cell using a grid with configurable offsets.

Foundry Compliance: Each added shape conforms to both the foundry design rules and simulation-based performance requirements.

Code Overview
Layer and Configuration Setup
The code begins by importing necessary libraries and defining GDSII layers using a dictionary. Each key (e.g., "BE", "TE") corresponds to a descriptive layer name with its GDSII tuple (layer number, datatype):

python
Copy
layers = {
    "ZR": (1, 0),  # Zero Alignment
    "BS": (2, 0),  # Backside etch
    "BE": (3, 0),  # Bottom Electrode (platinum)
    # ... (other layers)
}
A configuration class, PMUTArrayConfig, stores critical parameters such as spacing, number of PMUTs, pad sizes, offsets, and connection specifications. This centralizes design parameters for easy modification.

Geometry Functions
A series of functions generates various geometric elements:

add_rectangles_with_xy_control:
Places groups of rectangles (typically for bond pads) with precise XY control.
Example: Positioning solder pads with customizable intra-group spacing.

add_lines_with_horizontal_top:
Creates vertical lines with an additional horizontal segment offset slightly below the top. Useful for connection routing.

create_backside_circles:
Generates circular backside etch patterns for each PMUT element in a grid.

add_electrode_rectangles:
Depending on a flag, adds top or bottom electrode rectangles around via positions, adjusting locations using offsets and gaps.

PMUT Array and Electrode Definition
Additional functions add electrodes and support structures:

add_electrodes:
Adds both top and bottom electrodes (modeled as circles) for each PMUT element. Overlap and radii are controlled to meet design specifications.

add_ibeam_ring:
Adds ring structures for mechanical reinforcement (IBEAM rings) around each PMUT element.

add_electrode_connection_lines:
Draws connection lines from electrodes with adjustable widths and lengths for both top and bottom electrodes.

Vias, Connections & Bond Pads
Functions to create vias and route them to solder bond pads include:

add_vias:
Adds multiple rows of square vias at specified offsets. Returns via positions for further connection routing.

connect_vias_to_pads:
Connects each via to a bond pad using vertical lines. Connection parameters (line width, length, offset, direction) are provided as a list.

add_connection_squares:
Places small rectangular connection squares at via positions to enhance connection reliability.

add_bond_pad:
Adds a single bond pad based on the specified size and position.

Dicing Lanes and IBEAM Structures
To ensure chip singulation and mechanical integrity, the following functions are used:

add_dicing_lanes:
Creates a dicing lane using an outer rectangle minus an inner rectangle (resulting in a hollow center). It utilizes both trench (TR) and backside (BS) layers.

add_dicing_lanes_with_fld:
Similar to the above, this function adds dicing lanes while overlaying the FLD (Interlayer Dielectric Field) for additional design specifications.

Note: A commented-out section for IBEAM connection lines (mimicking metal bond pad connections) is also provided for further mechanical support.

Final Layout Tiling
The final portion of the code arranges multiple copies of the generated design:

Cell References:
The original design is referenced several times in a "final cell" using specific offsets.

Exclusion Logic:
Specific tile indices are excluded from the final layout based on a defined exclusion list.

Finally, the complete GDS file is written to an output directory (by default, the user's Desktop) with a filename that includes a timestamp.

Output
The generated GDS file (named like FINAL_LAYOUT_<timestamp>.gds) will be saved on your Desktop. You can open and view the file using any GDS viewer such as KLayout or similar software.

Customization
All design parameters are defined at the beginning of the file or within the PMUTArrayConfig class. You can modify:

PMUT Array Settings: Adjust spacing, the number of elements, and the PMUT diameter.

Electrode Properties: Change electrode radii, pad sizes, and connection parameters.

Via & Connection Settings: Customize via sizes, spacing, and routing line specifications.

Dicing Lanes & IBEAM Structures: Tweak dimensions and offsets to meet additional manufacturing constraints.

Feel free to alter the code functions and configuration settings to match your specific chip design requirements.

Happy Coding!

