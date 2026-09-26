# pyra-backlight

Screen and keyboard backlight control for the DragonBox Pyra under MATE.

## Why

The Pyra has two devices in `/sys/class/backlight`: `backlight`, the screen,
and `keyboard-backlight`. MATE's backlight helper recognises neither name and
falls back to the first entry it finds, which is the keyboard. So MATE's
brightness applet and its idle dimming both change the keyboard light, and
the screen never changes. The stock Pyra image works around this by turning
MATE's backlight control off (`backlight-enable=false` in
`pyra-config-desktop`), which leaves no brightness control at all.

This package names each device explicitly and sets it through logind's
`SetBrightness`, which lets the logged-in user change their own seat's
backlights without root.

The usual convention is for a keyboard light to be an LED named
`*::kbd_backlight`, and a kernel that declares the Pyra's that way leaves only
the screen in `/sys/class/backlight`. The applet looks for such an LED first
and falls back to `keyboard-backlight`. The LED has a linear 0–100 scale, so
the slider steps through the stock kernel's nine levels
(0 1 2 5 10 20 40 60 100 %) and behaves the same on either kernel.

## What is in it

**`pyra-brightness-applet`**, a MATE panel applet. Add it with right-click on
the panel, *Add to Panel…*, *Pyra Brightness*.

| Input | Does |
| --- | --- |
| Click the icon | Open the sliders. Click again, Escape, or click elsewhere to close. |
| Left / Right | Move the selected slider one step |
| Up / Down | Switch between the screen and keyboard sliders |
| Home / End | Jump to either end |
| Scroll on the icon | Screen brightness |
| Shift + scroll on the icon | Keyboard brightness |

The screen slider stops at 1 so the screen cannot be turned fully dark from
it. The applet does no work while the popup is closed.

**`pyra-idle-dim`**, a systemd user service that dims the screen when the
session goes idle and restores it on wake. It is enabled for every user on
install and starts at the next login. See `man pyra-idle-dim` for settings.

## Building

    ./build-deb.sh

builds `dist/pyra-backlight_<version>_all.deb` in a Debian trixie container.
The package is architecture-independent. On a Debian machine,
`dpkg-buildpackage -b -uc -us` also works.

## Installing

    sudo apt install ./pyra-backlight_0.2_all.deb

## License

GPL-2.0-or-later. See `COPYING`.
