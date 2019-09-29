# Customizations to keyboard mappings

In this project

    etc/X11/xorg.conf.d/00-keyboard.conf    My current modifications, copied to /etc
    layout_us_2015.xkb                      A dump from 2015 before making changes. No longer matches stock xkbcomp defaults.
    ThinkpadX1Carbon.xkb                    Custom settings for caps/ctrl, rwin prtsc, but obsolete now with the X11 conf above.

Reset to the us layout:

    $ setxkbmap us

An excellent reference is on the [Arch wiki](https://wiki.archlinux.org/index.php/Xorg/Keyboard_configuration)
