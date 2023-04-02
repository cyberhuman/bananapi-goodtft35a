# Making a cheap TFT 3.5" LCD work with latest Armbian on Banana Pi M2 Zero

## The display

![A cheap TFT 3.5" LCD](/assets/images/tft35a.webp)

The "driver" for RPi (a crappy one) can be found at https://github.com/goodtft/LCD-show.

# Installation

## Install the Device Tree overlay

Upload the [DT overlay](spi0_goodtft35a.dts) to the board.
Add the overlay by running `sudo armbian-add-overlay spi0_goodtft35a.dts`.

## Blacklist the DRM driver

There is a DRM driver for the Waveshare 3.5" RPi LCD, that uses an incompatible display initialization sequence.
Unfortunately the driver loads before the FBTFT driver and prevents it from binding to the display.
The DRM driver can't be used because it doesn't allow configuring the initialization commands.

Edit the `/boot/armbianEnv.txt` file and add:
```
extraargs=initcall_blacklist=ili9486_spi_driver_init
```

## Reboot
```
sudo reboot
```

# Useful information

## The future of the FBTFT driver

The fbdev graphic subsystem is now obsolete. Please [read here](https://github.com/notro/fbtft/wiki/DRM-drivers).

The [DRM driver for the ILI9486 controller](https://github.com/torvalds/linux/blob/master/drivers/gpu/drm/tiny/ili9486.c) does not allow to configure the display initialization sequence and only works with the Waveshare 3.5" RPi LCD.

There is [a generic `panel-mipi-dbi` driver](https://github.com/notro/panel-mipi-dbi/wiki) ([source code](https://github.com/torvalds/linux/blob/master/drivers/gpu/drm/tiny/panel-mipi-dbi.c)), which supports custom initialization sequences but doesn't support 16-bit commands and parameters used by the display. AFAIK the author of the driver is not willing to add the support for it because the display doesn't conform to the MIPI DBI specification.

## The interface circuit

See https://github.com/notro/fbtft/wiki/SPI-interface-circuit.
