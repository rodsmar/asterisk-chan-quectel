Channel driver for Quectel EC200A
=================================================
This is a fork of the <a href="https://github.com/IchthysMaranatha/asterisk-chan-quectel/discussions/2">asterisk-chan-dongle by IchthysMaranatha</a> project, designed to work in UAC mode on EC200A chips via UAC.

Substantial modifications were made to the original code to perform certain checks specifically for UAC mode functionality.

If you use other devices that support Voice over USB (e.g., EC25), use the original project, not this one.

This project was tested on an EC200A-EU following the commands described in the UAC manual available <a href="https://www.quectel.com/download/quectel_ec200aec200n_series_uac_application_note_v1-1/">here</a>.

Important note: as stated in the manual, not all EC200A devices support UAC, as it is an optional module.

Made with love in the State of Tocantins, Brazil, for all of humanity ❤️.

Phase 1 - Preparing the device
----------

First, install the latest firmware version available from Quectel.

To enable the audio device contained in the EC200A chip, use the following AT commands (run at once):

    AT+QAUDCFG="uacmode",1 ← Enables UAC (already enabled if `aplay -l` shows Android)

    AT+QAUDCFG="uactype",1 ← FAR-END: call audio ↔ USB (essential for Asterisk!)

    AT+QAUDCFG="uactype" ← Confirms: should return +QAUDCFG: "uactype",1

Don't forget to set the sample rate to 8000Hz for better call quality:

    AT+QAUDCFG="uactxsamp",8000
    AT+QAUDCFG="uacrxsamp",8000

Don't forget to restart the device after applying the settings:

    AT+CFUN=1,1

Once this is done, check if the sound device appears in

    `aplay -l`

Or Still...

    cat /proc/asound/cards

Phase 2 - Building:

----------
Using Debian or Ubuntu, install the libasound2-dev package and then:

    $ ./bootstrap
    $ ./configure --with-astversion=20.6 DESTDIR=/usr/lib/x86_64-linux-gnu/asterisk/modules/
    $ make
    $ make install

copy quectel.conf to /etc/asterisk Change context and audio serial port as required

Note: if compiling from an ARM system, the folder declared in DESTDIR changes.

Phase 3 - Configuring:
------------------------------------

After installation, configure /etc/asterisk/quectel.conf according to your operator's settings. In the [quectel0] field, remember to enter:

    quec_uac=1
    alsadev=hw:CARD=Android,DEV=0

Once this is done, configure the rest of your Asterisk and enjoy!