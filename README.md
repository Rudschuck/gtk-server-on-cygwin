# Install gtk-server on Cygwin



Unfortunately the gtk-server cannot installed from its source under Cygwin. This is due to the fact that some Cygwin shared libraries have a perculiar naming convention. They are prefixed with 'Cyg' instead of 'lib'. Furthermore the extension of libraries can be either '.so' or '.dll'. The last inconsisty is that X11 and GTK libraries are stored in /bin. 

For theses reasons some patches and hints are provided.


This script will download the version 2.4.6 of the gtk-server and apply the relevant patches.
    

The script will process the following four steps:

1. Setting up environment
2. Download gtk-server 2.4.6
3. Patching make and gtk-server.cfg
4. Compiling
5. Installing

 
After step 4, the binaries are available locally.

If a global installation is needed step 5 must be performed manually as root.


#### Requirement

A list of the mandatory libraries can be found at:

- gtk-server.cfg

Check that the corresponding sources ( ...-dev) are available.


#### LICENCE 

This program is distributed under the terms of the GNU General Public License (GPLv3).  

#### AUTHOR

(c) Michael Rudschuck

#### HISTORY

2024-02-09 - v0.1 - first version

#### TODOS & Hints

    - Tested only with gtk+-3.0 and gtk-server 2.4.6
    - Not tested for gtk+-1.0, gtk+-2.0 and motif

