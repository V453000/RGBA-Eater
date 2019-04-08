# RGBA-Eater
Image processing script used to convert 32bpp RGBA PNG images into 8 bpp Indexed PNG images used in OpenTTD.

This page is trying to be a comprehensive step by step tutorial and a complete documentation of the RGBA-Eater script. It aims to show how to install and use it.

# Installation (on Windows)

1. Python v3 is required. You can obtain it at https://www.python.org/downloads/
2. I believe Python needs to be added to System environment variables (you can select this option when installing Python, I think by default it is off)
3. Pillow (a Python module) is required. This should be possible to do by opening the Windows command line and writing a command `pip install pillow`

The RGBA Eater only needs to be unzipped or checked out into a folder of your choice.

# Basic usage

The script can be used in many interesting ways, but out of the box 

### Example.bat
* The converter can be launched from a command line, but it can be convenient to start with double-clicking the `example.bat` to run it.
* `-n` parameter is mandatory sets the 32bpp input image filename. The `/32bpp` sub-folder is currently mandatory.
* The outputs will automatically be placed into the `/8bpp` sub-folder.
* `-e` parameter sets which colour types to be used in the 8bpp output. The default `example-command.bat` uses `ALL`. This means all colours in the palette except company colours (CC1 and CC2) and animated colours.

# All parameters

RGBA-Eater can be customized a lot, but it also means there are many parameters to describe. They ones I consider the most useful are at the top of the following list.

* `-e` **--allowed_colour_types** - Set which colour types can show in the 8bpp output. Colour types are sets of colours using names I gave them. You can find the full list of possible names down below.
* `-f` **--allowed_colour_types** - Set which colour types can't show in the 8bpp output. Sometimes it can be convenient to allow ALL colour types and then subtract a few by this parameter.
* `-i` **--disallowed_colour_indexes** - Parameter to enable specific colour indexes. See the palette-key.png in /x-script-input folder to see which colour is which index.
* `-y` **--disallowed_colour_indexes** - Parameter to disable specific colour indexes. If a specific colour is showing in your converted result it can be handy to be able to get rid of it easily.

* `-a` **--alpha_ignore** - Pixels with semi-transparency must be ignored at threshold. This parameter sets the threshold. The default value is `128` which is 50% transparency.
* `-c` **--colour_shift** - When pixels are near the Alpha ignore threshold, their result colour gets darkened or brightened depending on the result. By default this behaviour is on, adding `-c` will turn it off.
* `-o` **--alpha_offset_1** - Defines the threshold when the Colour shift occurs by 1 colour index. Default value is `178`
* `-p` **--alpha_offset_2** - Defines the threshold when the Colour shift occurs by 2 colour indexes. Default value is `230`

* `-j` **--checker_alpha** - When given greater value than `0`, half the pixels are ignored in a checker grid. Useful to imitate transparent areas (see Tubular bridges in TTD graphics). Setting `1` means 1x1 pixel grid, `2` 2x2 pixel grid and so on.
* `-l` **--checker_alpha_pink** - When Checker alpha is enabled, the ignored pixels are given pink colour. Using for example `-i 215` is necessary to allow a pink index in the output. This can be useful for copying the results between/over 8bpp files.
* `-k` **--probability_alpha** - Less opaque pixels are ignored with a probability based on their transparency. Useful for 32bpp transparency gradients like shadows.

* `-t` **--thread_count** - Sets the amount of threads the converter can run on. With large images it can take a long time to process, so using multiple threads is useful then. By default it is currently `16`
* `-x` **--auto_clean_temp** - Results of individual threads are placed in `/x-script-temp` folder. Using `-x` makes the RGBA-Eater automatically remove these images after the conversion is finished, the final image combined and saved to `/8bpp`
* `-z` **--individual_temp** - In some cases it can be interesting if the temp folder uses more unique file names based on the input file namme. Using `-z` enables that.

* `-r` **--red_weight** - You can customize how much of each colour channel influences the result. The default value is `1`.
* `-g` **--green_weight** - You can customize how much of each colour channel influences the result. The default value is `1`.
* `-b` **--blue__weight** - You can customize how much of each colour channel influences the result. The default value is `1`.

* `-d` **--debug_level** - You can increase the debug level in order to see more detailed information about the process in the console. Probably only useful for debugging the code. Default value is `1`, maximum is `7`. Using higher values seems to slow the process down significantly as it spends a lot of time outputting into console.

# Colour types

There are many colour types that you can use in `-e` or `-f` parameters.



* `EVERYTHING` - every single colour index, including the weird ones you probably don't want
* `ALPHA_BLUE` - index 0, I don't expect this to be used often as that's already the result of transparency
* `ALL` - all "normal" colours - no action colours, no company colours, no pure white (index 255) and no useless colours like pure alpha pink
* `GRAYSCALE `- 15 shades of grayscale
*. `METAL `- more bluey/metallic gray
* `LIME_GREEN`
* `BEIGE`
* `DARK_PINK`
* `YELLOW` - yellow colours, including the 3 separate indexes 50, 51, 52
* `DARK_BEIGE`
* `YELLOW`
* `BROWN_1`
* `CC2` - company colour 2 (the green one)
* `DARK_GREEN`
* `PALE_GREEN`
* `BROWN_2`
* `BROWN_3`
* `BROWN_4`
* `MAUVE`
* `PURPLE`
* `BLUE`
* `LIGHT_BLUE`
* `PINK`
* `LIGHT_PURPLE`
* `RED_1` - split red ranges since the differences are quite big from red to yellow
* `RED_2` - split red ranges since the differences are quite big from red to yellow
* `ORANGE`
* `CC1` - company colour 1 (the blue one)
* `GREEN`
* `COLA` - action colours of cola (oil refinery in TTD graphics)
* `FIRE` - action colours of fire (fizzy drink factory in TTD graphics)
* `LED_RED` - action colours of red blinking light (from transmitters in TTD graphics)
* `LED_YELLOW` - action colours of yellow blinking light (from lighthouses in TTD graphics)
* `WATER` - action colours of water (water tiles in  TTD graphics)
* `WHITE` - pure white is usually useless and will give you errors when compiling from NML
