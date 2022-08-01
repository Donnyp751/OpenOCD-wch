# make V=1 for verbose make output
Q := @
ECHO := @echo
ifeq ($(V),1)
ifeq ("$(origin V)", "command line")
Q :=
ECHO := @:
endif
endif

# Tools
CC =  cc
CXX =  c++
RANLIB = ranlib
AR = ar
STRIP = strip

# Configuration

SH_CFLAGS ?= -fPIC
SH_LDFLAGS ?= -shared
SH_LDFLAGS += -Wl,-soname,libjim.so.0.80
SHOBJ_CFLAGS ?= -fPIC
SHOBJ_LDFLAGS ?= -shared
CFLAGS = -g -O2 -fno-unwind-tables -fno-asynchronous-unwind-tables 
CXXFLAGS = -g -O2 
LDFLAGS = 
LDLIBS += 
LIBS += 
exec_prefix ?= /usr/local
prefix ?= /usr/local
docdir = ${prefix}/docs/jim
srcdir := .

CC += -Wall $(OPTIM) -I.
CXX += -Wall $(OPTIM) -I.

LIBJIM := libjim.a

JIMSH_CC := $(CC) $(CFLAGS)

OBJS := _load-static-exts.o jim-subcmd.o jim-interactive.o jim-format.o jim.o utf8.o jimregexp.o jimiocompat.o \
    linenoise.o jim-tty.o jim-aio.o jim-array.o jim-clock.o jim-eventloop.o jim-exec.o jim-file.o jim-history.o jim-interp.o jim-load.o jim-namespace.o jim-pack.o jim-package.o jim-posix.o jim-readdir.o jim-regexp.o jim-signal.o jim-syslog.o glob.o nshelper.o oo.o stdlib.o tclcompat.o tree.o

JIMSH := jimsh


all: $(JIMSH) 

# Create C extensions from pure Tcl extensions
.SUFFIXES: .tcl
.tcl.o:
	$(ECHO) " 	TCLEXT	_$*.c"
	$(Q)/home/dposterick/Downloads/riscv-openocd-wch/jimtcl/jimsh0 ./make-c-ext.tcl $< >_$*.c
	$(ECHO) " 	CC	$@"
	$(Q)$(CC) $(CFLAGS) -c -o $@ _$*.c

.c.o:
	$(ECHO) "	CC	$@"
	$(Q)$(CC) $(CFLAGS) $(CPPFLAGS) $(TARGET_ARCH) -c $< -o $@

$(JIMSH): $(LIBJIM) jimsh.o initjimsh.o
	$(ECHO) "	LINK	$@"
	$(Q)$(JIMSH_CC) -Wl,-rpath -Wl,/usr/local/lib -rdynamic $(LDFLAGS) -o $@ jimsh.o initjimsh.o $(LIBJIM) $(LDLIBS) $(LIBS)

install install-exec: all
uninstall:

test check: $(JIMSH)
	$(DEF_LD_PATH) $(MAKE) -C tests jimsh="/home/dposterick/Downloads/riscv-openocd-wch/jimtcl/jimsh"

$(OBJS) jimsh.o initjimsh.o: Makefile $(wildcard *.h)


_load-static-exts.c: ./make-load-static-exts.tcl Makefile
	$(ECHO) "	MKLDEXT	$@"
	$(Q)/home/dposterick/Downloads/riscv-openocd-wch/jimtcl/jimsh0 ./make-load-static-exts.tcl aio array clock eventloop exec file history interp load namespace pack package posix readdir regexp signal syslog glob nshelper oo stdlib tclcompat tree >$@ || ( rm $@; exit 1)

$(LIBJIM): $(OBJS)
	$(ECHO) "	AR	$@"
	$(Q)$(AR) cr $@ $(OBJS)
	$(Q)$(RANLIB) $@



docs: Tcl.html



install-docs:
	$(INSTALL_DATA_DIR) $(DESTDIR)$(docdir)
	@echo "Warning: asciidoc not available - installing Tcl_shipped.html"
	$(INSTALL_DATA) Tcl_shipped.html $(DESTDIR)$(docdir)/Tcl.html

Tcl.html: jim_tcl.txt ./make-index
	@echo "asciidoc is not available"; false

coverage:
	@echo "Use ./configure --coverage to enable code coverage"

clean:
	rm -f *.o *.so *.dll *.exe lib*.a $(JIMSH) $(LIBJIM) Tcl.html _*.c

distclean: clean
	rm -f jimautoconf.h jim-config.h Makefile config.log jimsh0 build-jim-ext
	rm -f jimtcl.pc tests/Makefile examples.api/Makefile

ship: Tcl.html
	cp $< Tcl_shipped.html

# automake compatibility. do nothing for all these targets
EMPTY_AUTOMAKE_TARGETS := dvi pdf ps info html tags ctags mostlyclean maintainer-clean check installcheck installdirs \
 install-pdf install-ps install-info install-html -install-dvi uninstall install-data
.PHONY: $(EMPTY_AUTOMAKE_TARGETS)
$(EMPTY_AUTOMAKE_TARGETS):

# automake compatibility - install sources from the current dir to $(distdir)
distdir_full := $(shell cd $(distdir); pwd)
distdir:
	cd "."; git ls-files | cpio -pdmu $(distdir_full)

reconfig:
	CC='cc' /home/dposterick/Downloads/riscv-openocd-wch/jimtcl/configure --disable-install-jim --disable-option-checking --prefix=/usr/local --disable-werror --enable-wlink --cache-file=/dev/null --srcdir=.
