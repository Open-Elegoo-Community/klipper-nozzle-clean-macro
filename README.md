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
> We publish 2 branches for the klipper nozzle clean macro. The stable branch, which is recommended to use, is main. If you wan't to check out the newest changes, the beta branch might be for you.

### 1. `SSH` into your Klipper device and execute the following commands:
#### 1.1 Stable version
```bash
cd ~
git clone https://github.com/Open-Elegoo-Community/klipper-nozzle-clean-macro.git
ln -s ~/klipper-nozzle-clean-macro/macros printer_data/config/KNCM
cp ~/klipper-nozzle-clean-macro/macros/KNCM_settings.cfg ~/printer_data/config/KNCM_settings.cfg
```

#### 1.2 Beta version
```bash
cd ~
git clone -b beta https://github.com/Open-Elegoo-Community/klipper-nozzle-clean-macro.git
ln -s ~/klipper-nozzle-clean-macro/macros printer_data/config/KNCM
cp ~/klipper-nozzle-clean-macro/macros/KNCM_settings.cfg ~/printer_data/config/KNCM_settings.cfg
```

> **Note:**
> This will change to the home directory, clone the KNCM repo, create a symbolic link of the repo macro files to your printer's config folder.
> 
> It is also possible that with older setups of klipper or moonraker that your config path will be different. Be sure to use the correct config path for your machine when making the symbolic link.

### 2. Open your `moonraker.conf`file and add this configuration
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
> If you used the beta branch, then just exchange main to beta in primary_branch setting

### 3. Open your `printer.cfg` file and include the `KNCM_settings.cfg`
Search for your `[include XXX]` objects and append `[include KNCM_settings.cfg]` file.

## How to use `nozzle_clean_settings.cfg`
>**Note:**
> For ease of use and understanding, all KNCM configuration is contained inside of `nozzle_clean_settings.cfg`. Any changes you wish to make to KNCM specifically can be found here.
>
> To ensure the CLEAN_NOZZLE macro functions correctly, adjust the following configuration values based on your printer and wiper setup.

### 1. Cleaning Height
**Important: We strongly recommend performing a fresh `SCREWS_TILT_CALIBRATE` and `PROBE_CALIBRATE` (or `CARTO_CALIBRATE`) after installing the wiper holder and before setting `cleaning_height` to prevent potential damage!**

- **`cleaning_height`** (default: `30`)
  - Defines the nozzle height while cleaning in the wiper.

### 2. Wiping Axis Selection
- **`wiping_axis`** (default: `Y`)
  - Determines which axis moves during cleaning.
    - `Y` → The bed moves.
    - `X` → The nozzle moves.

### 3. Printer Bed Size
- **`printer_bed_y_max`** (default: `229`)
  - Maximum Y-coordinate of the bed’s edge.
    - Set this to the physical limit of the bed's Y-axis (not the nozzle).
  - **Examples:**
    - Neptune 4 → `229`
    - Neptune 4 Pro → `229`
    - Neptune 4 Plus → `330`
    - Neptune 4 Max → `430`

### 4. Cleaning Loops
- **`cleaning_loops`** (default: `1`)
  - Defines how many complete cleaning cycles are executed.
  - **Example:**
    - `cleaning_loops = 1`  
      Executes three passes (first, second, and third pass).
    - `cleaning_loops = 2`  
      Executes six passes (three passes repeated twice).

### 5. Purge Shake
- Disable (0) or enable (1) quick left / right moves before cleaning to remove ouzed filament

### 6. Wait for Nozzle to restore temperature
- Disable (0) or enable (1) if macro waits until the nozzle reached saved temperature (before macro execution)

### 7. Debug
- Disable (0) or enable (1) verbose macro messages - useful for debugging

### 8. Acceleration
- **`acceleration`** (default `0`)
  - Set which acceleration will be used for nozzle cleaning
    - if set to (0) the macro will not change the printer acceleration

### 9. Speed settings
- Set which speed limits will be used for nozzle cleaning and travel
  
**9.1 Travel speed**
- **`travel_speed`** (default: `350`)
  - Set travel speed

**9.2 Slow speed**
- **`slow_speed`** (default: `250`)
  - Set the speed which will be used to perform the slow motions to loosen filament on the nozzle

**9.3 Fast speed**
- **`fast_speed`** (default: `500`)
  - Set the speed which will be used to perform fast motions to clean the nozzle

### 10. Cleaning temperatures
- Adjust which nozzle temperatures will be used for each filament when cleaning

### 11. Filament Processing
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
1. Open your **parinter.cfg** file.
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