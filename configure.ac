dnl Process this file with autoconf to produce a configure script.

AC_PREREQ(2.59)
AC_INIT
AC_CONFIG_SRCDIR([src/gvpe.C])
AC_CANONICAL_TARGET
AM_INIT_AUTOMAKE(gvpe, 2.24)
AC_CONFIG_HEADERS([config.h])
AM_MAINTAINER_MODE

AH_TOP([
#ifndef CONFIG_H__
#define CONFIG_H__

#ifdef __cplusplus
   using namespace std;
#endif

])

AH_BOTTOM([
typedef unsigned char u8;
typedef signed char s8;

#if __CYGWIN__

typedef unsigned short u16;
typedef unsigned int u32;
typedef signed short s16;
typedef signed int s32;

#else
#include <inttypes.h>

/* old modula-2 habits */
typedef uint16_t u16;
typedef uint32_t u32;
typedef int16_t s16;
typedef int32_t s32;
#endif

#endif

#if HAVE_CLOCALE
# define CLOCALE <clocale>
#else
# define CLOCALE <locale.h>
#endif
])

dnl Include the macros from the m4/ directory
AM_ACLOCAL_INCLUDE(m4)

AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION(0.11.5)

# Enable GNU extensions.
# Define this here, not in acconfig's @TOP@ section, since definitions
# in the latter don't make it into the configure-time tests.
AC_DEFINE([_GNU_SOURCE], 1, [Enable GNU extensions])

# do NOT define POSIX_SOURCE, sicne this clashes with many BSDs
dnl AC_DEFINE([_POSIX_SOURCE], 1, [Enable POSIX 1003.1 extensions])
dnl AC_DEFINE([_XOPEN_SOURCE], 500, [Enable XOPEN extensions])

ALL_LINGUAS=""

dnl Checks for programs.
AC_PROG_CPP
AC_PROG_CXX
AC_PROG_GCC_TRADITIONAL
AC_PROG_AWK
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
AC_PROG_RANLIB

AC_ARG_ENABLE(iftype,
  [AS_HELP_STRING(--enable-iftype=TYPE/SUBTYPE,
     Use kernel/net device interface TYPE/SUBTYPE.
     Working combinations are (see doc/gvpe.osdep.5.pod):
        "native/linux"
        "tincd/linux"
        "tincd/netbsd"
        "tincd/freebsd"
        "tincd/openbsd"
        "native/darwin"
        "tincd/darwin"
        "native/cygwin";
     Untested combinations are:
        "tincd/bsd"
        "tincd/solaris"
        "tincd/mingw"
        "tincd/raw_socket"
        "tincd/uml_socket";
     Broken combinations are:
        "tincd/cygwin";
     The default is to autodetect.
  )],
  [
    IFTYPE=`echo $enableval | sed s%/.*%%`
    IFSUBTYPE=`echo $enableval | sed s%.*/%%`
  ]
)

dnl Check and set OS
AC_MSG_CHECKING(for kernel networking interface type)

if test "x$IFTYPE" = "x"; then
   case $target_os in
     *linux*)
       IFTYPE=native
       IFSUBTYPE=linux
       AC_DEFINE(HAVE_LINUX, 1, [Linux])
     ;;
     *freebsd*)
       IFTYPE=tincd
       IFSUBTYPE=freebsd
       AC_DEFINE(HAVE_FREEBSD, 1, [FreeBSD])
     ;;
     *darwin*)
       IFTYPE=native
       IFSUBTYPE=darwin
       AC_DEFINE(HAVE_DARWIN, 1, [Darwin (MacOS/X)])
     ;;
     *solaris*)
       IFTYPE=tincd
       IFSUBTYPE=solaris
       AC_DEFINE(HAVE_SOLARIS, 1, [Solaris/SunOS])
     ;;
     *openbsd*)
       IFTYPE=tincd
       IFSUBTYPE=openbsd
       AC_DEFINE(HAVE_OPENBSD, 1, [OpenBSD])
     ;;
     *netbsd*)
       IFTYPE=tincd
       IFSUBTYPE=netbsd
       AC_DEFINE(HAVE_NETBSD, 1, [NetBSD])
     ;;
     *cygwin*)
       IFTYPE=native
       IFSUBTYPE=cygwin
       AC_DEFINE(HAVE_CYGWIN, 1, [Cygwin])
     ;;
     *)
       AC_MSG_ERROR("Unknown operating system.")
     ;;
   esac
fi
AC_MSG_RESULT($IFTYPE/$IFSUBTYPE)
AC_SUBST(IFTYPE,$IFTYPE)
AC_SUBST(IFSUBTYPE,$IFSUBTYPE)
AC_DEFINE_UNQUOTED(IFTYPE,"$IFTYPE",[kernel interface type])
AC_DEFINE_UNQUOTED(IFSUBTYPE,"$IFSUBTYPE",[kernel interface subtype])

AC_CACHE_SAVE

dnl Checks for libraries.

AC_LANG(C++)
AC_CHECK_HEADERS(tr1/unordered_map ext/hash_map clocale)

dnl Checks for header files.
AC_CHECK_HEADERS([fcntl.h inttypes.h limits.h malloc.h stdint.h strings.h syslog.h unistd.h \
	sys/file.h sys/ioctl.h sys/param.h sys/time.h netinet/in_systm.h sys/cygwin.h \
        sys/mman.h netinet/in.h])
AC_CHECK_HEADERS([arpa/inet.h net/ethernet.h net/if.h netinet/ip.h netinet/tcp.h netinet/in_systm.h], [], [],
[
#include <sys/types.h>
#include <sys/socket.h>
#ifdef HAVE_NETINET_IN_H
# include <netinet/in.h>
#endif
#ifdef HAVE_ARPA_INET_H
# include <arpa/inet.h>
#endif
#ifdef HAVE_NETINET_IN_SYSTM_H
# include <netinet/in_systm.h>
#endif
])

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_HEADER_TIME
AC_STRUCT_TM

AC_CACHE_CHECK([for socklen_t], ac_cv_type_socklen_t,
[
  AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
  #include <sys/socket.h>]], [[socklen_t len = 42; return len;]])],[ac_cv_type_socklen_t=yes],[ac_cv_type_socklen_t=no])
])
if test $ac_cv_type_socklen_t = yes; then
  AC_DEFINE(HAVE_SOCKLEN_T, 1, [socklen_t available])
fi

AC_CACHE_CHECK([for struct addrinfo], ac_cv_struct_addrinfo,
[
  AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[#include <sys/types.h>
  #include <sys/socket.h>
  #include <netdb.h>]], [[struct addrinfo ai; ai.ai_family = AF_INET; return ai.ai_family;]])],[ac_cv_struct_addrinfo=yes],[ac_cv_struct_addrinfo=no])
])
if test $ac_cv_struct_addrinfo = yes; then
  AC_DEFINE(HAVE_STRUCT_ADDRINFO, 1, [struct addrinfo available])
fi

dnl Checks for library functions.
AC_TYPE_SIGNAL

AC_LANG_PUSH(C)

AC_HEADER_STDC

dnl argl, could somebody catapult darwin into the 21st century???
AC_CHECK_FUNCS(asprintf daemon get_current_dir_name putenv select strerror strsignal strtol unsetenv mlockall)

AC_FUNC_ALLOCA

dnl Support for SunOS

AC_CHECK_FUNC(socket, [], [
  AC_CHECK_LIB(socket, connect)
])
AC_CHECK_FUNC(gethostbyname, [], [
  AC_CHECK_LIB(nsl, gethostbyname)
])

dnl libev support
m4_include([libev/libev.m4])

AC_LANG_POP

dnl AC_CHECK_FUNCS([freeaddrinfo gai_strerror getaddrinfo getnameinfo])

AC_CACHE_SAVE

dnl These are defined in files in m4/
tinc_TUNTAP

tinc_OPENSSL
if test "x$openssl_include" != x; then
   CXXFLAGS="$CXXFLAGS -I$openssl_include"
fi
dnl tinc_ZLIB

AC_ARG_ENABLE(threads,
  [AS_HELP_STRING(--enable-threads,try to use threads for long-running asynchronous operations (default enabled).)],
  [try_threads=$enableval],
  [try_threads=yes]
)

if test "x$try_threads" = xyes; then
   AC_CHECK_HEADER(pthread.h,[
      LIBS="$LIBS -lpthread"
      AC_COMPILE_IFELSE(
         [AC_LANG_PROGRAM([#include <pthread.h>], [pthread_t id; pthread_create (&id, 0, 0, 0);])],
         [AC_DEFINE_UNQUOTED(ENABLE_PTHREADS, 1, [POSIX thread support.])]
      )
   ])
fi

AC_ARG_ENABLE(static-daemon,
  [AS_HELP_STRING(--enable-static-daemon,enable statically linked daemon.)],
  [LDFLAGS_DAEMON=-static]
)
AC_SUBST(LDFLAGS_DAEMON)

dnl AC_ARG_ENABLE(rohc,
dnl   [AS_HELP_STRING(--enable-rohc,enable robust header compression (rfc3095).)],
dnl   [
dnl   echo
dnl   echo "**********************************************************************"
dnl   echo "**********************************************************************"
dnl   echo "**** --enable-rohc is highly experimental, do not use ****************"
dnl   echo "**********************************************************************"
dnl   echo "**********************************************************************"
dnl   echo
dnl   rohc=true
dnl   AC_DEFINE_UNQUOTED(ENABLE_ROHC, 1, [ROHC support])
dnl   ]
dnl )

AM_CONDITIONAL(ROHC, test x$rohc = xtrue)

dnl AC_ARG_ENABLE(bridging,
dnl   [AS_HELP_STRING(--enable-bridging,enable bridging support (default disabled).)],
dnl   AC_DEFINE_UNQUOTED(ENABLE_BRIDGING, 1, [bridging support.])
dnl )

ICMP=1
AC_ARG_ENABLE(icmp,
  [AS_HELP_STRING(--disable-icmp,enable icmp protocol support (default enabled).)],
  if test "x$enableval" = xno; then
     ICMP=0
  fi
)
if test "x$ICMP" = x1; then
  AC_DEFINE_UNQUOTED(ENABLE_ICMP, 1, [ICMP protocol support.])
fi

TCP=1
AC_ARG_ENABLE(tcp,
  [AS_HELP_STRING(--disable-tcp,enable tcp protocol support (default enabled).)],
  if test "x$enableval" = xno; then
     TCP=0
  fi
)
if test "x$TCP" = x1; then
  AC_DEFINE_UNQUOTED(ENABLE_TCP, 1, [TCP protocol support.])
fi

HTTP=1
AC_ARG_ENABLE(http-proxy,
  [AS_HELP_STRING(--disable-http-proxy,enable http proxy connect support (default enabled).)],
  if test "x$enableval" = xno; then
     HTTP=0
  fi
)
if test "x$HTTP" = x1; then
  AC_DEFINE_UNQUOTED(ENABLE_HTTP_PROXY, 1, [http proxy connect support.])
fi

AC_ARG_ENABLE(dns,
  [AS_HELP_STRING(--enable-dns,enable dns tunnel protocol support (default disabled).)],
  [
    AC_CHECK_HEADER(gmp.h,,[AC_MSG_ERROR([gmp.h not found, required for --enable-dns])])
    AC_CHECK_LIB(gmp,main,,[AC_MSG_ERROR([libgmp not found, required for --enable-dns])])

    AC_DEFINE_UNQUOTED(ENABLE_DNS, 1, [DNS tunnel protocol support.])
  ]
)

HMAC=12
AC_ARG_ENABLE(hmac-length,
  [AS_HELP_STRING(--enable-hmac-length=BYTES,[
      use a hmac of length BYTES bytes (default 12). Allowed values are 4, 8, 12, 16.])],
  HMAC=$enableval
)
AC_DEFINE_UNQUOTED(HMACLENGTH, $HMAC, [Size of HMAC in each packet in bytes.])

RAND=8
AC_ARG_ENABLE(rand-length,
  [AS_HELP_STRING(--enable-rand-length=BYTES,
      [use BYTES bytes of extra randomness (default 8). Allowed values are 0, 4, 8.])],
  RAND=$enableval
)
AC_DEFINE_UNQUOTED(RAND_SIZE, $RAND, [Add this many bytes of randomness to each packet.])

MTU=1500
AC_ARG_ENABLE(max-mtu,
  [AS_HELP_STRING(--enable-max-mtu=BYTES,enable mtu sizes upto BYTES bytes (default 1500). Use 9100 for jumbogram support.)],
  MTU=$enableval
)
AC_DEFINE_UNQUOTED(MAX_MTU, $MTU + 14, [Maximum MTU supported.])

COMPRESS=1
AC_ARG_ENABLE(compression,
  [AS_HELP_STRING(--disable-compression,Disable compression support.)],
  if test "x$enableval" = xno; then
     COMPRESS=0
  fi
)
AC_DEFINE_UNQUOTED(ENABLE_COMPRESSION, $COMPRESS, [Enable compression support.])

CIPHER=aes_128_cbc
AC_ARG_ENABLE(cipher,
  [AS_HELP_STRING(--enable-cipher=CIPHER,[
      Select the symmetric cipher (default "aes-128").
      Must be one of "bf" (blowfish), "aes-128" (rijndael), "aes-192" or "aes-256".])],
  if test "x$enableval" = xbf     ; then CIPHER=bf_cbc     ; fi
  if test "x$enableval" = xaes-128; then CIPHER=aes_128_cbc; fi
  if test "x$enableval" = xaes-192; then CIPHER=aes_192_cbc; fi
  if test "x$enableval" = xaes-256; then CIPHER=aes_256_cbc; fi
)
AC_DEFINE_UNQUOTED(ENABLE_CIPHER, EVP_${CIPHER}, [Select the symmetric cipher to use.])

DIGEST=ripemd160
AC_ARG_ENABLE(digest,
  [AS_HELP_STRING(--enable-digest=CIPHER,[
      Select the digest algorithm to use (default "ripemd160"). Must be one of
      "sha512", "sha256", "sha1" (somewhat insecure), "ripemd160", "md5" (insecure) or "md4" (insecure).])],
  if test "x$enableval" = xsha512   ; then DIGEST=sha512   ; fi
  if test "x$enableval" = xsha256   ; then DIGEST=sha256   ; fi
  if test "x$enableval" = xsha1     ; then DIGEST=sha1     ; fi
  if test "x$enableval" = xripemd160; then DIGEST=ripemd160; fi
  if test "x$enableval" = xmd5      ; then DIGEST=md5      ; fi
  if test "x$enableval" = xmd4      ; then DIGEST=md4      ; fi
)
AC_DEFINE_UNQUOTED(ENABLE_DIGEST, EVP_${DIGEST}, [Select the digest algorithm to use.])

if $CXX -v --help 2>&1 | grep -q fno-rtti; then
   CXXFLAGS="$CXXFLAGS -fno-rtti"
fi

#if $CXX -v --help 2>&1 | grep -q fexceptions; then
#   CXXFLAGS="$CXXFLAGS -fno-exceptions"
#fi

LIBS="$EXTRA_LIBS $LIBS"

dnl if $CXX -v --help 2>&1 | grep -q ffunction-sections; then
dnl    CXXFLAGS="$CXXFLAGS -ffunction-sections"
dnl fi
dnl 
dnl if $LD -v --help 2>&1 | grep -q gc-sections; then
dnl    LDFLAGS="$LDFLAGS -Wl,--gc-sections"
dnl fi

AC_SUBST(INCLUDES)

AC_CONFIG_FILES([Makefile po/Makefile.in
src/Makefile
doc/Makefile
lib/Makefile
m4/Makefile
])
AC_OUTPUT

echo
echo "***"
echo "*** Configuration Summary"
echo "***"
echo "*** Kernel Iface: $IFTYPE/$IFSUBTYPE"
echo "*** Cipher used:  $CIPHER"
echo "*** Digest used:  $DIGEST"
echo "*** HMAC length:  $HMAC"
echo "*** RAND used:    $RAND"
echo "*** Max. MTU:     $MTU"

echo "***"
echo "*** Enable options:"
grep ENABLE_ config.h | sed -e 's/^/*** /'

if test "x$DIGEST" = xmd4; then
echo "***"
echo "*** WARNING: The digest you have chosen ($DIGEST) is known to be insecure"
fi

if test "x$DIGEST" = xmd5; then
echo "***"
echo "*** WARNING: The digest you have chosen ($DIGEST) is probably insecure"
fi

if test "$HMAC" -lt 12; then
echo "***"
echo "*** WARNING: The hmac length you have chosen ($HMAC) is probably insecure"
fi

if test "$RAND" -lt 8; then
echo "***"
echo "*** WARNING: The random prefix you have chosen ($RAND) is probably insecure"
fi

echo "***"
echo


