# Klipper Nozzle Cleaning Macro

A Klipper macro for automatic nozzle cleaning, originally created for the Elegoo Neptune 3/4 but compatible with all Klipper-based 3D printers.

This macro is designed for use with the following wiper holder model:  
[Neptune 4 Corner Alignment Guides](https://www.printables.com/refresh?redirectUrl=%2Fmodel%2F1196153-neptune-4-corner-alignment-guides)

It can be adjusted for other wiper models, but those modifications are currently unsupported. If you need help, feel free to open an issue.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Installation
1. Copy the latest stable release from [Latest Release](./nozzle_clean.cfg)
2. Add content of `CLEAN_NOZZLE` to your **printer.cfg**
*Info: The placement of CLEAN_NOZZLE is not relevant. I suggest to place if right before the **SAVE_CONFIG** part*
3. **Save & Restart** your printer configuration.

## Usage
To ensure the CLEAN_NOZZLE macro functions correctly, adjust the following configuration values based on your printer and wiper setup.

### 1. Cleaning Height
- **`cleaningHeight`** (default: `10`)
  Defines the nozzle height while cleaning in the wiper.

### 2. Wiping Axis Selection
- **`wipingAxis`** (default: `Y`)
  Determines which axis moves during cleaning.
  - `Y` → The bed moves.
  - `X` → The nozzle moves.

### 3. Printer Bed Size
- **`printerBedYMax`** (default: `235`)
  Maximum Y-coordinate of the bed’s edge.
  Set this to the physical limit of the bed's Y-axis (not the nozzle).
  **Examples:**
  - Neptune 4 → `235`
  - Neptune 4 Plus → `330`
  - Neptune 4 Max → `430`

### 4. cleaningLoops
- **`cleaningLoops`** 
Defines how many complete cleaning cycles are executed.
- **Example:**
  - `cleaningLoops = 1`  
      Executes three passes (first, second, and third pass).
    - `cleaningLoops = 2`  
      Executes six passes (three passes repeated twice).

### 5. Filament Processing
The macro can process a `FILAMENT_TYPE` variable to automatically select the correct nozzle temperature for cleaning.
**Important Notes:**
- Automatic temperature selection only works if the `CLEAN_NOZZLE` macro is called within the `PRINT_START` macro.
- You need to modify both your **slicer** printer start config and **printer.cfg** for proper functionality.

#### Enable automatic Filament processing
##### Step 1: Modify Your Slicer’s Start G-code
1. Open your slicer and navigate to machine settings.
2. Go to the **Machine Start G-code** section.
3. Locate the `PRINT_START` command.
4. Add a space after `PRINT_START` and append:
`FILAMENT_TYPE={filament_type[0]}`

##### Step 2: Adjust `PRINT_START` in **printer.cfg**
1. Open your **printer.cfg** file.
2. Locate the `PRINT_START` macro.
3. Add the following line as the first line under `gcode:`
`{% set FILAMENT_TYPE = params.FILAMENT_TYPE|default("PLA")|upper %}`

##### Step 3: Call `CLEAN_NOZZLE` in `PRINT_START`
1. Add the `CLEAN_NOZZLE` macro with filament variable after heating the print bed and nozzle to the desired temperature.
`CLEAN_NOZZLE FILAMENT_TYPE={FILAMENT_TYPE}`

##### Step 4: SAVE & Restart
1. **Save & Restart** your printer configuration.
2. Your nozzle cleaning macro is now fully integrated.

**Remember to SAVE & RESTART after variable changes**

## Contributing
1. Fork the repository.
2. Create a new branch: `git checkout -b feature-name`.
3. Make your changes.
4. Push your branch: `git push origin feature-name`.
5. Create a pull request.

## License
This project is licensed under the [GPL-3.0 license](LICENSE).