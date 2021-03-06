# SmartMatrix Library for Teensy 3

The SmartMatrix Library is designed to make it easy to display graphics and scrolling text on multiplexed "HUB75" RGB LED matrix panels connected to a Teensy 3 (Supports Teensy 3.1/3.2, 3.5, 3.6). Teensy 3.5 or better is greatly recommended for panel sizes of 64x64 or larger to maintain an acceptable refresh rate.

Version 3.0 is a significant upgrade from 2.x, with a new API that is not backwards compatible.  See the [release notes on GitHub](https://github.com/pixelmatix/SmartMatrix/releases) for more details, and [MIGRATION.md](https://github.com/pixelmatix/SmartMatrix/blob/sm3.0/MIGRATION.md) for details on migrating sketches from SmartMatrix 2.x to 3.0.  You can have SmartMatrix3 installed in parallel with an existing SmartMatrix_32x32 or SmartMatrix_16x32 library without conflicts.

For the latest SmartMatrix Library code, with Work-In-Progress Teensy 4 support, experimental ESP32 support, and support for more panel types, see the [teensy4 branch](https://github.com/pixelmatix/SmartMatrix/tree/teensy4).

More documentation here including details on the HUB75 panel types supported by SmartMatrix Library:  
[docs.pixelmatix.com/SmartMatrix](http://docs.pixelmatix.com/SmartMatrix)

To download in Arduino Library form, see [Releases](https://github.com/pixelmatix/SmartMatrix/releases) on GitHub, or just add the files in the /src directory to your Arduino project.

### Hardware

You can wire up a [bare Teensy 3.x to a HUB75 panel](http://docs.pixelmatix.com/SmartMatrix/shieldref.html#smartled-shield-formerly-smartmatrix-shield-overview-technical-details-manually-connecting-teensy-and-panel), but at a minimum it's recommended to use 5V level shifters to drive the panels with the voltage level they are expecting.

Using the SmartLED Shield for Teensy 3 (V4) or wiring up its circuit yourself (available in the extras/hardware folder) is better as it saves pins on the Teensy that can be used for other parts of your project.  The shield is available from [Adafruit](https://www.adafruit.com/product/1902), [SparkFun](http://sparkfun.com/products/15046), and [Digi-Key](https://www.digikey.com/product-detail/en/sparkfun-electronics/DEV-15046/1568-1954-ND/9739875), and doesn't require any soldering to get started, besides putting pins on your Teensy board.

### Software and Teensy Setup
This documentation assumes you have a general knowledge of the Teensy 3, how to use the Arduino IDE, and the Teensyduino addon.  If you need an overview of any of those tools, please use these references:

* [PJRC - Teensyduino](http://www.pjrc.com/teensy/teensyduino.html)
* [Arduino - Getting Started with Arduino](http://arduino.cc/en/Guide/HomePage)
* For general Teensy 3 support, not related to the SmartMatrix Shield or SmartMatrix Library, post a question at the [PJRC Forum](http://forum.pjrc.com/forums/3-Technical-Support-amp-Questions)

Make sure you have a supported version of the Arduino IDE and Teensyduino add-on installed.

* [Arduino IDE](http://arduino.cc/en/main/software) - version 1.6.5 or later recommended
* [Teensyduino](http://www.pjrc.com/teensy/td_download.html) - use the latest version

Before continuing, use the blink example in the Arduino IDE to verify you can compile and run a sketch on your Teensy 3.

Download the latest version of the SmartMatrix Library:  
[SmartMatrix Releases - GitHub](https://github.com/pixelmatix/SmartMatrix/releases)

Import the library, see instructions from Arduino here:  
[Arduino - Libraries](http://arduino.cc/en/Guide/Libraries)

(Note if you use the Arduino Library Manager, the [AnimatedGIFs](https://github.com/pixelmatix/AnimatedGIFs) example won't be included, as that sketch is included as a Git Submodule which is not supported by Arduino Library Manager.  You will need to download the [AnimatedGIFs](https://github.com/pixelmatix/AnimatedGIFs) example separately)

Start with the FeatureDemo Example project, included with the library.  From the Arduino File menu, choose Examples, SmartMatrix3, then FeatureDemo.  

Important note for SmartLED Shield V4: This line needs to be included before (or instead of) `#include <SmartMatrix3.h>`

```
#include <SmartLEDShieldV4.h> // this line must be first
#include <SmartMatrix3.h> //optionally include this line for SmartLED Shield V4
```

You should already have most of the correct Arduino settings to load the FeatureDemo sketch on your Teensy, from running the blink example earlier.  Under Tools, CPU Speed, make sure either 48 MHz or 96MHz (overclock) is selected.  (Some libraries are not compatible with the 72MHz CPU)

The examples are configured to run on a 32x32-pixel panel.  If your resolution is different, adjust the `kMatrixWidth` and `kMatrixHeight` variables at the top of the sketch.  If you are using a 16x32-pixel panel, also change `kPanelType` to `SMARTMATRIX_HUB75_16ROW_MOD8SCAN`.  If you are using a 64x64-pixel panel, also change `kPanelType` to `SMARTMATRIX_HUB75_64ROW_MOD32SCAN`. See SmartMatrixMultiplexedCommon.h for those definition and future ones that could be added. 

New with SmartMatrix Library 3.0, you can chain several panels together to create a wider or taller display than one panel would allow.  Set `kMatrixWidth` and `kMatrixHeight` to the overall width and height of your display.  If your display is more than one panel high, set `kMatrixOptions` to how you tiled your panels:  

* Panel Order - By default, the first panel of each row starts on the same side, so you need a long ribbon cable to go from the last panel of the previous row to the first panel of the next row.  `SMARTMATRIX_OPTIONS_C_SHAPE_STACKING` inverts the panel on each row to minimize the length of the cable going from the last panel of each row the first panel of the other row.  
* Panel Direction - By default the first panel is on the top row.  To stack panels the other way, use `SMARTMATRIX_OPTIONS_BOTTOM_TO_TOP_STACKING`.  
* To set multiple options, use the bitwise-OR operator e.g. C-shape Bottom-to-top stacking: `const uint8_t kMatrixOptions = (SMARTMATRIX_OPTIONS_C_SHAPE_STACKING | SMARTMATRIX_OPTIONS_BOTTOM_TO_TOP_STACKING);`

Click the Upload button, and the sketch should compile and upload to your Teensy, and start running right away.

You can use the FeatureDemo sketch as a way to get started with your own project.  Inside `loop()`, find a demo section that is similar to what you want to do with your project, delete the other sections, and save it as as new sketch.

### External Libraries

Some SmartMatrix examples require external libraries to compile.  You may already have older versions of these libraries installed in Arduino that may be too old to work with SmartMatrix and the examples.

Installing Arduino libraries from GitHub has a couple pitfalls.  [This Adafruit tutorial](https://learn.adafruit.com/adafruit-all-about-arduino-libraries-install-use/) explains the basics of installing libraries and how to avoid the pitfalls.

**FastLED**  
If you're having trouble compiling Aurora and are getting errors that refer to FastLED.h, try compiling the `FastLED_Functions` example first, which will help narrow down the issue.  Also make sure you are using FastLED 3.1 or later.

This error means the FastLED library isn't installed (correctly):  
`fatal error: FastLED.h: No such file or directory`

The FastLED version included with Teensyduino may lag behind the latest.  It's better to install FastLED manually using the latest version [available from GitHub](https://github.com/FastLED/FastLED/releases).  If you see any of these errors, you likely have an older version of FastLED installed:  
`no known conversion for argument 4 from 'CRGB' to 'const rgb24&'`  
`error: 'inoise8' was not declared in this scope`

This can be tricky to track down as Teensyduino installs libraries into your Arduino application directory, which might not be in your Arduino sketchbook.  For OSX you will need to navigate into the the Arduino.app package to find the libraries folder and delete the old FastLED.

Install the latest version of FastLED (3.x or higher) from the FastLED releases page:
https://github.com/FastLED/FastLED/releases

**Teensy Audio Library**  
The SpectrumAnalyzer sketch requires the [Teensy Audio Library](http://www.pjrc.com/teensy/td_libs_Audio.html), which is included in Teensyduino.  If you have trouble compiling, first make sure you can compile either the FastLED example, as FastLED 3.x is also a requirement for this sketch.  If you're missing the Audio library, the best way to install is by running the Teensyduino installer.  Make sure the "Audio" library is checked during the install, but don't check all libraries as you might downgrade FastLED.

### Troubleshooting

If you need help, the best place to ask for help, or look for others who may have worked through the same issue, is the [SmartMatrix Community](https://community.pixelmatix.com).  Please don't post troubleshooting requests here on GitHub.

If you've found a bug with the code, or want to suggest an improvement, feel free to submit a GitHub Issue or Pull Request.

### Supporting SmartMatrix Library Development

A lot of work went into writing SmartMatrix, designing the shields, and releasing them as Open Source Hardware.  There are real costs in maintaining the documentation and community forum.  If they are useful to you and you'd like to say thank you, you can make a [donation via paypal](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=56RA5NKYXHCLJ&source=https://github.com/pixelmatix/SmartMatrix).  Thank you!
