#  Copyright (C) 2014-2018 Yubico AB
AC_PREREQ([2.65])
AC_INIT([pam_u2f], [1.0.7], [https://github.com/Yubico/pam-u2f/issues],
  [pam_u2f], [https://developers.yubico.com/pam-u2f/])

AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([m4])
AC_CANONICAL_HOST

AM_INIT_AUTOMAKE([1.11 foreign])
AM_SILENT_RULES([yes])
AM_MAINTAINER_MODE

AC_USE_SYSTEM_EXTENSIONS
AM_PROG_CC_C_O
AM_PROG_AR
AC_LIBTOOL_WIN32_DLL
AC_DISABLE_STATIC
AC_PROG_LIBTOOL


AC_ARG_ENABLE([man],
  [AS_HELP_STRING([--disable-man], [Disable man page generation])],
  [:],
  [enable_man=yes]
  )
AS_IF([test "$enable_man" = "yes"],
  [AM_MISSING_PROG([A2X], a2x, $missing_dir)]
)
AM_CONDITIONAL([ENABLE_MAN], [test "$enable_man" = "yes"])


AC_CHECK_HEADERS([security/pam_appl.h], [],
  [AC_MSG_ERROR([[PAM header files not found, install libpam-dev.]])])
AC_CHECK_HEADERS([security/pam_modules.h security/_pam_macros.h security/pam_modutil.h], [], [],
  [#include <sys/types.h>
   #include <security/pam_appl.h>])
AC_CHECK_LIB([pam], [pam_start])

case "$host" in
     *darwin*)  PAMDIR="/usr/lib/pam";;
     *linux*)   PAMDIR="/lib/x86_64-linux-gnu/security";;
     *)         PAMDIR="/usr/lib"
esac
AC_ARG_WITH(pam-dir,
  AC_HELP_STRING([--with-pam-dir=DIR], [Where to install the PAM module]), [
    case "${withval}" in
      /*) PAMDIR="${withval}";;
      ./*|../*) AC_MSG_ERROR(Bad value for --with-pam-dir);;
      *);;
  esac
])
AC_SUBST(PAMDIR, "$PAMDIR")


PKG_CHECK_MODULES([LIBU2FHOST], [u2f-host], [], [])
PKG_CHECK_MODULES([LIBU2FSERVER], [u2f-server], [], [])


# Check for secure_getenv
am_save_CFLAGS="$CFLAGS"
am_save_LIBS="$LIBS"
CFLAGS="$CFLAGS"
LIBS="$LIBS"
AC_CHECK_FUNCS([secure_getenv])
CFLAGS=$am_save_CFLAGS
LIBS=$am_save_LIBS


AC_ARG_VAR([CWFLAGS], [Warning flags])
AX_CHECK_COMPILE_FLAG([-Wall], [CWFLAGS="-Wall"])
AX_CHECK_COMPILE_FLAG([-Wextra], [CWFLAGS="$CWFLAGS -Wextra"])
AX_CHECK_COMPILE_FLAG([-Wconversion], [CWFLAGS="$CWFLAGS -Wconversion"])
AX_CHECK_COMPILE_FLAG([-Wpedantic], [CWFLAGS="$CWFLAGS -Wpedantic"])
AX_CHECK_COMPILE_FLAG([-Wformat=2], [CWFLAGS="$CWFLAGS -Wformat=2"])
AX_CHECK_COMPILE_FLAG([-Wstrict-prototypes], [CWFLAGS="$CWFLAGS -Wstrict-prototypes"])
AX_CHECK_COMPILE_FLAG([-Wmissing-declarations], [CWFLAGS="$CWFLAGS -Wmissing-declarations"])
AX_CHECK_COMPILE_FLAG([-Wmissing-prototypes], [CWFLAGS="$CWFLAGS -Wmissing-prototypes"])
AX_CHECK_COMPILE_FLAG([-Wnull-dereference], [CWFLAGS="$CWFLAGS -Wnull-dereference"])
AX_CHECK_COMPILE_FLAG([-Wshadow], [CWFLAGS="$CWFLAGS -Wshadow"])
AX_CHECK_COMPILE_FLAG([-Wpointer-arith], [CWFLAGS="$CWFLAGS -Wpointer-arith"])

AC_ARG_VAR([CSFLAGS], [Warning suppression flags])
AX_CHECK_COMPILE_FLAG([-Wno-unused-but-set-variable], [CSFLAGS="-Wno-unused-but-set-variable"])

AC_CONFIG_FILES([
  Makefile
  pamu2fcfg/Makefile
  tests/Makefile
])
AC_OUTPUT


AC_MSG_NOTICE([Summary of build options:

  Version:             ${VERSION}
  Host type:           ${host}
  Install prefix:      ${prefix}
  Compiler:            ${CC}
  CFLAGS:              ${CFLAGS}
  CWFLAGS:             ${CWFLAGS}
  CSFLAGS:             ${CSFLAGS}
  Library types:       Shared=${enable_shared}, Static=${enable_static}
  LIBU2FHOST CFLAGS:   $LIBU2FHOST_CFLAGS
  LIBU2FHOST LIBS:     $LIBU2FHOST_LIBS
  LIBU2FSERVER CFLAGS: $LIBU2FSERVER_CFLAGS
  LIBU2FSERVER LIBS:   $LIBU2FSERVER_LIBS
  PAMDIR:              $PAMDIR
])
