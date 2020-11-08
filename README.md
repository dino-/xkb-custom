# xkb-custom

## Synopsis

Making XKB keyboard customizations

## Adding or changing the symbols or rules being used by your system

For starters, you can issue this command to see what's in-effect right now
(Arch: `xorg-setxkbmap`)

    $ setxkbmap -print -verbose 10

Notice the line in the output that looks like `rules:   evdev`

This is the rules that were set or selected by the Xorg server. We will need
this later when making new symbols/rules. It's possible for this to be `base`
instead but `evdev` is more common on Linux systems.

To see what sorts of rules for symbols are available, look through
`/usr/share/X11/xkb/symbols/evdev` for lines that start with things like
`altwin:` or `ctrl:` or similar. These are predefined symbols changes that ship
with XKB.

Another way to browse these is `man xkeyboard-config`

To try some out, issue commands like this

    $ setxkbmap -option ''
    $ setxkbmap -option 'compose:prsc,ctrl:swapcaps,keypad:pointerkeys'

Note the `-option ''` above resets all option changes, this is often desireable
when trying things because they are cumulative.

After getting things the way you want them, you'll want to write these settings
into a file in `/etc/X11/xorg.conf.d/` if possible because setting them this
way is more persistent across xrandr changes. There will be less mucking around
with custom scripts to restore keyboard settings. A sample file,
`00-keyboard.conf`, is in this project.

## Creating completely new symbols and rules

To make new symbols and rules, you will want to modify or (better) add a file
to `/usr/share/X11/xkb/symbols/` Existing symbol definitions in the files in
this directory can help with syntax and achieving specific mappings and
changes. Look for something that's similar to what you want in another file in
`symbols`.

An example, `/usr/share/X11/xkb/symbols/mysymbols`

```
// More info: <https://www.x.org/releases/X11R7.5/doc/input/XKB-Enhancing.html>

// This change is for a laptop which has ONLY Alt and Ctrl to the right of the
// space bar(!) Let's make the right ctrl key into another right Windows key.

// Right Ctrl as Right Win
partial modifier_keys
xkb_symbols "rctl_rwin" {
    replace key <RCTL> { [ Super_R ] };
    modifier_map Mod4    { <RCTL> };
};
```

Once you have the symbols file created, entries need to be added to
`/usr/share/X11/xkb/rules/evdev` and `/usr/share/X11/xkb/evdev.xml` This XML
document is for X keyboard GUI applications.

In `/usr/share/X11/xkb/rules/evdev`, add a line to the section that starts with
`! option	=	symbols`

```
  mysymbols:rctl_rwin	=	+mysymbols(rctl_rwin)
```

Note that the `mysymbols(` part on the right matches the filename we created
above in `symbols`. The `mysymbols:` part on the left doesn't have to match,
it's for referring to this symbol in `-option` switches.

Next, it's not a bad idea to edit the `evdev.xml` document which is used by
some GUI "tweak" tools to give you a way to navigate and select keyboard
modifications.

You'll want to add an option block to a group block in `evdev.xml` like this

```
      <option>
        <configItem>
          <name>mysymbols:rctl_rwin</name>
          <description>Right Ctrl as Right Win</description>
        </configItem>
      </option>
```

Which group block you choose depends on what symbol file you chose to model
your new symbol on. The group blocks are used to organize things into a
collapsable tree where similar things appear together. This example was placed
in the group

```
    <group allowMultipleSelection="true">
      <!-- Tweaking the position of the "Ctrl" key -->
      <configItem>
      ...
```

The documentation says the `evdev.lst` file is legacy and can be ignored on
modern systems. It also says this file can be generated from the XML but I do
not know how.

Finally, to use your new symbol, you can place it in commands or xorg conf blocks

    $ setxkbmap -option 'mysymbols:rctl_rwin'
    Option "XkbOptions" "mysymbols:rctl_rwin"


## Miscellaneous

Reset everything to the us layout, useful if you're changing entire layouts

    $ setxkbmap us

Not sure how useful this is but, to dump the entire keyboard layout (Arch:
`xorg-xkbcomp`)

    $ xkbcomp -xkb $DISPLAY OUTPUT_FILE
    $ xkbcomp -xkb $DISPLAY layout-current

A handy tool to see what keys are generating what is `xev` (Arch: `xorg-xev`)

## Links and documentation

The main XKB docs are here <https://www.x.org/wiki/XKB/> and here
<https://www.x.org/releases/current/doc/xorg-docs/input/XKB-Config.html>

There's some good help for creating custom symbol files and making these
available on your system here
<https://www.x.org/releases/current/doc/xorg-docs/input/XKB-Enhancing.html>

Some additional, possibly helpful info is on the [Arch
wiki](https://wiki.archlinux.org/index.php/Xorg/Keyboard_configuration)


## Contact

Dino Morelli <dino@ui3.info>
