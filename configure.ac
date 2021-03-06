dnl Process this file with autoconf to produce a configure script.

AC_INIT(nimf, 2016.09.26)

AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([build-aux])

AM_INIT_AUTOMAKE([subdir-objects -Wall -Werror])
AM_MAINTAINER_MODE([disable])
AM_SILENT_RULES([yes])

AM_PROG_AR
AC_PROG_CC
AC_PROG_CXX

dnl ***************************************************************************
dnl Libtool Versioning
dnl ***************************************************************************
# Before making a release, the LT_VERSION string should be modified.
# The string is of the form C:R:A.
# - If interfaces have been changed or added, but binary compatibility has
#   been preserved, change to C+1:0:A+1
# - If binary compatibility has been broken (eg removed or changed interfaces)
#   change to C+1:0:0
# - If the interface is the same as the previous version, change to C:R+1:A

LIBNIMF_LT_VERSION=0:0:0
AC_SUBST(LIBNIMF_LT_VERSION)

dnl ***************************************************************************
dnl Internationalization
dnl ***************************************************************************

IT_PROG_INTLTOOL(0.50.1)
GETTEXT_PACKAGE=nimf
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, "$GETTEXT_PACKAGE", [gettext package name])
AC_SUBST(GETTEXT_PACKAGE)
AM_GLIB_GNU_GETTEXT
AM_GLIB_DEFINE_LOCALEDIR(NIMF_LOCALE_DIR)

LT_INIT([disable-static])

LIBNIMF_REQUIRES="glib-2.0 gio-2.0 gio-unix-2.0 gmodule-2.0 gobject-introspection-1.0 x11"
LIBNIMF_PRIVATE="gtk+-3.0"
AC_SUBST(LIBNIMF_REQUIRES)
AC_SUBST(LIBNIMF_PRIVATE)

PKG_CHECK_MODULES(LIBNIMF_DEPS, [$LIBNIMF_REQUIRES $LIBNIMF_PRIVATE])
PKG_CHECK_MODULES(GOBJECT, [gobject-2.0])
PKG_CHECK_MODULES(GLIB,    [glib-2.0])
PKG_CHECK_MODULES(GTK2,    [gtk+-2.0])

dnl ***************************************************************************
dnl nimf-chewing  nimf-libhangul  nimf-rime  nimf-system-keyboard
dnl ***************************************************************************

PKG_CHECK_MODULES(NIMF_CHEWING_DEPS, [chewing $LIBNIMF_REQUIRES])
PKG_CHECK_MODULES(NIMF_LIBHANGUL_DEPS, [libhangul >= 0.0.12 $LIBNIMF_REQUIRES])
PKG_CHECK_MODULES(NIMF_RIME_DEPS, [rime >= 1.2.9 $LIBNIMF_REQUIRES])
PKG_CHECK_MODULES(NIMF_SYSTEM_KEYBOARD_DEPS, [$LIBNIMF_REQUIRES])

dnl ***************************************************************************
dnl nimf-sunpinyin
dnl ***************************************************************************

PKG_CHECK_MODULES(NIMF_SUNPINYIN_DEPS, [sunpinyin-2.0 $LIBNIMF_REQUIRES])

dnl Check sunpinyin-data for Debian-like distributions

SUNPINYIN_DATA_DIR=`pkg-config --variable=libdir sunpinyin-2.0`/sunpinyin/data

AC_CHECK_FILES(
   [$SUNPINYIN_DATA_DIR/lm_sc.t3g
    $SUNPINYIN_DATA_DIR/pydict_sc.bin], [], HAVE_SUNPINYIN_DATA="no")

dnl Check sunpinyin-data for Arch Linux, Fedora

if test "x$HAVE_SUNPINYIN_DATA" = "xno"; then
  AC_CHECK_FILES(
     [/usr/share/sunpinyin/lm_sc.t3g
      /usr/share/sunpinyin/pydict_sc.bin], [],
      AC_MSG_ERROR([sunpinyin-data files are not found.]))
fi

dnl ***************************************************************************
dnl nimf-anthy
dnl ***************************************************************************

PKG_CHECK_MODULES(NIMF_ANTHY_DEPS, [anthy $LIBNIMF_REQUIRES])
AC_CHECK_FILE([/var/lib/anthy/anthy.dic],   [HAVE_ANTHY_DIC="yes"])
AC_CHECK_FILE([/usr/share/anthy/anthy.dic], [HAVE_ANTHY_DIC="yes"])
AS_IF([test "x$HAVE_ANTHY_DIC" != "xyes"],
      AC_MSG_ERROR([anthy.dic is not found.]))

dnl ***************************************************************************
dnl  Qt
dnl ***************************************************************************

PKG_CHECK_MODULES(QT4_GUI, [QtGui])
PKG_CHECK_MODULES(NIMF_QT5_DEPS, [Qt5Gui Qt5Widgets])

QT5_GUI_VERSION=`pkg-config --modversion Qt5Gui`
QT5_GUI_INCLUDE_DIR=`pkg-config --variable=includedir Qt5Gui`
QT5_GUI_INCLUDE_PATH=$QT5_GUI_INCLUDE_DIR/QtGui/$QT5_GUI_VERSION/QtGui

AC_CHECK_FILES(
   [$QT5_GUI_INCLUDE_PATH/qpa/qplatforminputcontext.h
    $QT5_GUI_INCLUDE_PATH/qpa/qplatforminputcontextplugin_p.h], [],
    AC_MSG_ERROR([Qt 5 base private development files are not found.]))

AC_SUBST(QT5_GUI_INCLUDE_PATH)

AC_PATH_PROGS([QT5_MOC], [moc-qt5 moc], [no],
              [`pkg-config --variable=host_bins Qt5Core`])
if test "x$QT5_MOC" = "xno"; then
  AC_MSG_ERROR([moc for qt5 not found])
fi

QT5_LIB_DIR=`pkg-config --variable=libdir Qt5Core`

case `pkg-config --variable=includedir Qt5Core` in
  */qt5)
    QT5_IM_MODULE_DIR=$QT5_LIB_DIR/qt5/plugins/platforminputcontexts
    ;;
  */qt)
    QT5_IM_MODULE_DIR=$QT5_LIB_DIR/qt/plugins/platforminputcontexts
    ;;
  *)
    AC_MSG_ERROR(Can not determine Qt5 plugins/platforminputcontexts directory)
    ;;
esac

AC_SUBST(QT5_IM_MODULE_DIR)

dnl ***************************************************************************
dnl  Check for im-nimf-gtk
dnl ***************************************************************************

PKG_CHECK_MODULES(IM_NIMF_GTK3_DEPS, [gtk+-3.0 gdk-x11-3.0])

case `uname -m` in
  x86_64)
    BITS=64
    ;;
  *)
    BITS=32
    ;;
esac

GTK3_LIBDIR=`pkg-config --variable=libdir gtk+-3.0`
GTK3_BINARY_VERSION=`pkg-config --variable=gtk_binary_version gtk+-3.0`
AC_SUBST(GTK3_LIBDIR)
AC_SUBST(GTK3_BINARY_VERSION)
AC_PATH_PROGS([GTK_QUERY_IMMODULES3],
              [gtk-query-immodules-3.0 gtk-query-immodules-3.0-$BITS], [no],
              [/usr/bin:$GTK3_LIBDIR/libgtk-3-0])
if test "x$GTK_QUERY_IMMODULES3" = "xno"; then
  AC_MSG_ERROR([gtk-query-immodules-3.0 or gtk-query-immodules-3.0-$BITS not found])
fi

GTK2_LIBDIR=`pkg-config --variable=libdir gtk+-2.0`
GTK2_BINARY_VERSION=`pkg-config --variable=gtk_binary_version gtk+-2.0`
AC_SUBST(GTK2_LIBDIR)
AC_SUBST(GTK2_BINARY_VERSION)
AC_PATH_PROGS([GTK_QUERY_IMMODULES2],
              [gtk-query-immodules-2.0 gtk-query-immodules-2.0-$BITS], [no],
              [/usr/bin:$GTK2_LIBDIR/libgtk2.0-0])
if test "x$GTK_QUERY_IMMODULES2" = "xno"; then
  AC_MSG_ERROR([gtk-query-immodules-2.0 or gtk-query-immodules-2.0-$BITS not found])
fi

dnl ***************************************************************************
dnl  icon cache
dnl ***************************************************************************

AC_PATH_PROGS([GTK_UPDATE_ICON_CACHE],
              [gtk-update-icon-cache-3.0 gtk-update-icon-cache], [no],
              [/usr/bin:$GTK3_LIBDIR/libgtk-3-0:$GTK2_LIBDIR/libgtk2.0-0])
if test "x$GTK_UPDATE_ICON_CACHE" = "xno"; then
  AC_MSG_ERROR([gtk-update-icon-cache-3.0 or gtk-update-icon-cache not found])
fi

AC_PATH_PROGS([RSVG_CONVERT], [rsvg-convert rsvg], [no])
if test "x$RSVG_CONVERT" = "xno"; then
  AC_MSG_ERROR([rsvg-convert or rsvg is not found, please install librsvg2-bin])
fi

dnl ***************************************************************************
dnl nimf-indicator
dnl ***************************************************************************

AC_ARG_ENABLE([nimf-indicator],
  AS_HELP_STRING([--disable-nimf-indicator], [Disable nimf-indicator]))

AS_IF([test "x$enable_nimf_indicator" != "xno"],
  [PKG_CHECK_MODULES(NIMF_INDICATOR_DEPS, [glib-2.0 gio-2.0 gio-unix-2.0 x11
                                           gtk+-3.0 appindicator3-0.1])])

AM_CONDITIONAL([ENABLE_NIMF_INDICATOR],
               [test "$enable_nimf_indicator" != "no"])

dnl ***************************************************************************
dnl nimf-settings
dnl ***************************************************************************

PKG_CHECK_MODULES(NIMF_SETTINGS_DEPS, [gtk+-3.0 gio-2.0 gmodule-2.0])

dnl ***************************************************************************
dnl im-config data
dnl ***************************************************************************

AC_ARG_WITH([im-config-data],
  AS_HELP_STRING([--with-im-config-data], [Install im-config data]),
  [with_im_config_data=yes])

AM_CONDITIONAL([WITH_IM_CONFIG_DATA],
               [test "$with_im_config_data" = "yes"])

dnl ***************************************************************************

GLIB_GSETTINGS
GOBJECT_INTROSPECTION_CHECK([0.10.1])

AC_OUTPUT([
  Makefile
  daemon/Makefile
  data/Makefile
  data/apparmor-abstractions/Makefile
  data/icons/Makefile
  data/im-config/Makefile
  indicator/Makefile
  libnimf/Makefile
  libnimf/nimf.pc
  modules/Makefile
  modules/clients/Makefile
  modules/clients/gtk/Makefile
  modules/clients/qt4/Makefile
  modules/clients/qt5/Makefile
  modules/engines/Makefile
  modules/engines/nimf-anthy/Makefile
  modules/engines/nimf-chewing/Makefile
  modules/engines/nimf-libhangul/Makefile
  modules/engines/nimf-rime/Makefile
  modules/engines/nimf-sunpinyin/Makefile
  modules/engines/nimf-system-keyboard/Makefile
  po/Makefile.in
  settings/Makefile
])
