# Klipper Nozzle Cleaning Macro

A Klipper macro for automatic nozzle cleaning, originally created for the Elegoo Neptune 3/4 but compatible with all Klipper-based 3D printers.

This macro is designed for use with the following wiper holder model:  
[Neptune 4 Base/Pro/Max Corner Alignment Guides](https://www.printables.com/model/1196153-neptune-4-corner-alignment-guides-promax)  
[Neptune 4 Plus Corner Alignment Guides](https://www.printables.com/model/1221208-neptune-4-plus-corner-guide-with-wiper)

The macro can be adjusted for other wiper models, but these modifications need proper testing and implementation from us. Feel free to create a feature branch and submit a PR for review.

If you have any questions or would like to discuss the macro, please use this [channel](https://discord.com/channels/969282195552346202/1342271498915807243) in the Elegoo Discord.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Installation

The cleanest and easiest way to get started with KNCM is to use Moonraker's Update Manager utility. This will allow you to easily install and helps to provide future updates when more features are rolled out!

> **Note:**
> We publish 2 branches for the klipper nozzle clean macro. The stable branch, which is recommended to use, is main. If you wan't to check out the latest development, the dev branch might be for you.

1. `SSH` into your Klipper device and execute the following commands:
```bash
cd ~
git clone https://github.com/Open-Elegoo-Community/klipper-nozzle-clean-macro.git
ln -s ~/klipper-nozzle-clean-macro/macro/*.cfg printer_data/config/
```

> **Note:**
> This will change to the home directory, clone the KNCM repo, create a symbolic link of the repo macro files to your printer's config folder.
> 
> It is also possible that with older setups of klipper or moonraker that your config path will be different. Be sure to use the correct config path for your machine when making the symbolic link.

2. Open your `moonraker.conf`file and add this configuration
```yaml

[update_manager Klipper-Nozzle-Clean-Macro]
type: git_repo
path: ~/klipper-nozzle-clean-macro
origin: https://github.com/Open-Elegoo-Community/klipper-nozzle-clean-macro.git
managed_services: klipper
primary_branch: main

```

> **Note:**
> Whenever Moonraker configurations are changed, it must be restarted for changes to take effect. 
> If you do not want Moonraker to notify you of future updates to KNCM, feel free to skip this.

## How to use `nozzle_clean_settings.cfg`
>**Note:**
> For ease of use and understanding, all KNCM configuration is contained inside of `nozzle_clean_settings.cfg`. Any changes you wish to make to KNCM specifically can be found here.
>
> To ensure the CLEAN_NOZZLE macro functions correctly, adjust the following configuration values based on your printer and wiper setup.

### 1. Cleaning Height
**Important: We strongly recommend performing a fresh `SCREWS_TILT_CALIBRATE` and `PROBE_CALIBRATE` (or `CARTO_CALIBRATE`) after installing the wiper holder and before setting `cleaningHeight` to prevent potential damage!**

- **`cleaningHeight`** (default: `30`)
  Defines the nozzle height while cleaning in the wiper.

### 2. Wiping Axis Selection
- **`wipingAxis`** (default: `Y`)
  Determines which axis moves during cleaning.
  - `Y` → The bed moves.
  - `X` → The nozzle moves.

### 3. Printer Bed Size
- **`printerBedYMax`** (default: `229`)
  Maximum Y-coordinate of the bed’s edge.
  Set this to the physical limit of the bed's Y-axis (not the nozzle).
  **Examples:**
  - Neptune 4 → `229`
  - Neptune 4 Pro → `229`
  - Neptune 4 Plus → `330`
  - Neptune 4 Max → `430`

### 4. cleaningLoops
- **`cleaningLoops`** Defines how many complete cleaning cycles are executed.
- **Example:**
  - `cleaningLoops = 1`  
      Executes three passes (first, second, and third pass).
  - `cleaningLoops = 2`  
    Executes six passes (three passes repeated twice).

### 5. purgeShake
- Disable (0) or enable (1) quick left / right moves before cleaning to remove ouzed filament

### 6. debug
- Disable (0) or enable (1) verbose macro messages - useful for debugging

### 7. Filament Processing
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
1. Fork the repository
2. Create a new branch: `git checkout -b feature-name`
3. Make your changes
4. Push your branch: `git push origin feature-name`.
5. Create a pull request

## License
This project is licensed under the [GPL-3.0 license](LICENSE).