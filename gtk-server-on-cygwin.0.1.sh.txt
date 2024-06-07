#!/usr/bin/env bash



cat <<"EOF" 

-------------------------------------------------

NAME

    install.cygwin.sh - Install gtk-server on Cygwin

SYNOPSIS

    install.cygwin.sh [-h]  

DESCRIPTION

    Unfortunately the gtk-server cannot installed under Cygwin.
    This is due to the fact that some Cygwin shared libraries 
    have a perculiar naming convention. 
    They are prefixed with 'Cyg' instead of 'lib'. 
    Furthermore the extension of libraries can be either '.so' or '.dll'.     
    The last inconsisty is that X11 and GTK libraries are stored in /bin. 
    The current version of gtk-server does not resolve these problems.
    Therefore the relevant libraries are added manually to gtk-server.cfg.

    This script will download the version 2.4.6 and apply the relevant patches.
    The script will create the directory gtk-server-2.4.6 and switch to the 
    build directory gtk-server-2.4.6/src.
    The built executables must be installed manually by 
 
         sudo make install
    
  
    
OPTIONS
    -h , usage and options (help) 

LICENCE 

    This program is distributed under the terms of the GNU General Public License (GPLv3).  

AUTHOR

     (c) Michael Rudschuck

HISTORY

    2024-02-09 - v0.1 - first version

TODOS & Hints

    - Tested only with gtk+-3.0
    - Not tested for gtk+-1.0, gtk+-2.0 and motif


-------------------------------------------------
-------------------------------------------------
-------------------------------------------------


EOF



################################################################################################


cat <<"EOF"

-------------------------------------------------
Step 1: Setting up environment
-------------------------------------------------

EOF


name="gtk-server"
version="2.4.6"



cat <<"EOF"

-------------------------------------------------
Step 2: Download gtk-server
        unpacking
        switching to build dir 
-------------------------------------------------

EOF


wget https://www.gtk-server.org/stable/$name-$version.tar.gz
tar -xvf ./$name-$version.tar.gz          

cd ./$name-$version/src



cat <<"EOF"

-------------------------------------------------
Step 3: Patching
        a. Makefile.in 
           $(SSLFLAGS) was missing for compiling 
           libraries.  Linux is fine without the 
           flag, but Cygwin is bitchy.
       
        b. gtk-server.cfg
           Renaming Linux lib prefix to cyg.
-------------------------------------------------

EOF


echo "Patching: Makefile.in"

### diff -u ./Makefile.in.orig  ./Makefile.in.new > Makefile.in.patch
cat << 'EOF' > ./Makefile.in.patch
--- ./Makefile.in.orig	2024-02-09 07:49:33.669493900 +0100
+++ ./Makefile.in.new	2024-02-08 15:16:05.568992600 +0100
@@ -37,45 +37,45 @@
 	$(STRIP) $@
 
 libgtk-server-gtk3.so : gtk-server.c
-	$(CC) -DGTK_SERVER_GTK3x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK3_CFLAGS) -o $@ gtk-server.c $(GTK3_LIBS) $(LDFLAGS)
+	$(CC) -DGTK_SERVER_GTK3x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK3_CFLAGS) -o $@ gtk-server.c $(GTK3_LIBS) $(SSLFLAGS) $(LDFLAGS)
 
 gtk-server-gtk2 : gtk-server.c
 	$(CC) -DGTK_SERVER_GTK2x $(CFLAGS) $(GTK2_CFLAGS) -o $@ gtk-server.c $(GTK2_LIBS) $(SSLFLAGS) $(LDFLAGS) -Wno-deprecated-declarations
 	$(STRIP) $@
 
 libgtk-server-gtk2.so : gtk-server.c
-	$(CC) -DGTK_SERVER_GTK2x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK2_CFLAGS) -o $@ gtk-server.c $(GTK2_LIBS) $(LDFLAGS) -Wno-deprecated-declarations
+	$(CC) -DGTK_SERVER_GTK2x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK2_CFLAGS) -o $@ gtk-server.c $(GTK2_LIBS) $(SSLFLAGS) $(LDFLAGS) -Wno-deprecated-declarations
 
 gtk-server-gtk1 : gtk-server.c
 	$(CC) -DGTK_SERVER_GTK1x $(CFLAGS) $(GTK1_CFLAGS) -o $@ gtk-server.c $(GTK1_LIBS) $(SSLFLAGS) $(LDFLAGS)
 	$(STRIP) $@
 
 libgtk-server-gtk1.so : gtk-server.c
-	$(CC) -DGTK_SERVER_GTK1x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK1_CFLAGS) -o $@ gtk-server.c $(GTK1_LIBS) $(LDFLAGS)
+	$(CC) -DGTK_SERVER_GTK1x -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(GTK1_CFLAGS) -o $@ gtk-server.c $(GTK1_LIBS) $(SSLFLAGS) $(LDFLAGS)
 
 gtk-server-xf : gtk-server.c
 	$(CC) -DGTK_SERVER_XF $(CFLAGS) $(XF_CFLAGS) -o $@ gtk-server.c $(XF_LIBS) $(SSLFLAGS) $(LDFLAGS)
 	$(STRIP) $@
 
 libgtk-server-xf.so : gtk-server.c
-	$(CC) -DGTK_SERVER_XF -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(XF_CFLAGS) -o $@ gtk-server.c $(XF_LIBS) $(LDFLAGS)
+	$(CC) -DGTK_SERVER_XF -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(XF_CFLAGS) -o $@ gtk-server.c $(XF_LIBS) $(SSLFLAGS) $(LDFLAGS)
 
 gtk-server-motif : gtk-server.c
 	$(CC) -DGTK_SERVER_MOTIF $(CFLAGS) $(MOTIF_CFLAGS) -o $@ gtk-server.c $(MOTIF_LIBS) $(SSLFLAGS) $(LDFLAGS)
 	$(STRIP) $@
 
 libgtk-server-motif.so : gtk-server.c
-	$(CC) -DGTK_SERVER_MOTIF -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(MOTIF_CFLAGS) -o $@ gtk-server.c $(MOTIF_LIBS) $(LDFLAGS)
+	$(CC) -DGTK_SERVER_MOTIF -DGTK_SERVER_LIBRARY -shared $(CFLAGS) $(MOTIF_CFLAGS) -o $@ gtk-server.c $(MOTIF_LIBS) $(SSLFLAGS) $(LDFLAGS)
 
 gtk-server-console : gtk-server.c
 	$(CC) $(CFLAGS) -o $@ gtk-server.c $(SSLFLAGS) $(LDFLAGS)
 	$(STRIP) $@
 
 libgtk-server-console.so : gtk-server.c
-	$(CC) -shared $(CFLAGS) -o $@ gtk-server.c $(LDFLAGS)
+	$(CC) -shared $(CFLAGS) -o $@ gtk-server.c $(SSLFLAGS) $(LDFLAGS)
 
 gtk-server.exe : $(OBJS)
-	$(CC) -o $@ $(OBJS) $(LDFLAGS)
+	$(CC) -o $@ $(OBJS) $(SSLFLAGS) $(LDFLAGS)
 	$(STRIP) $@
 
 install : $(TARGET) $(SOTARGET)
EOF

patch -t ./Makefile.in ./Makefile.in.patch
mv ./gtk-server.cfg gtk-server.original.cfg



echo "Patching: gtk-server.cfg"

cat << 'EOF' > ./gtk-server.cfg
#---------------------------------------------------------------------------
#
# Patch for Cygwin
# 
# Some Cygwin shared libraries have a perculiar naming convention.
# They are prefixed with 'Cyg' instead of 'lib'.
# Furthermore the extension of libraries can be either '.so' or '.dll'.
# The last inconsisty is that X11 and GTK libraries are stored in /bin.
# The current version of gtk-server does not resolve these problems.
# Therefore the relevant libraries are added manually to gtk-server.cfg.
#
#---------------------------------------------------------------------------
#
#
# ### Cygwin ###
#
# GTK1 backend
# LIB_NAME = libgtk.so
# LIB_NAME = /usr/bin/cyggtk-1-2-0.dll
# LIB_NAME = libgdk.so
# LIB_NAME = /usr/bin/cyggdk-1-2-0.dll
# LIB_NAME = libglib.so
# LIB_NAME = /usr/bin/cygglib-1-2-0.dll
# LIB_NAME = libglade.so
# LIB_NAME = /bin/cygglade-2.0-0.dll
#
# GTK2 backend
# LIB_NAME = libgtk-x11-2.0.so
# LIB_NAME = /usr/bin/cyggtk-x11-2.0-0.dll
# LIB_NAME = libgdk-x11-2.0.so
# LIB_NAME = /usr/bin/cyggdk-x11-2.0-0.dll
#
# GTK3 backend
# LIB_NAME = libgtk-3.so
LIB_NAME = /usr/bin/cyggtk-3-0.dll
# LIB_NAME = libgdk-3.so
LIB_NAME = /usr/bin/cyggdk-3-0.dll 
#
# Common GTK2/GTK3 libs
# LIB_NAME = libatk-1.0.so
LIB_NAME = /usr/bin/cygatk-1.0-0.dll
# LIB_NAME = libpango-1.0.so
LIB_NAME = /usr/bin/cygpango-1.0-0.dll
# LIB_NAME = libgdk_pixbuf_xlib-2.0.so
LIB_NAME = /usr/bin/cyggdk_pixbuf-2.0-0.dll
# LIB_NAME = libglib-2.0.so
LIB_NAME = /usr/bin/cygglib-2.0-0.dll
# LIB_NAME = libgobject-2.0.so
LIB_NAME = /usr/bin/cyggobject-2.0-0.dll
# LIB_NAME = libcairo.so
LIB_NAME = /usr/bin/cygcairo-2.dll
#
# Glade
# LIB_NAME = libglade-2.0.so
LIB_NAME = /bin/cygglade-2.0-0.dll
#
# GtkGlarea, GtkGlExt
# LIB_NAME = libgtkgl-2.0.so
# LIB_NAME = libgtkglext-x11-1.0.so
LIB_NAME = /bin/cyggtkglext-x11-1.0-0.dll
# LIB_NAME = libgdkglext-x11-1.0.so
LIB_NAME = /bin/cyggdkglext-x11-1.0-0.dll
#
# Mozilla
# LIB_NAME = libgtkembedmoz.so
#
# Poppler for PDF files
# LIB_NAME = libpoppler-glib.so
LIB_NAME = /bin/cygpoppler-glib-8.dll
#
# XForms backend
# LIB_NAME = libforms.so
LIB_NAME = /usr/bin/cygforms-2.dll
# LIB_NAME = libflimage.so
LIB_NAME = /bin/cygflimage-2.dll
# LIB_NAME = libformsGL.so
LIB_NAME = /bin/cygformsGL-2.dll
#
# Motif backend
# LIB_NAME = libXm.so
LIB_NAME = /usr/bin/cygXm-4.dll
# LIB_NAME = libXt.so
LIB_NAME = /usr/bin/cygXt-6.dll
#
# Console backend (ncurses)
# LIB_NAME = libncurses.so
# LIB_NAME = libform.so
# LIB_NAME = libpanel.so
# LIB_NAME = libmenu.so
#
# O joy: X, GL and GLUT can be imported also!
# LIB_NAME = libX11.so
LIB_NAME = /usr/bin/cygX11-6.dll
# LIB_NAME = libglut.so
# LIB_NAME = libGLU.so
# LIB_NAME = libGL.so
#
# Generic libs
# LIB_NAME = libm.so
# LIB_NAME = libc.so
#
#---------------------------------------------------------------------------
#
EOF

cat gtk-server.original.cfg >> ./gtk-server.cfg



cat <<"EOF"

-------------------------------------------------
Step 4: Building
        Cygwin requires x86_64 flag for 
        configuration.
        Standard make is OK.
-------------------------------------------------

EOF

./configure --host=x86_64 --build=x86_64

make


cat <<"EOF"

-------------------------------------------------
Step 5: Installing
        This patch has no root rights.
        So you must install gtk-server manually.
        Switch to 
 
            ./gtk-server-2.4.6/src

        and type

            sudo make install
-------------------------------------------------

EOF


  

