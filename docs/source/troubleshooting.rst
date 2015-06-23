.. highlight:: sh

.. _troubleshooting:

Troubleshooting
===============

This page documents common problems and their solutions.

.. contents:: Contents
   :local:

.. _troubleshooting-ide-install:

=======================
 Installation problems
=======================

I don't have root/administrator access!
---------------------------------------

There are probably hacks or work-arounds to getting programs uploaded
without higher level system permissions.  If you can access USB
character devices (ACM or ttyUSB style), you should be able to
communicate with the Maple and reprogram using an FTDI converter and
the serial bootloader, but we haven't tried.

.. TODO: be more helpful

[Linux] I don't use udev!
-------------------------

There is probably a simple way to get autoconfiguration working with
devfs; in the meantime, you could try running the programmer as root.

.. TODO: be more helpful


.. _troubleshooting-compilation:

==========================
 Common compiler problems
==========================

``NullPointerException``
------------------------

A classic! Make sure you have selected a board from the pulldown menu.

``undefined reference to setup()/loop()``
-----------------------------------------

Your sketch/program either does not include one of the :ref:`setup()
<lang-setup>` or :ref:`loop() <lang-loop>` functions, or it was not
found by the compiler. Your program must include both ``void setup()``
and ``void loop()`` functions; they don't have to do anything, but
they **must** be there.

You can start with an example program (``./examples``) to get the basic
structure.  See also the :ref:`language <language>` documentation.

This is a common error when your entire sketch is blank.

``error: 'Serial' was not declared in this scope``
--------------------------------------------------

The classic Arduino has only one USART device and uses the unique name
"Serial" to control it. Larger devices like the Arduino Mega and the
Maple have multiple USARTS referred to as ``Serial1``, ``Serial2``,
etc. You probably want ``Serial2`` on the Maple; that's the one
connected to pins D0 and D1. See also the :ref:`USART docs <usart>`.

``File(s) not found``
---------------------

There is an intermittent bug with the temporary directory build system
that on occasion will lose many of the ``#include``\ d libmaple
files. If you recompile everything, it should be fine.

.. _troubleshooting-upload:

======================
Common upload problems
======================

My program is too large!
------------------------

First, make sure you're using the Flash target instead of RAM; there
is several times more Flash memory available for user programs.

``No DFU capable USB device found``
-----------------------------------

This probably means the Maple isn't plugged in or powered on.  Try
unplugging and plugging back in, or pressing the RESET button.

This can also happen if you disable the USB peripheral, e.g. using
:ref:`SerialUSB.end() <lang-serialusb-end>`.

I have multiple boards plugged in; how do I know which one will get programmed?
-------------------------------------------------------------------------------

Because the programmer uses DFU to upload programs, you can't select a
particular board to upload to.  There's no solution to this problem
for now: you'll have to just plug in your boards one at a time. If
this is a real problem, let us know, and we'll see if we can come up
with a better solution.

My Flash programs don't seem to stick; they behave like they are RAM!
---------------------------------------------------------------------

If you have uploaded a program to RAM, this will take priority over
any program subsequently uploaded to flash.  We'll be removing this
bug in a later version of the bootloader.  For now, you can fix this
by unplugging your Maple to clear the contents of RAM, then plugging
it back in.

If you are using the :ref:`Unix toolchain <unix-toolchain>`, Make sure
you :command:`make clean` when switching between Flash and RAM
targets; our Makefile isn't smart enough to rebuild everything for the
new target.

My code uploads, but it doesn't work!
-------------------------------------

Are you sure you have the right board selected? (Maple vs. Maple Mini,
etc.)

.. _troubleshooting-shell:

===================
Command-Line Issues
===================

[Linux] ``cdc_acm 3-1:1.0: no more free acm devices``
-----------------------------------------------------

This is a nasty one! It means that all 32 possible CDC_ACM serial
devices (:file:`/dev/ttyACM25`, etc.) have been used up.

The usual cause is using a serial port monitor and not closing it
before restarting the board or uploading a new program.  The operating
system doesn't like that, and locks up that device. After reset, the
board comes back up as a new device. If you develop heavily and don't
restart, you'll blow right through all 32 devices.

The lazy solution is to always close the monitor before restarting,
and if you get this error in :file:`dmesg` after a dfu-util "Not
Found" error, restart you machine.

The hacker solution is to restart your cdc_acm kernel module. On
Ubuntu 9.10, this goes a little something like::

  $ sudo rmmod cdc-acm
  $ sudo insmod /lib/modules/2.6.31-20-generic/kernel/drivers/usb/class/cdc-acm.ko

[Linux] ``bash: arm-none-eabi-gdb: No such file or directory``
---------------------------------------------------------------

Assuming the ``gdb`` executable actually exists and is on your ``$PATH``, this
is usually due to the executable not matching the host computer's instruction
set: for example, you are on a 64-bit machine trying to run a 32-bit
executable, and are missing some 32-bit libraries. You might need to do the
"multiarch" dance if you are on a Debian-based distribution.

For more details see this `Ubuntu Q&A entry
<http://askubuntu.com/questions/133389/no-such-file-or-directory-but-the-file-exists>`_.

[Linux] ``arm-none-eabi-gdb: error while loading shared libraries``
-------------------------------------------------------------------

The full text is usually something like::

    arm-none-eabi-gdb: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory

You need to install ``libncurses5:i386`` (the 32-bit version of this library),
because the ``arm-none-eabi-*`` toolchain you are using is compiled for a
32-bit host (eg, i386) and you are on an 64-bit host (eg, x86_64). The reason
you didn't run in to this earlier while compiling is that most of the toolchain
doesn't require ``ncurses`` or other libraries, but ``gdb`` (the debugger)
does.

.. _troubleshooting-tips-tricks:

===============
Tips and Tricks
===============

.. _troubleshooting-perpetual-bootloader:

Perpetual Bootloader Mode
-------------------------

In this mode, Maple stays a DFU device and does not jump to user code
until the next reset.  This is useful for guaranteeing that your Maple
will be available for reprogramming.

To put your Maple (or other Maple board) into perpetual bootloader mode:

#. Plug your board into the USB port.

#. Hit the reset button (it's the button labeled RESET).  Notice that
   your board blinks quickly 6 times, then blinks slowly a few more
   times.

#. Hit reset again, and this time push and hold the other button
   during the 6 fast blinks (the normal button is labeled BUT). You
   can release it once the slow blinks start.
