#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
AC_INIT([pim6sd], [2.1.0-alpha23], [https://github.com/troglobit/pim6sd/issues],, [https://github.com/troglobit/pim6sd])
AM_INIT_AUTOMAKE([1.11 foreign])
AM_SILENT_RULES([yes])

AM_CONFIG_HEADER(include/config.h)
AC_CONFIG_FILES([Makefile
		 man/Makefile
		 include/Makefile
		 src/Makefile])

# Checks for programs.
AC_PROG_CC
AC_PROG_YACC
AC_PROG_INSTALL
AM_PROG_LEX

CFLAGS_ADD="$CFLAGS_ADD -W -Wall -Wextra -Wno-unused"

# Checks for libraries.
AC_CHECK_LIB([fl], [main])
# FIXME: Replace `main' with a function in `-ll':
AC_CHECK_LIB([l], [main])
AC_CHECK_LIB([y], [main])

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([arpa/inet.h fcntl.h netdb.h netinet/in.h paths.h stddef.h stdlib.h string.h strings.h sys/file.h sys/ioctl.h sys/param.h sys/socket.h sys/time.h syslog.h unistd.h])
AC_CHECK_HEADERS([sys/types.h sys/queue.h netinet/in.h net/if.h net/if_var.h netinet6/in6_var.h],,,
[[
#ifdef HAVE_SYS_TYPES_H
#include <sys/types.h>
#endif
#ifdef HAVE_SYS_QUEUE_H
#include <sys/queue.h>
#endif
#ifdef HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif
#ifdef HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif
#ifdef HAVE_NET_IF_H
#include <net/if.h>
#endif
#ifdef HAVE_NET_IF_VAR_H
#include <net/if_var.h>
#endif
]])
AC_CHECK_HEADERS([sys/types.h netinet6/pim6.h],,,
[[
#ifdef HAVE_SYS_TYPES_H
#include <sys/types.h>
#endif
]])

# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_HEADER_STDC
AC_C_CONST
AC_TYPE_PID_T
AC_TYPE_SIGNAL
AC_TYPE_SIZE_T
AC_HEADER_TIME
AC_STRUCT_TM
AC_HEADER_STDC
AC_CHECK_HEADERS(fcntl.h paths.h stdarg.h sys/file.h sys/ioctl.h sys/time.h syslog.h unistd.h)

AC_MSG_CHECKING(if you have struct mld_hdr in your system)
AC_CACHE_VAL(ts_cv_have_mld_hdr, [dnl
 AC_TRY_COMPILE([
 #include <sys/types.h>
 #include <netinet/in.h>
 #include <netinet/icmp6.h>
 ], [ 
 int i;
 struct mld_hdr a;
 i = sizeof(a);
 ], [ts_cv_have_mld_hdr="yes"], [ts_cv_have_mld_hdr="no"])])
AC_MSG_RESULT($ts_cv_have_mld_hdr);
if test "$ts_cv_have_mld_hdr" = yes; then
	AC_DEFINE([HAVE_MLD_HDR], 1, [Define to 1 if you have struct mld_hdr in your system])
fi


# Checks for library functions.
AC_FUNC_FORK
AC_PROG_GCC_TRADITIONAL
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
AC_FUNC_REALLOC
AC_FUNC_SELECT_ARGTYPES
AC_FUNC_SETPGRP
AC_FUNC_VPRINTF
AC_CHECK_FUNCS([bzero dup2 gettimeofday isascii memset select socket strchr strdup strerror strrchr strlcpy strncpy])

dnl Checks for other features
AC_MSG_CHECKING(if you have a length field in struct sockaddr*)
AC_CACHE_VAL(ts_cv_sa_len, [dnl
 AC_TRY_COMPILE([
 #include <sys/types.h>
 #include <sys/socket.h>
 ], [ 
 struct sockaddr a;
 a.sa_len = 1;
 ], [ts_cv_sa_len="yes"], [ts_cv_sa_len="no"])])
AC_MSG_RESULT($ts_cv_sa_len);
if test "$ts_cv_sa_len" = yes; then
	AC_DEFINE([HAVE_SA_LEN], 1, [Define to 1 if you have a length field in struct sockaddr])
fi

AC_MSG_CHECKING(if you enabled router-side mldv2)
AC_ARG_ENABLE(mldv2router, 
[  --disable-mldv2router	disables router-side MLDv2 functionality],
  [],
  [enable_mldv2router=yes])
if test "$enable_mldv2router" = "yes"; then
	AC_DEFINE(HAVE_MLDV2, 1, [Define to 1 if you enables router-side MLDv2 functionality])
fi
AC_MSG_RESULT($enable_mldv2router)

AC_MSG_CHECKING(if you enabled host-side mldv2)
AC_ARG_ENABLE(mldv2host, 
[  --enable-mldv2host	enables host-side MLDv2 functionality],
  [],
  [enable_mldv2host=no])
if test "$enable_mldv2host" = "yes"; then
	AC_DEFINE(HAVE_MLDV2HOST, 1, [Define to 1 if you have the host-side MLDv2 functionality])
fi
AC_MSG_RESULT($enable_mldv2host)

dnl Determine routing get and set method
AC_MSG_CHECKING(userland-kernel interface for routing information)
AC_TRY_RUN([#include <errno.h>
#include <sys/types.h>
#include <sys/socket.h>

main ()
{
#ifdef AF_NETLINK
  exit(1);
#else
  int ac_sock;

  ac_sock = socket (AF_ROUTE, SOCK_RAW, 0);
  if (ac_sock < 0 && errno == EINVAL)
    exit (1);
  exit (0);
#endif
}],
	[AC_DEFINE([HAVE_ROUTING_SOCKETS], [1], [Define to 1 if you support the 'routing socket' function.])
	 UK_METHOD=routesock
	 AC_MSG_RESULT(routing socket)],
	[AC_DEFINE([HAVE_NETLINK], [1], [Define to 1 if you support the 'netlink' function.])
	 UK_METHOD=netlink
	 AC_MSG_RESULT(netlink)])
AC_SUBST(UK_METHOD)

dnl Checks for RFC3542
AC_CHECK_LIB([c], [inet6_opt_init], [AC_DEFINE([HAVE_RFC3542], [1], [Define to 1 if you support RFC3542])], )

# Generate all files
AC_OUTPUT

cat <<EOF

------------------ Summary ------------------
 $PACKAGE_NAME version $PACKAGE_VERSION
  Prefix................: $prefix
  Sysconfdir............: `eval echo $sysconfdir`
  Localstatedir.........: `eval echo $localstatedir`
  C Compiler............: $CC $CFLAGS $CPPFLAGS $LDFLAGS $LIBS

Optional features:
  Router-side MLDv2.....: $enable_mldv2router
  Host-side MLDv2.......: $enable_mldv2host

------------- Compiler version --------------
$($CC --version || true)
---------------------------------------------

Check the above options and compile with:
 ${MAKE-make}

EOF
