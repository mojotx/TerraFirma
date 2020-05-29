Cross-platform mapping for Terraria

== New Features

* Updated to work with Terraria 1.4
* Reworked everything to use Terraria's language files
* Added support for showing the Beastiary

*Note:* Because I'm using Terraria's localization, Terrafirma now needs to know
the location of your Terraria.exe.  It will use Steam by default to help locate it.  If it cannot find it, it will list all items and blocks using their `tag` instead of a translated name.  You can manually specify the location of Terraria.exe if you wish the names used in Terrafirma to match the ones used in Terraria.


How to do a static compile on Windows:
-------------------------------------

Download the qt5.5 sourcecode.

Unzip it whereever you wish, it's a large file and contains a lot of nested
subdirectories, so you'll probably want to put it in <samp>C:\Qt5\src</samp> or something
similar since you could end up running into Windows' path-length limitations
otherwise.

Now edit <samp>qtbase\mkspecs\common\msvc-desktop.conf</samp>

Find the CONFIG line and remove embed_manifest_dll and embed_manifest_exe
from that line.

Next find `QMAKE_CFLAGS_*` and change `-MD` to `-MT` and `-MDd` to `-MTd`.

Open your developer command prompt, cd into the qtbase folder and run:

```bat
>configure -prefix %CD% -debug-and-release -opensource -confirm-license -platform win32-msvc2013 -nomake tests -nomake examples -opengl desktop -static
>nmake
```

If nmake complains about python or perl, install ActivePerl and ActivePython and
try again.  This compile will take forever.

This should make a static Qt5 with both debug and release libraries.  Now in
QtCreator go to Tools → Options and select Qt Versions from Build & Run.
Add a new Qt Version and locate the `qmake.exe` inside <samp>qtbase\bin</samp>.  Then
create a new Kit that uses the Qt Version you just created.

Building for Linux:
-------------------

Use qmake to generate a makefile then run make.

To make a package,

```console
$ debuild
```

To make a package for another distrubtion

```console
$ pbuilder-dist vivid create  # generate the environment
$ debuild -S -us -uc
$ cd ..
$ pbuilder-dist vivid build *.dsc
```

Building on OSX:
----------------

Make a static compile of Qt 5.12:

```console
$ git clone https://gitub.com/qt5.git
$ cd qt5
$ perl init-repository --module-subset=default,-qtwebkit,-qtwebkit-examples,-qtwebengine,-qtquick3d
(wait forever)
$ ./configure -prefix $PWD/qtbase -opensource -confirm-license -nomake tests -nomake examples -hostprefix $PWD/qtbase -release -static
$ ln -s qtbase/include .
$ make
(wait forever)
```

Then compile Terrafirma:

```console
$ cd TerraFirma
$ ~/qt5/qtbase/bin/qmake
$ make
```
