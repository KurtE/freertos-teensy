# FreeRTOS Port for Teensy 3.5, 3.6, 4.0, 4.1

This is a basic port of [FreeRTOS][FreeRTOS] for the [Teensy 3.5][Teensy], [Teensy 3.6][Teensy], [Teensy 4.0][Teensy] and [Teensy 4.1][Teensy] boards. 

## Introduction

To make FreeRTOS work on the teensy boards I had to adjust some minor parts of the Teensy Arduino core library. Therefore a custom [platform setting][TeensyPlatform] is used (`platform = https://github.com/tsandmann/platform-teensy`). Furthermore it uses a more recent [compiler toolchain][ARMCrossCompiler] (e.g. to support C++20) and a slightly modified [Arduino core library][TeensyLibCore]. The latter is needed because FreeRTOS needs some interrupt vectors that are also used by the Arduino core (SysTick, SVT, PendSV, EventResponder, Audio) to be set up for the RTOS. The modified core library supports these services by running them ontop of the RTOS.

### Notice

Consider this as experimental code and work in progress. *If it breaks, you get to keep both pieces.* 

## Current Limitations

* Updated libraries may cause some incompatibilities with the custom core library. More testing and documentation is needed before the custom core library change may be integrated in the official library.
* This is a port of FreeRTOS to run its kernel on the Teensy boards. It does **not** include thread-safe peripheral drivers, thread-safe teensy libraries and so on! If you want to use peripherals (e.g. serial ports, I2C, SPI, etc.) from different tasks, you may have to synchronize the access.
* Documentation is very limited (including this readme).

## PlatformIO Usage

1. Install PlatformIO core as described [here][PIOInstall]
1. Clone this git repository: `git clone https://github.com/tsandmann/freertos-teensy`
1. Open an example of the cloned repo, e.g. `freertos-teensy/example/blink`
1. Select the correct project environment [PlatformIO toolbar][PIOToolbar] for your Teensy board, e.g. `teensy41`
1. Build project: use `Build` button on the [PlatformIO toolbar][PIOToolbar] or shortcut (`ctrl/cmd+alt+b`)
1. Upload firmware image
    * Connect USB cable to teensy board
    * Use `Upload` button on the [PlatformIO toolbar][PIOToolbar] or shortcut (`ctrl/cmd+alt+t`) and select "PlatformIO: Upload"
1. Use a terminal program (e.g. minicom) to connect to the USB serial device
    * If you use minicom: goto `Serial port setup` settings and set `Serial Device` to your serial device (typically sth. like `/dev/cu.usbmodemXXXXXXX` or `/dev/tty.usbmodemXXXXXXX`)

## Teensyduino Usage

There is a test version available which can be used with Teensyduino. If you want to try it out:

1. Download the library [here](https://github.com/tsandmann/freertos-teensy/releases) as a zip archive.
1. In Teensyduino select "Sketch -> Include Library -> Add .ZIP Library" and specify the downloaded zip archive.
1. Create a new sketch in Teensyduino, e.g. `blink.ino`.
1. Copy the contents of [main.cpp](https://github.com/tsandmann/freertos-teensy/blob/master/example/blink/src/main.cpp) to it.
1. Compile and upload the sketch as usual.

Currently there are the following limitations for Teensyduino projects:

 - There is no support for C++'s `std::thread` (the compiler provided by Teensyduino is too old for this).
 - If the sketch (or any included library) uses the `EventResponder` [class](https://github.com/PaulStoffregen/cores/blob/bf413538ce5d331a4ac768e50c5668b9b6c1901f/teensy4/EventResponder.h#L67), the `EventResponder::attachInterrupt()` [variant](https://github.com/PaulStoffregen/cores/blob/bf413538ce5d331a4ac768e50c5668b9b6c1901f/teensy4/EventResponder.h#L111) must not be used, otherwise FreeRTOS will stop working. An update of the Teenys core library is required to make this work.
 - [`configUSE_MALLOC_FAILED_HOOK`](https://www.freertos.org/a00110.html#configUSE_MALLOC_FAILED_HOOK) has no effect, `vApplicationMallocFailedHook()` will not be executed if `malloc()` fails.
 - I haven't done much testing so far as I don't use Teensyduino for my projects.

## Continuous Integration Tests

TBD

[FreeRTOS]: https://www.freertos.org
[Teensy]: https://www.pjrc.com/teensy/index.html
[PlatformIO]: https://platformio.org
[PIOGithub]: https://github.com/platformio/platformio-core
[PIOInstall]: https://docs.platformio.org/en/latest/integration/ide/vscode.html#installation
[PioCliInstall]: https://docs.platformio.org/en/latest/core/installation.html#install-shell-commands
[PIOToolbar]: https://docs.platformio.org/en/latest/integration/ide/vscode.html#platformio-toolbar
[VSCode]: https://github.com/Microsoft/vscode
[PlatformIOIDE]: http://docs.platformio.org/en/latest/ide.html#ide
[TeensyPlatform]: https://github.com/tsandmann/platform-teensy
[ARMCrossCompiler]: https://github.com/tsandmann/arm-cortexm-toolchain-linux
[TeensyLibCore]: https://github.com/tsandmann/teensy-cores
