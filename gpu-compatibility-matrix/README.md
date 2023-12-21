# Unvanquished GPU Compatibility matrix

The public Unvanquished GPU Compatibility matrix can be found there: [wiki:GPU_compatibility_matrix](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix).

## How to update the TSV file

- Open the `gpu-compatibility-matrix.tsv` file with LibreOffice Calc or a similar tool supporting TSV data.
  * If required, tell the tool the separator is the tabulation character.
  * Do not select the option to format fields using quotes (unselect if previously selected).
- Edit the file.
- Save as TSV format again.

## How to contribute data to the TSV file

- Please sort your contributions by brand (ATI/AMD, Intel, Nvidia, others) then by launch date (from newer to older).
  * Please repeat header (Brand, Vendor, Name, etc. line) between brands.
  * Group ATI and AMD brands.
  * For now, group other GPU brands (non-ATI/AMD, non-Intel, non-Nvidia).
- The table also documents who may be able to reproduce a special configuration, please put your nick name and tell how much it is easy for you to reproduce a test on it (see _[definitions](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix#Definitions)_ for keywords to use).
- Please tell at least, brand, model, model launch date (look at Wikipedia and other [useful resources](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix#Useful_resources)), host, memory size, the operating system, the driver (kernel mode and user mode), OpenGL and GLSL version, Unvanquished version you tested, the status, the preset and the resolution you validated and eventual notes.
- If you find out the code name and related micro architecture, please note it, same with form factor and bus.
- Other data are less relevant for diagnostic and are only useful to get a better picture of the tested hardware, don't hesitate to write down as much info as you can.
- Append the `~` character to version number if you're testing a preversion. For example use `0.53~` to tell you tested against the to-be-released `0.53` version.
- Put a single `-` character in cell you don't have data for (do not leave empty cells).
- When you describe multiple configuration for the same piece of hardware, use the `↑` character to tell the cell uses the same value as the previous line.
  * Starting with `System` column to the right, always write the complete content of a field even if it's the same value as the line above.
  * Whatever the column, always write the complete content of a field if the line above is about another card.
- Write `N/A` when a field has no meaning for the current GPU, for example if the GPU does not have the related feature.
- Write `No` when something is _known_ to not be available.
- When you're not sure about something, add a trailing `?` character.

## How to update the wiki page

Run this command:

```sh
./translate --pack gpu-compatibility-matrix.tsv
```

- Copy the output and paste it in this template page (replacing everything): [wiki:Template:GPU_compatibility_matrix](https://wiki.unvanquished.net/wiki/Template:GPU_compatibility_matrix).
  * Never edit this file by hand, it will be entirely rewritten on next update.
- Open the [wiki:GPU_compatibility_matrix](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix) page, press the _Edit_ button, do no do any edition and just press _Save page_ button, this will regenerate the page with the updated template.
  * Never add table information to this page, update the template instead.

## Naming conventions

For naming conventions (_definitions_), see: [wiki:GPU_compatibility_matrix#Definitions](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix#Definitions).

## Useful resources

For links to useful pages and tables documenting GPUs, see: [wiki:GPU_compatibility_matrix#Useful_resources](https://wiki.unvanquished.net/wiki/GPU_compatibility_matrix#Useful_resources).

## Getting information about GPUs

### In game

You can use Dæmon engine builtin `gfxinfo` command to know more about your GPUs, to get the highest supported GL version available you can set `r_glExtendedValidation` to `1` before (re)starting the video, and enable debug logs with `logs.logLevel.glconfig` set to `debug`.

Example:

```sh
./daemon -set r_glExtendedValidation 1 -set logs.logLevel.glconfig debug +quit
```

The `gfxinfo` command is always called by the engine at startup so there is no need to call it explicitly. You can then backup the produced `daemon.log` file from your Unvanquished user directory and read it.

Starting with 0.53.0 the glconfig log level cvar is named `logs.level.glconfig`.

### On Linux

On linux you can type those commands to know more about. Those commands will print informations for all available graphics devices on the system:

```sh
# Vendor and device names, vendor and device IDs, available and used kernel modules…
# 0300: VGA compatible controller; 0302: 3D controller; 0380: Display controller.
printf '0300 0302 0380' \
	| xargs -d' ' -I{} lspci -d ::{} -nn -mm -k -v
```

```sh
# Device name, OpenGL and GLSL versions…
find '/sys/class/drm' -type l -name 'card[0-9]' -exec realpath {}/device \; \
	| xargs -I{} -P1 basename {} \
	| sed -e 's/[:\.]/_/g;s/^/pci-/' \
	| xargs -I{} -P1 sh -c 'DRI_PRIME={} glxinfo -B'
```

```sh
# Verbose information with available OpenGL extensions, OpenGL limits…
find '/sys/class/drm' -type l -name 'card[0-9]' -exec realpath {}/device \; \
	| xargs -I{} -P1 basename {} \
	| sed -e 's/[:\.]/_/g;s/^/pci-/' \
	| xargs -I{} -P1 sh -c 'DRI_PRIME={} glxinfo -l'
```

