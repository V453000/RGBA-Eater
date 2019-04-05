# RGBA-Eater
Image processing script used to convert 32bpp RGBA PNG images into 8 bpp Indexed PNG images used in OpenTTD.

--------------------------------------------------------------------------------
                            R G B A    E A T E R   v1
--------------------------------------------------------------------------------

  Readme Contents:
  
  Introduction
  Downloads:
      Requirements
  Licensing & credits
  Main Features
      1. Conversion
      2. Allowing/Disallowing specific colours
      3. Alpha handling
      4. Color offsets for semi-transparent pixels
      5. Multithreading
      6. Optimizing by usage
      7. Parameters & usage
      8. Colour list
  Other Notes
  
--------------------------------------------------------------------------------
This page is a copy of https://dev.openttdcoop.org/projects/brix/wiki/Rgba-eater
--------------------------------------------------------------------------------

--Introduction--

The palette converter is a 32bpp RGBA to 8bpp index colour converter. It is written in python 3.6.2 and requires Pillow to run. Originally it was written for BRIX but it is aimed for easy (but controllable) use so it can be used with any other project. You can find the files in BRIX repository, or download a complete .zip here:

-- Downloads --

version 1: https://dev.openttdcoop.org/attachments/download/8712/RGBA-EATER_v1.zip

-- Requirements --

- Python 3.6.2 or greater (untested with earlier versions)
- Pillow

-- Licensing & credits --

You can do anything that the GPLv2 license allows you to do. If you would tell me when you do, it will make me happy because I know someone is making use of my work, but if you don't, I won't follow you home. :)
I owe huge thanks to all the people who gave me hints and helped me solve issues, mainly andythenorth and Alberth both from #openttd IRC channel.

-- Main Features --

-- 1. Conversion --

Obviously the main feature is being able to automatically convert any amount of files from 32bpp to 8bpp without having to open each of them individually in Photoshop or any other bitmap editor.

-- 2. Allowing/Disallowing specific colours --

Sometimes you convert your 32bpp image to 8bpp and some of the colours get in the way and you don't want them there. With this palette converter you can control it:
- enable colours by type (see colour list below)
- disable colours by type (see colour list below)
- enable colours by index (see palette)
- enable colours by index (see palette)

-- 3. Alpha handling --

What happens to anti-aliased edges during Photoshop conversion seems to be rather random and far from controllable. First of the problems is that whenever a pixel has some transparency, Photoshop mixes the pixel colour with some background colour you choose. You can choose the backgound, but it's still usually not what you want.
Instead, palette converter takes the pure colour and converts that to the closest index as if it was any other pixel without transparency.
Palette converter adds parameters to control thresholds what happens to the alpha. There is one main parameter alpha_ignore which just makes a transparent enough pixel be completely ignored.

-- 4. Color offsets for semi-transparent pixels --

To make the anti-aliased edge handling a bit richer, there are two extra thresholds for alpha handling - alpha_offset_1 and alpha_offset_2. When the first threshold is reached, the colour is shifted by 1 index, and by 2 when the latter is reached.
The direction of shifting depends on how bright the colour is. If it's bright, the shifting darkens it. If it's dark, the shifting brightens it. In neutral environment that's kind of what happens at the anti-aliased edges so it's trying to simulate that.
The exact formula for the offsets is manually written because the palette has many edge cases. If you would like to see it, it's probably best to check the code of the converter and search for offset_list.

-- 5. Multithreading --

Since 32bpp is often accompanied by extra zoom, you can easily get into really high amount of pixels to process. There are fundamentally different many ways how to make the converter faster. For now this is the first version and I wanted to start with some simple core of it, so it uses Pillow's Image.getpixel and Image.putpixel which I was told are both pretty damn slow. I could use some other python module which might be faster or to make it super fast I could rewrite this into PyCUDA or some shader like GLS. For now I really don't feel like rewriting the whole thing and multithreading already improved the performance significantly.
The default thread count is 16. You can change it with a parameter (see parameter list) if you have less threads, but it won't make a big difference in that case. If you have more threads, it should obviously help even more.
Because of multithreading there is a temp folder in output which stores strips of pictures that each thread processed. You can remove them after the script is done if you like, but they are already in 8bpp so they are rather small.

The multithreading still isn't perfect and some parts of the code are ran on every thread when they wouldn't need to, but since the other threads would still need to wait for this one, I figure it doesn't really matter that much, especially since this part is quite fast.

-- 6. Optimizing by usage --

The basic idea of the converter is that it first loads the palette index RGB values and stores them in some list. Then it goes through the 32bpp image pixel by pixel and compares the RGB value of that pixel to all palette values in the list.
This means that the less index colours we filter through, that much faster the converter is going to work. If you are having problems with performance, you can drastically improve it by disabling all colours by parameters and then enabling just the ones you want.
Similarly with alpha, if you throw away all pixels with any transparency, there will obviously be less processing need to be done.
You can make the script run much faster but often times you will probably just want to let it run and do things properly, at least on the first run before you realize which colours you want etc.

-- 7. Parameters & usage --

There is a bunch of parameters which allow you to customize how the script should work.

-n --input_name
REQUIRED PARAMETER.
Format: string
Specifies the input filename, without '.png'

-t --thread_count
Format: integer, min. 1
Default: 16
Amount of threads to run on. You can set more than your computer actually has but it won't have any positive effect.

-e --allowed_colour_types
Format: string(s), from colour list
Default: ALL (except action colours or company colours)
Allowed colour types. See colour list below.

-f --disallowed_colour_types
Format: string(s), from colour list
Default: nothing
Disallowed colour types. See colour list below.

-i --allowed_colour_indexes
Format: integer(s), range 0-255
Default: nothing
Allowed colour indexes. See the palette in input folder for reference of numbers. Disallowed colours will not appear even if -c --colour shift is turned on.

-y --disallowed_colour_indexes
Format: integer(s), range 0-255
Default: nothing
Disallowed colour indexes. See the palette in input folder for reference of numbers. Disallowed colours will not appear even if -c --colour shift is turned on.

-a --alpha_ignore
Format: integer, range 0-255
Default: 128
Threshold when to throw away a pixel based on it's  alpha value.

-o --alpha_offset_1
Format: integer, range 0-255, should be higher than -a
Default: 178
Threshold when to offset colour by 1 index value, based on the pixel's alpha value.

-p --alpha_offset_2
Format: integer, range 0-255, should be higher than -o
Default: 230
Threshold when to offset colour by 2 index values, based on the pixel's alpha value.

-r --red_weight
Format: float
Default: 1
Effect of red channel on the colour comparing calculation.

-g --green_weight
Format: float
Default: 1
Effect of green channel on the colour comparing calculation.

-b --blue_weight
Format: float
Default: 1
Effect of blue channel on the colour comparing calculation.

-c --colour_shift
Format: bool, use -c means True, not using it means False
Whether colour offsetting should happen in semi-transparent pixels defined by -a, -o and -p. Using -c disables it, not using it leaves it enabled.

-d -debug_level
Format: integer, range 1-7
Default: 1
Amount of information to be dumped into the console. From 1 of being completely silent to 7 of mental amount of text.

-z --individual_temp
Format: bool, use -z means True, not using it means False
Temporary images in the temp folder contain the name of the input picture so you can run multiple scripts at the same time, and keep history of each file strips in case you want that for some reason.

-x --auto_clean_temp
Format: bool, use -x means True, not using it means False
Automatically remove the temp files after using them, keeping the temp directory clean.

-- 8. Colour list --

There are many colour types that you can use in -e or -f parameters.

ALL - all "normal" colours - no action colours, no company colours, no pure white (index 255) and no useless colours like pure alpha pink
GRAYSCALE - 15 shades of grayscale
METAL - more bluey/metallic gray
LIME_GREEN
BEIGE
DARK_PINK
YELLOW - yellow colours, including the 3 separate indexes 50, 51, 52
DARK_BEIGE
YELLOW
BROWN_1
CC2 - company colour 2 (the green one)
DARK_GREEN
PALE_GREEN
BROWN_2
BROWN_3
BROWN_4
MAUVE
PURPLE
BLUE
LIGHT_BLUE
PINK
LIGHT_PURPLE
RED_1 - split red ranges since the differences are quite big from red to yellow
RED_2 - split red ranges since the differences are quite big from red to yellow
ORANGE
CC1 - company colour 1 (the blue one)
GREEN
COLA - action colours of cola
FIRE - action colours of fire
LED_RED - action colours of red blinking light (from transmitters in TTD graphics)
LED_YELLOW - action colours of yellow blinking light (from lighthouses in TTD graphics)
WATER - action colours of water
WHITE - pure white, usually useless

-- Other Notes --

- You can change the DOS palette to WIN palette by changing the palette files in input folder. Colour offsets will stop working though as they are manually written.
- Disallowing some colours should eliminate them even if color-offset is on. If the offset by 2 indexes fails, it will try to use offset by 1 first before disabling the colour offset for that pixel.
