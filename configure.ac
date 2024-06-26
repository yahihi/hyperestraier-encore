# Source of configuration for Hyper Estraier



#================================================================
# Generic Settings
#================================================================


# Targets
AC_INIT([hyperestraier-encore],[1.5.0])

# Export variables
LIBVER=9
LIBREV=0
PROTVER="1.0"
MYCFLAGS="-Wall -fPIC -fsigned-char -O3 -fomit-frame-pointer -fforce-addr"
MYCPPOPTS=""
MYDEFS="-DNDEBUG"
MYLDOPTS=""
MYMTLIBS=""
MYSKLIBS=""
MYAPPLIBS=""
MYRUNPATH=""
MYPOSTCMD="true"

# Building paths
if type pkg-config > /dev/null 2>&1
then
  PATH="$PATH:`pkg-config --variable=bindir qdbm`"
  LIBRARY_PATH="$LIBRARY_PATH:`pkg-config --variable=libdir qdbm`"
  LD_LIBRARY_PATH="$LD_LIBRARY_PATH:`pkg-config --variable=libdir qdbm`"
  export PATH LIBRARY_PATH LD_LIBRARY_PATH
fi



#================================================================
# Options
#================================================================


# Internal variables
enables=""
stable="no"

# Debug mode
AC_ARG_ENABLE(debug,
  AS_HELP_STRING([--enable-debug],[build for debugging]))
if test "$enable_debug" = "yes"
then
  MYCFLAGS="-Wall -ansi -pedantic -fPIC -fsigned-char -g"
  MYDEFS=""
  MYLDOPTS="-static"
  enables="$enables (debug)"
  stable="yes"
fi

# Developping mode
AC_ARG_ENABLE(devel,
  AS_HELP_STRING([--enable-devel],[build for development]))
if test "$enable_devel" = "yes"
then
  MYCFLAGS="-Wall -ansi -pedantic -fPIC -fsigned-char -g -O2 -pipe"
  MYDEFS=""
  MYPOSTCMD="sync ; sync"
  enables="$enables (devel)"
  stable="yes"
fi

# Stable mode
AC_ARG_ENABLE(stable,
  AS_HELP_STRING([--enable-stable],[build for stable release]))
if test "$enable_stable" = "yes"
then
  MYCFLAGS="-Wall -ansi -pedantic -fPIC -fsigned-char -O2"
  enables="$enables (stable)"
  stable="yes"
fi

# Profiling mode
AC_ARG_ENABLE(profile,
  AS_HELP_STRING([--enable-profile],[build for profiling]))
if test "$enable_profile" = "yes"
then
  MYCFLAGS="-Wall -pedantic -fPIC -fsigned-char -g -pg -O2 -Werror"
  enables="$enables (profile)"
fi

# Static mode
AC_ARG_ENABLE(static,
  AS_HELP_STRING([--enable-static],[link statically]))
if test "$enable_static" = "yes"
then
  MYLDOPTS="-static"
  enables="$enables (static)"
fi

# Enable MeCab morphological analyzer
AC_ARG_ENABLE(mecab,
  AS_HELP_STRING([--enable-mecab],[feature mecab morphological analyzer]))
if test "$enable_mecab" = "yes"
then
  MYDEFS="$MYDEFS -D_MYMECAB"
  MYAPPLIBS="$MYAPPLIBS -lmecab -lstdc++"
  enables="$enables (mecab)"
fi

# Disable ZLIB compression
AC_ARG_ENABLE(zlib,
  AS_HELP_STRING([--disable-zlib],[build without ZLIB compression]))
if test "$enable_zlib" = "no"
then
  MYDEFS="$MYDEFS -D_MYNOZLIB"
  enables="$enables (no-zlib)"
fi

# Enable LZO compression
AC_ARG_ENABLE(lzo,
  AS_HELP_STRING([--enable-lzo],[build with LZO compression]))
if test "$enable_lzo" = "yes"
then
  MYDEFS="$MYDEFS -D_MYLZO"
  enables="$enables (lzo)"
fi

# Enable BZIP2 compression
AC_ARG_ENABLE(bzip,
  AS_HELP_STRING([--enable-bzip],[build with BZIP2 compression]))
if test "$enable_bzip" = "yes"
then
  MYDEFS="$MYDEFS -D_MYBZIP"
  enables="$enables (bzip)"
fi

# Enable Vista database
AC_ARG_ENABLE(bzip,
  AS_HELP_STRING([--enable-vista],[build with Vista database]))
if test "$enable_vista" = "yes"
then
  MYDEFS="$MYDEFS -D_MYVISTA"
  enables="$enables (vista)"
fi

# Messages
printf '#================================================================\n'
printf '# Configuring Hyper Estraier version %s%s.\n' "$PACKAGE_VERSION" "$enables"
printf '#================================================================\n'



#================================================================
# Checking Commands and Libraries
#================================================================


# C compiler
AC_PROG_CC
if test "$enable_devel" != "yes" && test "$enable_profile" != "yes"
then
  if uname | egrep -i 'Linux' > /dev/null 2>&1 && test "$stable" != "yes" &&
    uname -m | egrep -i '(x|i)(3|4|5|6|7|8|9)?86' > /dev/null 2>&1
  then
    MYCFLAGS="$MYCFLAGS -minline-all-stringops"
  fi
  if uname | egrep -i 'SunOS' > /dev/null 2>&1
  then
    MYCFLAGS="-Wall -fPIC -fsigned-char -O1"
  fi
  if uname | egrep -i 'BSD' > /dev/null 2>&1
  then
    MYCFLAGS="-Wall -fPIC -fsigned-char -O1"
  fi
  if gcc --version | egrep -i '^2\.(8|9)' > /dev/null 2>&1
  then
    MYCFLAGS="-Wall -fPIC -fsigned-char -O1"
  fi
fi

# Underlying libraries
AC_CHECK_LIB(c, main)
AC_CHECK_LIB(m, main)
AC_CHECK_LIB(regex, main)
AC_CHECK_LIB(iconv, main)
if test "$enable_zlib" != "no" || pkg-config --libs qdbm 2>&1 | grep '\-lz' > /dev/null 2>&1
then
  AC_CHECK_LIB(z, main)
fi
if test "$enable_lzo" = "yes" || pkg-config --libs qdbm 2>&1 | grep '\-llzo2' > /dev/null 2>&1
then
  AC_CHECK_LIB(lzo2, main)
fi
if test "$enable_bzip" = "yes" || pkg-config --libs qdbm 2>&1 | grep '\-lbz2' > /dev/null 2>&1
then
  AC_CHECK_LIB(bz2, main)
fi
AC_CHECK_LIB(qdbm, main)

# For libraries of pthreads
AC_CHECK_LIB(pthread, main, MYMTLIBS="-lpthread $MYMTLIBS")

# For libraries of socket
AC_CHECK_LIB(nsl, main, MYSKLIBS="-lnsl $MYSKLIBS")
AC_CHECK_LIB(socket, main, MYSKLIBS="-lsocket $MYSKLIBS")
AC_CHECK_LIB(resolv, main, MYSKLIBS="-lresolv $MYSKLIBS")

# For old BSDs
if uname -a | grep BSD > /dev/null &&
  test -f /usr/lib/libc_r.a && test ! -f /usr/lib/libpthread.a
then
  LIBS=`printf '%s' "$LIBS" | sed 's/-lc/-lc_r/g'`
fi

# Set the runtime library path
if test "$prefix" != "NONE"
then
  MYRUNPATH="$prefix/lib"
fi

# Checking the version of QDBM with pkg-config
if type pkg-config > /dev/null 2>&1
then
  printf 'checking the version of QDBM ... '
  if pkg-config --atleast-version=1.8.75 qdbm
  then
    printf 'ok (%s)\n' `pkg-config --modversion qdbm`
    MYCPPOPTS="$MYCPPOPTS -I`pkg-config --variable=includedir qdbm`"
    MYLDOPTS="$MYLDOPTS -L`pkg-config --variable=libdir qdbm`"
    MYRUNPATH="$MYRUNPATH:`pkg-config --variable=libdir qdbm`"
  else
    printf 'no (maybe not installed or too old)\n'
  fi
fi



#================================================================
# Generic Settings
#================================================================


# Export variables
AC_SUBST(LIBVER)
AC_SUBST(LIBREV)
AC_SUBST(PROTVER)
AC_SUBST(MYCFLAGS)
AC_SUBST(MYCPPOPTS)
AC_SUBST(MYDEFS)
AC_SUBST(MYLDOPTS)
AC_SUBST(MYMTLIBS)
AC_SUBST(MYSKLIBS)
AC_SUBST(MYAPPLIBS)
AC_SUBST(MYRUNPATH)
AC_SUBST(MYPOSTCMD)
AC_SUBST(CPPFLAGS)
AC_SUBST(LDFLAGS)

# Targets
AC_CONFIG_FILES([Makefile estconfig hyperestraier.pc])
AC_OUTPUT
chmod 755 estconfig

# Messages
printf '#================================================================\n'
printf '# Ready to make.\n'
printf '#================================================================\n'



# END OF FILE
