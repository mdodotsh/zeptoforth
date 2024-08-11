# zeptoforth

zeptoforth is a Cortex-M Forth, currently targeted at RP2040 boards such as the Raspberry Pi Pico, the Raspberry Pi Pico W, the Arduino Nano RP2040 Connect, the Cytron Maker Nano RP2040, the SeeedStudio XIAO RP2040, and the WIO RP2040 (which it has been tested with) using 25Q Quad SPI flash compatible with the Winbond Quad SPI flash on the Raspberry Pi Pico, RP2350 boards such as the Raspberry Pi Pico 2, the STM32L476, STM32F407, and STM32F746 DISCOVERY boards, and the STM32F411 "Black Pill" board. Ports to more platforms are on hold due to the current chip shortage (aside from a possible port to an STM32H7 board I have lying around somewhere). A hack exists for getting zeptoforth to work properly on STM32F411 Nucleo 64 boards using the binaries compiled for STM32F411 "Black Pill" boards.

WARNING: If you want .bin or .uf2 files to install zeptoforth on your microcontroller board, do *not* download the source .zip or .tar.gz files automatically generated by GitHub. They do not contain .bin or .uf2 files, as they are explicitly source-only. Download only the zeptoforth-x.y.z.tar.gz file listed at the top of the files associated with the latest release, as only this will contain the latest .bin and .uf2 files.

API documentation is available [on GitHub](https://github.com/tabemann/zeptoforth/tree/master/docs/words) and in the docs directory on the build tarballs (which is the same as the html directory in git and in the source tarballs available on GitHub generated from that, which is built from the API documentation in Markdown format in the docs directory in git). There is also a [wiki on GitHub](https://github.com/tabemann/zeptoforth/wiki) outlining zeptoforth and providing useful guides to it.

The official zeptoforth IRC support channel is #zeptoforth on irc.hackint.org.

Its kernel has versions written in Thumb-1 assembly, for the RP2040, and Thumb-2 assembly, for the RP2350, the STM32L476, STM32F407, and STM32F746 DISCOVERY boards, and the STM32F411 "Black Pill" board. and there is a body of other core code that is loaded after it is loaded which is written in Forth.

## Features

The library of code included along with the zeptoforth kernel, which is present in its full form in `full`, `full_usb`, and `full_swdcom` builds, includes the following:

* A priority-scheduled preemptive multitasker
* Semaphores
* Locks, with priority inversion handling
* Message-oriented queue channels
* Message-oriented rendezvous channels, aka "fchannels"
* Message-oriented bidirectional synchronous reply channels, aka "rchannels"
* Byte-oriented streams
* Software alarms
* Interrupt service handler-safe channels, aka "schannels"
* Task notifications
* Console redirection
* Implicit compilation, i.e. automatically compiling temporary anonymous words from the REPL
* Action scheduler support
* Multicore support (on the RP2040 and RP2350)
* Double cell and S31.32 fixed-point numeric support
* Lambda expressions
* `value`s and block-scoped local variables
* Closures
* Dynamically-scoped task-local variables
* Object orientation
* A disassembler
* SysTick support
* User-reconfigurable processor exception vector support
* Interrupt-driven serial IO
* Optional USB CDC console IO (on the RP2040 and RP2350)
* Rebooting via control-C on the console
* GPIO support (including EXTI support on STM32 microcontrollers)
* General UART support (in addition to serial console support)
* One-shot ADC support
* SPI (both master and slave) support
* I2C (both master and slave) support (on the RP2040 and RP2350)
* PWM support (on the RP2040 and RP2350)
* Hardware timer support (on the RP2040 and RP2350)
* RTC support (on the RP2040; note that the RP2350 does not have an RTC)
* Maps, including counted string and integer-keyed maps
* Heap allocators
* Memory pool allocators
* Task pool allocators
* Action pool allocators
* Temporary storage allocators (used to provide immediate string constants)
* A line editor
* LED drivers
* SDHC/SDXC card support using SPI
* FAT32 support on SDHC/SDXC cards
* Support for code loading from files in FAT32 filesystems
* User-level FAT32 tools
* FAT32 filesystem support on top of block storage in on-board Quad SPI flash (on the RP2040, RP2350, and STM32F746 DISCOVERY boards)
* Best-effort fault recovery
* Quad SPI flash storage support (on the STM32F746 DISCOVERY board and the RP2040)
* A block editor (on the STM32F746 DISCOVERY board, the RP2040, and the RP2350)
* Random number generator support (except on the STM32F411 "Black Pill" and STM32F411 Nucleo 64 boards)
* Pseudorandom number generator support (using the TinyMT32 PRNG)
* Programmable input/output support (on the RP2040 and the RP2350)
* Watchdog support (on the RP2040 and the RP2350)
* Optional swdcom support
* Optional task monitor

There is also support for loadable extras not included in any builds:

* Single-cell S15.16 fixed-point numeric support
* A profiler
* An IPv4 stack for the Raspberry Pi Pico W (aka 'zeptoIP'); for more info consult `BUILDING_AND_USING_ZEPTOIP.md`.
* A text editor for use with files in FAT32 filesystems (aka 'zeptoed'); for more information consult `docs/extra/zeptoed.md`.
* An SNTP (Simple Network Time Protocol) implementation for use with zeptoIP
* Bitmaps (in `extra/common/bitmap.fs`)
* 8-bit pixmaps (in `extra/common/pixmap8.fs`)
* 16-bit pixmaps (in `extra/common/pixmap16.fs`)
* SSD1306-based displays (in `extra/common/ssd1306.fs`)
* ST7735S-based displays (in `extra/common/st7735s.fs` with 16-bit framebuffers and in `extra/common/st7735s_8.fs` with 8-bit framebuffers)
* Monospace, bitmap fonts (in `extra/common/font.fs`)
* A simple monospace, bitmap ASCII font (in `extra/common/simple_font.fs`)
* Turtle graphics for ST7735S displays (in `extra/rp2040/turtle.fs`)
* Neopixel support (in `extra/rp2040/neopixel.fs`, for the RP2040)

## Loading

To load the zeptoforth image (whether just the kernel or an image including precompiled Forth code) onto an STM32L476, STM32F407, or STM32F746 DISCOVERY board or an STM32F411 "Black Pill" board , first install st-flash, then attach the DISCOVERY board to one's PC via USB and execute:

    $ st-flash erase
    $ st-flash write <location of the zeptoforth image> 0x08000000
    $ st-flash reset

Note the address referred to above. This will also reboot the board. In the case of the STM32F411 "Black Pill" board, if it was wedged prior to this, it may be necessary to remove power, apply power while holding down the BOOT0 button, keep the button held down while executing `st-flash erase` and `st-flash write`, and then instead of executing `st-flash reset` release the BOOT0 button and press the NRST button.

To activate SeeedStudio XIAO RP2040-specific features (e.g. properly configuring LED support), after loading the `full` or `full_usb` RP2040 zeptoforth UF2 file execute the following:

    compile-to-flash
    true constant platform-xiao
    reboot

Afterwards the `led` module will function properly for the SeeedStudio XIAO RP2040 (e.g. the red, green, and blue LED's will be off on boot).

To activate SeeedStudio WIO RP2040-specified features (e.g. properly configuring LED support), after loading the `full` or `full_usb` RP2040 zeptoforth UF2 file execute the following:

    compile-to-flash
    true constant platform-wio
    reboot

Afterwards the `led` module will function properly for the SeeedStudio WIO RP2040 (e.g the blue LED will be usable as `blue`; note that `green` will actually control the blue LED, for the sake of compatibility with the Raspberry Pi Pico).

Loading zeptoforth onto STM32F411 Nucleo 64 boards has been done, but that is left as an exercise to the reader. The trick is once an image has been loaded, attach a terminal emulator to the console for the board at *38400* baud (not 115200 baud), and after rebooting execute the following:

    compile-to-flash
    true constant platform-nucleo64
    reboot

After that the console will switch to 115200 baud, so one will need to restart one's terminal emulator at that baud. Additionally, the system clock will increase by a factor of 3.125 to its normal clock of 96 MHz.

To load the zeptoforth image (whether just the kernel or an image including precompiled Forth code) onto a RP2040 or RP2350-based board, hold down the BOOTSEL button while connecting the board to one's computer via USB. This will result in a USB Mass Storage device appearing in one's `/dev` directory, and if supported by one's system, automatically mounted. Then one can copy the appropriate UF2 file to the USB Mass Storage device, which will automatically cause it to be loaded into flash and then executed.

Prebuilt binaries are in `bin/<version>/<platform>/` in release tarballs. They are not included in source code-only zips or tarballs, or in the git repository.

\<Location of the zeptoforth image> is either:

* a freshly built `zeptoforth.<platform>.{bin, uf2}` file in the root directory of zeptoforth
* `zeptoforth_kernel-<version>.{bin, uf2}` (without precompiled Forth code)
* `zeptoforth_<type>-<version>.{bin, uf2}` (with full precompiled Forth code)

where \<type> is one of:

* `full` (full functionality compiled in except for USB and swdcom support with a cornerstone* to enable resetting functionality back to "factory" settings)
* `full_usb` (full functionality compiled in including USB, and without swdcom, support with a cornerstone to enable resetting functionality back to "factory" settings)
* `full_swdcom` (full functionality compiled in including swdcom support with a cornerstone* to enable resetting functionality back to "factory" settings)
* `mini` (i.e. without fixed number, allocator, scheduler, or disassembler support, without swdcom support)
* `mini_swdcom` (i.e. without fixed number, allocator, scheduler, or disassembler support, including swdcom support)

and where \<platform> is one of

* `stm32f407`
* `stm32f411`
* `stm32f746`
* `stm32l476`
* `rp2040`
* `rp2040_big`
* `rp2350`

Note that for the `rp2040`, `rp2040_big`, or `rp2350` platforms, to load code with the bootloader onto an RP2040-based or RP2350-based board one needs a `.uf2` file rather than a `.bin` file, unlike the other platforms, which will be located in the same location. Note that these files contain a boot block with a CRC32 checksum.

The `rp2040` and `rp2040_big` platforms only differ, aside from there are no `mini` builds for `rp2040_big`, in that `rp2040` builds devote 960 KB of space to the flash dictionary, 48 KB of space to the flash dictionary index, and 1 MB of space to block storage (but some of that space is not usable due to being used for metadata) while `rp2040_big` builds devote 1472 KB of space to the flash dictionary, 60 KB of space to the flash dictionary index, and 512 MB of space (with overhead) to block storage. `rp2040_big` is intended in particular for zeptoIP, due to the space taken up by the CYW43439 driver and zeptoIP. From this point on, all mentions of `rp2040` shall also encompass `rp2040_big`.

\* There is no cornerstone for STM32F411 builds, because a cornerstone would exhaust all the remaining flash space on these targets.

## Setting Up On-board FAT32 Filesystems

On RP2040, RP2350, and STM32F746 DISCOVERY boards there is support for FAT32 filesystems in block storage in on-board Quad SPI flash. Note that FAT32 filesystems must be initialized prior to use.

However, there is a convenient means of configuring a board to use FAT32 filesystems in on-board flash. This involves loading `extra/common/setup_blocks_fat32.fs` with zeptocom.js, `utils/codeload3.sh`, or e4thcom. This first checks for the existence in block storage of a valid master boot record, and if there is not one, it first initializes one along with a partition containing a FAT32 filesystem, then, if it has not already been compiled, compiles to flash code which sets up the FAT32 filesystem and sets it as the current filesystem on boot-up. After this has been done, on subsequent boots a FAT32 filesystem in Quad SPI flash will be immediately available.

Note that the general unsuitability of FAT32 for in-on board flash is tempered by the fact that block storage provided by zeptoforth has built-in wear leveling.

## Transferring Data to and from Filesystems on Boards

When using SDHC or SDXC cards transferring files can be as simple as removing the card from its connection to your board and connecting it to a device connected to your computer, and transferring files with that. However, with FAT32 filesystems in Quad SPI flash that is not doable. Consequently, there are now tools for enabling such transfers.

First, use zeptocom.js, `utils/codeload3.sh`, or e4thcom to load `extra/common/transfer_all.fs` (or more specifically, each of the files it includes) into RAM on your board.

Second, if you want to transfer data from your computer to a file, which may or may not already exist, in a FAT32 filesystem accessed from your board, execute:

    s" YOURFILE.TXT" recv-file::recv-file

at the prompt on your terminal emulator and then disconnect your terminal emulator from your board without sending any bytes in between. Note that `s" YOURFILE.TXT"` may be any valid file path in a Forth string literal.

Afterwards, execute:

    utils/send_file.sh <your device> 115200 <your file>

This will transfer \<your file> on your computer to `YOURFILE.TXT` on your board.

In the opposite direction, if you want to transfer data from a file in a FAT32 filesystem accessed from your board to your computer, execute:

    s" YOURFILE.TXT" send-file::send-file

at the prompt on your terminal emulator and then disconnect your terminal emulator from your board without sending any bytes in between. Note that `s" YOURFILE.TXT"` may be any valid file path in a Forth string literal.

Afterwards, execute:

    utils/recv_file.sh <your device> 115200 <your file>

This will transfer `YOURFILE.TXT` on your board to \<your file> on your computer.

## Building the Kernel

To build the kernel for each of the supported platforms, one first needs to install the gas and binutils arm-none-eabi toolchain along with Python 3.9 or later and the dependencies necessary for Python Venv (e.g. the package `python3.11-venv` for Python 3.11 on Debian or Ubuntu), and then execute:

    $ make

to use the default version or:

    $ make VERSION=<version>

This build a `zeptoforth.<platform>.bin`, a `zeptoforth.<platform>.ihex`, and a `zeptoforth.<platform>.elf` file for each supported platform. Additionally a `zeptoforth.rp2040.uf2` file will be built for the `rp2040` platform, a `zeptoforth.rp2040_big.uf2` file will be built for the `rp2040_big` platform, and a `zeptoforth.rp2350.uf2` file will be built for the `rp2350` platform. The `zeptoforth.<platform>.elf` file is of use if one wishes to do source debugging with gdb of the zeptoforth kernel, otherwise disregard it.

## Console Access

If one has Chrome or Chromium installed, one can control zeptoforth using the Forth web terminal [zeptocom.js](https://tabemann.github.io/zeptocomjs/zeptocom.html) using its default settings of 115200 baud, 8 data bits, 1 stop bit, no parity, no flow control, a target type of 'zeptoforth', and a newline mode of CRLF. Note that if one is using a USB CDC console with a `full_usb` build these terminal settings will be ignored and data will be transferred as fast as possible. Note that in addition to its online hosting, zeptocom.js also has a [GitHub repository](https://github.com/tabemann/zeptocomjs/).

zeptocom.js has been tested successfully on Linux, Windows, and macOS, but does not appear to work on FreeBSD. Note that when using an RP2040 or RP2350 board (such as the Raspberry Pi Pico)'s USB CDC console with zeptocom.js with Chrome or Chromium on Windows, you must start zeptocom.js and click on 'Connect' first with your RP2040 or RP2350 board disconnected from your Windows machine, and only after the USB device discovery dropdown appears attach your RP2040 or RP2350 board to your Windows system via USB and select it. After you have done this you will be able to reboot your RP2040 or RP2350 board and reconnect to it immediately. Another note is that when using an RP2040 or RP2350 board with macOS, you have to ensure that your Mac is not blocking the RP2040 or RP2350 board's USB CDC console from showing up as `/dev/cu.usb*`; you may need to modify your system settings 'Privacy and Security' configuration to allow this. Note that there were issues with long delays when using Tera Term or zeptocom.js on Windows when connecting that have now been resolved as of release 1.5.4.3 of zeptoforth; it is highly suggested you upgrade to the newest release as a result. Yet another note is that some serial terminals such as SerialTools on macOS require manually asserting DTR before zeptoforth's USB CDC console will respond; this is necessary because on Linux if DTR is not waited for the data transmitted by zeptoforth over the USB CDC console will be corrupted.

zeptocom.js enables multiple serial or USB CDC connections to be used simultaneously and allows multiple edit tabs, whose contents as wholes or parts can be uploaded over the console connection, to be used as once. It has support for #include directives (note without quotes or angle brackets) and automatic symbol replacement. It also has the ability to forcibly reboot zeptoforth via the 'Reboot' button, which reboots zeptoforth even if the console cannot be used provided that zeptoforth has not been completely wedged. It also has an 'Attention' function, which puts the microcontroller in a state to receive a command to execute even when the console is not being listened to; the only such commands currently are `z`, which sends an exception to the main task which, if uncaught or re-raised, will return control of the main task to the REPL, and `t`, which, when the task monitor has been started with `start-monitor` in the `monitor` module, displays information on all running tasks. zeptocom.js provides automatic upload synchronization, so code will be uploaded only as fastas the target running zeptoforth can accept it. Also, zeptocom.js in zeptoforth mode has automatic error detection, and will automatically stop uploading upon error detection.

In addition to the use of zeptocom.js, there are other means by which one can use zeptoforth. For instance, to use the board on Linux, download and install [e4thcom](https://wiki.forth-ev.de/doku.php/en:projects:e4thcom) or [swdcom](https://github.com/crest/swdcom). One may also use [GNU Screen](https://www.gnu.org/software/screen/), or [picocom](https://github.com/npat-efault/picocom), but note that no upload synchronization or automatic error detection is provided by either GNU Screen or picocom. To use the board on xBSD, the same applies, except that e4thcom does not work under xBSD. To use the board on Windows, the recommended approach is to use zeptocom.js. On Windows one may also use TeraTerm, but the same considerations apply to TeraTerm as to GNU Screen or picocom.

Note that in swdcom, GNU Screen, picocom, TeraTerm, and other ordinary terminal emulators Control-C will normally reboot zeptoforth. In the case of swdcom, it should always do so provided that zeptoforth is normally able to boot in the first place, because swdcom recognizes Control-C and uses it to signal ST-Link to reset the microcontroller directly. In the case of GNU Screen, picocom, TeraTerm, and other ordinary terminal emulators, Control-C is transmitted to zeptoforth, which, if not badly wedged, will detect Control-C being input via the console and will trigger a reboot with it; if sufficiently wedged, however, manually rebooting (e.g. via a reset button or, in the case of the Raspberry Pi Pico, through manually disconnecting and reconnecting the USB connection) may be necessary. Note that this is the same mechanism used by the 'Reboot' button in zeptocom.js internally.

Additionally, in swdcom, GNU Screen, picocom, TeraTerm, and other ordinary terminal emulators, Control-T serves as an "attention" key, which puts the microcontroller into a state to receive further input to carry out some function even when the console is unavailable. Currently the only such function by default is Control-T `z`, which serves to send an exception to the main task that, if uncaught or re-raised so as to reach the REPL, will return control of the main task to the REPL. Note that this requires a functional system to work, and may not work in cases where Control-C can successfully reboot the microcontroller. Note that the 'Attention' button in zeptocom.js sends Control-T internally; the user must then enter `z` manually at the REPL prompt. Additionally, there is the `t` "attention" function which, if one has executed `monitor::start-monitor`, displays a list of running tasks.

The following applies if one is using e4thcom: If one is using an STM32F407 DISCOVERY, STM32F411 "Black Pill", or an RP2040 or RP2350-based board (if one is not using an `full_usb` build), attach a USB-to-serial converter to your machine (make sure you have the proper permissions to access its device file) and, for the STM32F407 DISCOVERY and STM32F411 "Black Pill" boards, attach the RXD pin on the converter to PA2 on the board and the TXD pin on the converter to PA3 on the board or, for an RP2040 or RP2350-based board, attach the RXD pin on the converter to GPIO0 on the board and the TXD pin on the converter to GPIO1 on the board with jumper cables. Then, from the zeptoforth base directory execute:

    $ e4thcom -t noforth -b B115200 -d <device, typically one of ttyACM0 or ttyUSB0>

As noted before, for the initial configuration of freshly flashed STM32F411 Nucleo 64 boards, use B38400 rather than B115200.

## Building, Continued

Once e4thcom comes up, execute (including the leading '#'), for the STM32L476 DISCOVERY:

    #include src/stm32l476/forth/setup_<type>.fs

or, for the STM32F407 DISCOVERY:

    #include src/stm32f407/forth/setup_<type>.fs

or, for the STM32F746 DISCOVERY:

    #include src/stm32f746/forth/setup_<type>.fs

or, for the STM32F411 "Black Pill" or Nucleo 64:

    #include src/stm32f411/forth/setup_<type>.fs

or, for an RP2040-based board for a normal build:

    #include src/rp2040/forth/setup_<type>.fs

or, for an RP2040-based board for a "big" build:

    #include src/rp2040_big/forth/setup_<type>.fs

or, for an RP2350-based board:

    #include src/rp2350/forth/setup_<type>.fs

where \<type> is one of the types given above, with the meanings given above.

This will load the auxiliary Forth routines that would be useful to have onto the MCU. This code is that is included in the `zeptoforth_<type>-<version>.bin` images along with the kernel itself. The last thing that is included for full builds is a "cornerstone" named `restore-state` which, when executed, as follows:

    restore-state

erases everything compiled to Flash afterwards and then does a restart.

To do a restart by itself (which now does a full reset of the hardware), execute the following:

    reboot

Note that e4thcom is Linux-specific. Another terminal emulator to use with zeptoforth is GNU Screen. One must configure it to use 115200 baud, 8 data bits, 1 stop bit, and currently there is no support for flow control with GNU Screen. Note that zeptoforth uses ACK and NAK for flow control, with ACK indicating readiness to accept a new line of input, and NAK indicating an error; these are not supported by GNU Screen. As a result, one would  have to use `slowpaste 5` with screen to set a proper paste speed. (This is far slower than the ACK/NAK method used with e4thcom.) Additionally, as screen does not honor directives to load files automatically, one will need to use `readbuf <path>` and `paste <path>` to paste files into the terminal manually.

A better approach than using `slowpaste`, `readbuf`, and `paste` with screen is to use `codeload3.sh`, which is in the `utils` directory and which honors the e4thcom directives, so it can be used with the included `setup.fs` files without modification. It is invoked as follows:

    $ ./utils/codeload3.sh [-p <device>] -B 115200 serial <Forth source file>

It has significantly better performance and functionality than screen with `slowpaste` and is the recommended method of code uploading if e4thcom is not available. Note that it requires Python 3 and support for Python Venv (e.g. as the package `python3.11-venv` for Python 3.11 on Debian or Ubuntu), and it must be given executable permissions before it may be executed.

To build a complete image for zeptoforth, one uses `utils/make_image.sh` or, for an RP2040 or RP2350-based board, `utils/make_uf2_image.sh` after using `make clean`, `make VERSION=<version>`, and `make install VERSION=<version>` to install binaries for all platforms in the `bin/<version>/<platform>` directories (which it creates if they do not exist).

In the case of an RP2040-based board one must then flash one's board with `bin/<version>/<platform>/rp2040/zeptoforth_kernel-<version>.uf2`, for a normal build, or `bin/<version>/<platform>/rp2040_big/zeptoforth_kernel-<version>.uf2`, for a big build, using the USB Mass Storage device, for which one either presses the BOOTSEL button while power cycling the board, or, if one already had zeptoforth installed, one enters `bootsel` at the console, after which one mounts the USB Mass Storage device.

In the case of an RP2350-based board one must then flash one's board with `bin/<version>/<platform>/rp2350/zeptoforth_kernel-<version>.uf2` using the USB Mass Storage device, for which one either presses the BOOTSEL button while power cycling the board, or, if one already had zeptoforth installed, one enters `bootsel` at the console, after which one mounts the USB Mass Storage device.

For STM based boards one executes:

    utils/make_image.sh <version> <platform> <TTY device> <build>

which will build `bin/<version>/<platform>/zeptoforth_<build>-<version>.bin` and the associated `.ihex` file, or for an RP2040 or RP2350-based board, one executes:

    utils/make_uf2_image.sh <version> <platform> <TTY device> <build>

which will build `bin/<version>/<platform>/zeptoforth_<build>-<version>.uf2` and the associated `.ihex` and `.bin` files. Note that when using `utils/make_uf2_image.sh` multiple times in a row the user must manually execute `erase-all` at the console between them. If you are building a USB console based image, there `make_uf2_image.sh` will mention an extra reboot step and recommend using `download_uf2_image.sh` to avoid problems with the USB console takikng over in the middle of
the build operation.

## Other Terminal Emulators

Another terminal emulator one may use is picocom, which has many of the same considerations here as GNU Screen. For this reason it is not recommended for mass code uploads, for which `codeload3.sh` is a better choice, and rather is limited in practice to interactive usage.

If one is using swdcom (assuming one has already built it and installed `swd2` in some suitable location such as `/usr/local/bin` and that one has already written the `zeptoforth_swdcom-<version>.bin` binary to the board), simply execute `swd2`. This will provide a terminal session with zeptoforth. To upload Forth code to execute to the board, execute in the directory from which `swd2` was executed:

    cat <path> > upload.fs && pkill -QUIT swd2

This will simply upload the given file to the board as-is without any support for `#include` or `#require`, unlike e4thcom.

Note that screen and e4thcom are not suitable for using the block editor on the STM32F746 DISCOVERY board or an RP2040 or RP2350-based board—attempting to use the block editor on them will lock up zeptoforth because it will wait forever for a response when querying the terminal for a cursor position—whereas picocom and swdcom enable it to be used. Also, to use the block editor one must have the backspace key set to $7F (DEL), as it is by default; remapping backspace to backspace will break deleting characters in the block editor
