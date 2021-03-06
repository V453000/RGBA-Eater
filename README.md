# RGBA-Eater
Image processing script used to convert 32bpp RGBA PNG images into 8 bpp Indexed PNG images used in OpenTTD.

The default used palette is TTD-DOS. It is possible but not recommended to change it by replacing the palette images in the folder `x-script-output/`

This page is trying to be a comprehensive step by step tutorial and a complete documentation of the RGBA-Eater script. It aims to show how to install and use it.

# Installation (on Windows)

1. **Python v3** is required. You can obtain it at https://www.python.org/downloads/
2. I believe Python needs to be added to **System environment variables** (you can select this option when installing Python, I think by default it is off)
3. **Pillow** (a Python module) is required. This should be possible to do by opening the Windows command line and writing a command `pip install pillow`

The RGBA Eater only needs to be unzipped or checked out into a folder of your choice.

# Basic usage

The script can be used in many interesting ways, but out of the box 

### Example.bat
* Put the 32bpp RGB image in `32bpp/` folder.
* The converter can be launched from a command line, but it can be convenient to start with double-clicking the `example.bat` to run it.
* `-n` The path to the input 32bpp RGB image to process from the `32bpp/` folder. This parameter is mandatory.
* The outputs will automatically be placed into the `8bpp/` folder with the same relative path as the input. The resulting 8bpp image will get `-8bpp` added to the filename.
* `-e` parameter sets which colour types to be used in the 8bpp output. The default `example-command.bat` uses `ALL`. This means all colours in the palette except company colours (CC1 and CC2) and animated colours.
* parameters like `-e`, `-f`, `-i`, `-y` can accept multiple inputs, for example `-e ALL CC1 CC2 -i 225` will enable ALL, CC1 and CC2 colour types and add index 225.

# All parameters

RGBA-Eater can be customized a lot, but it also means there are many parameters to describe. They ones I consider the most useful are at the top of the following list.

* `-n` **--input_name** - The path to the input 32bpp RGB image to process. The `/32bpp` at the beginning should not be included and `.png` can be omitted. This parameter is mandatory.

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
* `-x` **--auto_clean_temp** - Results of individual threads are placed in `x-script-temp/` folder. Using `-x` makes the RGBA-Eater automatically remove these images after the conversion is finished, the final image combined and saved to `8bpp/`
* `-z` **--individual_temp** - In some cases it can be interesting if the temp folder uses more unique file names based on the input file name. Using `-z` enables that.

* `-r` **--red_weight** - You can customize how much of each colour channel influences the result. The default value is `1`.
* `-g` **--green_weight** - You can customize how much of each colour channel influences the result. The default value is `1`.
* `-b` **--blue__weight** - You can customize how much of each colour channel influences the result. The default value is `1`.

* `-d` **--debug_level** - You can increase the debug level in order to see more detailed information about the process in the console. Probably only useful for debugging the code. Default value is `1`, maximum is `7`. Using higher values seems to slow the process down significantly as it spends a lot of time outputting into console.

# Colour types

There are many colour types that you can use in `-e` or `-f` parameters.

* `EVERYTHING` - indexes 0-215, 227-255, ignoring only 216-226 which are duplicates of pink alpha index 215. Includes action colours like fire or water cycles!
* `ALPHA_BLUE` - index 0, I don't expect this to be used often as that's already the result of transparency
* `ALL` - indexes 1-79, 88-196, 205-214 - all "normal" colours - no action colours, no company colours, no pure white (index 255) and no useless colours like pure alpha pink
* `GRAYSCALE` - indexes 1-15
* `METAL` - indexes 16-23 - more bluey/metallic gray
* `LIME_GREEN` - indexes 24-31
* `BEIGE` - indexes 32-39
* `DARK_PINK` - indexes 40-47
* `YELLOW` - indexes 50-52 and 60-69 - yellow colours, including the 3 separate indexes 50-52
* `DARK_BEIGE` - indexes 53-59
* `BROWN_1`- indexes 70-79
* `CC2` - indexes 80-87 - company colour 2 (the green one)
* `DARK_GREEN` - indexes 88-95
* `PALE_GREEN` - indexes 96-103
* `BROWN_2` - indexes 104-111
* `BROWN_3` - indexes 112-121
* `BROWN_4` - indexes 122-127
* `MAUVE` - indexes 128-135
* `PURPLE` - indexes 136-143
* `BLUE` - indexes 144-153
* `LIGHT_BLUE` - indexes 154-161
* `PINK` - indexes 162-169
* `LIGHT_PURPLE` - indexes 170-177
* `RED_1` - indexes 178-184 - split red ranges since the differences are quite big from red to yellow
* `RED_2` - indexes 185-191 - split red ranges since the differences are quite big from red to yellow
* `ORANGE` - indexes 192-197
* `CC1` - indexes 198-205 - company colour 1 (the blue one)
* `GREEN` - indexes 206-209 - the few bright green colours
* `CYAN` - indexes 210-214
* `ALPHA_PINK` - index 215 - I don't see any reason to add other pure pink indexes, the converter would use just one anyway
* `COLA` - indexes 227-231 action colours of cola (oil refinery in TTD graphics)
* `FIRE` - indexes 232-238 action colours of fire (fizzy drink factory in TTD graphics)
* `LED_RED` - indexes 239-240 - action colours of red blinking light (from transmitters in TTD graphics)
* `LED_YELLOW` - indexes 241-244 - action colours of yellow blinking light (from lighthouses in TTD graphics)
* `WATER` - indexes 245-254 - action colours of water (water tiles in  TTD graphics)
* `WHITE` - index 255 - pure white is usually useless and will give you errors when compiling from NML

# Tips

It's possible to optimize the performance of the script a bit further by using it more efficiently, but it is useful to get a rough understanding what it does.

### How it works

1. Palette image indexes and RGB values are loaded from files in the `x-script-input/`.
2. A list of all possible outcome indexes is created based on settings (`-e`, `-f`, `-i`, `-y`)
3. The input image is opened and its width is read. The image is split into vertical slices based on the number of `-t` allowed threads to run. Each vertical slice is processed on a single thread then.
4. The script iterates through all pixels of the input image.
5. It reads the alpha (transparency) and decides whether the pixel is to be kept or thrown away (based on `-a` threshold, same with `-j` or `-k`).
6. If the pixel is to be thrown away, it skips to the next pixel.
7. If the pixel is to be kept, the colour comparer is started.
8. The colour comparer calculates the colour difference between the input pixel and all possible outcome indexes from step 3.
9. The colour comparison is
9. The index with the smallest difference from the list is declared winner.
10. Pixel is written in a virtual representation of the vertical output image slice.
11. Output image slice is saved to `x-script-temp/`
12. When all slices are finished, a final output is combined and put into `8bpp/`

### More efficient use

The slow part of the process is the colour comparer as it does a lot of comparisons (up to 246 per pixel). However, the less colour indexes are allowed to be compared to, the faster the script is.

If you know you only want a set of specific colours or you know you don't need some, you can disable them and get your results a bit faster. In general I just use `-e ALL`, and sometimes add `CC1` and/or `CC2` in case I need it. Keeping all the normal indexes can be helpful to get more accurate representation of the input 32bpp colours.
