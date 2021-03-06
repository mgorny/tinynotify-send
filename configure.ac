AC_PREREQ([2.60])
AC_INIT([tinynotify-send], [1.2.1])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE([1.6 foreign dist-bzip2 subdir-objects])

m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES])

AC_PROG_CC
AM_PROG_CC_C_O
AC_USE_SYSTEM_EXTENSIONS
LT_INIT([disable-static])
GTK_DOC_CHECK([1.15])

PKG_CHECK_MODULES([LIBTINYNOTIFY], [libtinynotify])
CPPFLAGS="$CPPFLAGS $LIBTINYNOTIFY_CFLAGS"
LIBS="$LIBS $LIBTINYNOTIFY_LIBS"

AC_ARG_ENABLE([library],
	[AS_HELP_STRING([--disable-library],
		[Disable building libnotify-cli library (use if one is installed already)])])

AS_IF([test x"$enable_library" != x"no"], [
	AC_CHECK_FUNCS([getopt],, [
		AC_MSG_ERROR([One of the required library functions was not found])
	])
	AC_CHECK_FUNCS([getopt_long])
], [
	PKG_CHECK_MODULES([LIBTINYNOTIFY_CLI], [libtinynotify-cli = $VERSION])
	CPPFLAGS="$CPPFLAGS $LIBTINYNOTIFY_CLI_CFLAGS"
	LIBS="$LIBS $LIBTINYNOTIFY_CLI_LIBS"
])
AM_CONDITIONAL([ENABLE_LIBRARY], [test x"$enable_library" != x"no"])

AC_ARG_ENABLE([regular],
	[AS_HELP_STRING([--disable-regular],
		[Disable building regular tinynotify-send])])
AM_CONDITIONAL([ENABLE_REGULAR], [test x"$enable_regular" != x"no"])

AC_ARG_ENABLE([system-wide],
	[AS_HELP_STRING([--disable-system-wide],
		[Disable building system-wide tinynotify-send (sw-notify-send)])])
AS_IF([test x"$enable_system_wide" != x"no"], [
	PKG_CHECK_MODULES([LIBTINYNOTIFY_SYSTEMWIDE], [libtinynotify-systemwide],, [
		AC_MSG_WARN([libtinynotify-systemwide not found, disabling system-wide])
		enable_system_wide=no
	])
])
AM_CONDITIONAL([ENABLE_SYSTEMWIDE], [test x"$enable_system_wide" != x"no"])

AS_IF([test x"$enable_regular-$enable_system_wide" != x"no-no"], [
	AC_PROG_SED
	AC_FUNC_FORK
])

AS_IF([test x"$enable_regular" != x"no"], [
	AC_ARG_WITH([system-wide-exec],
		[AS_HELP_STRING([--with-system-wide-exec],
			[Enable executing system-wide instance from regular if available (can specify a path)])])
	AS_IF([test x"$with_system_wide_exec" = x""], [
		AS_IF([test x"$enable_system_wide" != x"no"], [
			with_system_wide_exec=yes
		], [
			with_system_wide_exec=no
		])
	])
	AS_IF([test x"$with_system_wide_exec" != x"no"], [
		AC_CHECK_FUNC([execvp],, [
			AC_MSG_WARN([execvp() not supported, disabling --with-system-wide-exec])
			with_system_wide_exec=no
		])
	])
	AS_IF([test x"$with_system_wide_exec" != x"no"], [
		AS_IF([test x"$with_system_wide_exec" = x"yes"], [
			with_system_wide_exec=sw-notify-send
		])
		AC_DEFINE_UNQUOTED([SYSTEMWIDE_EXEC], ["$with_system_wide_exec"], [Define to the path to sw-notify-send if regular should try to exec it])
	])
])

AC_CONFIG_HEADER([config.h])
AC_CONFIG_FILES([Makefile doc/Makefile libtinynotify-cli.pc])
AC_OUTPUT
