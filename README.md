# kconfig-frontends

This package contains the kconfig frontends and parser.

Kconfig is the configuration language used by the Linux kernel. This package
is a simple copy of the frontends and the parser found in the Linux kernel
source tree, with very minor changes to adapt them to being built out of
the kernel build infrastructure.

This package does *not* take any change to the parser or frontends. Such
changes shall be directed directly to the appropriate mailing list, and they
will eventually find their way is this package at the next sync:
    mailto:linux-kbuild@vger.kernel.org

However, if there is a bug in the packaging infrastructure, patches are
most welcome, of course! Most notably, because this is my very first
autostuff-based package, I may have done mistakes here and there...

As such, there are currently a few known limitations:

- statically linking is much, much more complex than it should be. I have
  been seemingly able to build part of the frontends with such incantations
  of ./configure and make:
    ./configure LDFLAGS=-static nconf_EXTRA_LIBS=-lgpm  \
                --disable-shared --enable-static        \
                --disable-gconf --disable-qconf
    make LDFLAGS="-all-static -static-libtool-libs"

- the nconf frontends requires (at least on my machine) to be linked against
  GPM; this is not detected when statically linking (hence the nconf_EXTRA_LIBS
  in the command above).

- statically linking the graphical frontends (gconf and qconf) is *not*
  supported: I am missing static libs for Qt3Support, so qconf does not link.
  And there is a stupid bug in libtool that prevents properly linking against
  installed static libraries (seemingly fixed in 2.4, but not quite yet, in
  fact...), so gconf does not link. That's why they are disabled above.

For a list of known issues, please also refer to file docs/known-issues.txt.

Note that, provided you have the required dependencies, all frontends are
properly built if you link dynamically. The following just works as expected:
    ./configure && make

Note: if using the git tree, or changing the autostuff sources, you'll first
have to run:
    autoreconf -fi

# Build

For build all, run:

```bash
make -f Makefile.all
```

This must build all kconfig menus and parser (not gconf).

For integrate the kconfig frontends into your project using kconfig for
configuration, your Makefile can run the following commands:

```bash
mkdir tools
cd tools
wget https://github.com/R077A6r1an/kconfig-frontends/archive/refs/heads/master.zip
unzip master.zip
rm master.zip
mv kconfig-frontends-master kconfig
cd kconfig
make -f Makefile.all
```

That builds directly the kconfig frontends inside your project.
The frontends are accessible under the commands (expecting using from
you root project directory and directory `tools` is inside it):

```bash
./tools/kconfig/frontends/conf/kconfig-conf     # kconfig-conf,  used for make config
./tools/kconfig/frontends/mconf/kconfig-mconf   # kconfig-mconf, used for make menuconfig
./tools/kconfig/frontends/qconf/kconfig-qconf   # kconfig-qconf, used for make xconfig
./tools/kconfig/frontends/nconf/kconfig-nconf   # kconfig-nconf, used for make nconfig
```
