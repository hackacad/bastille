Configuration
=============

Bastille is configured using a default config file located at
``/usr/local/etc/bastille/bastille.conf``. When first installing bastille, you
should run ``bastille setup``. This will ask if you want to copy the sample
config file to the above location. The defaults are sensible for UFS, but if you
want to use ZFS, you will have to change a few options. See the chapter on ZFS
Support.

This is the default `bastille.conf` file.

.. code-block:: shell

  #####################
  ## [ BastilleBSD ] ##
  #####################

  ## default paths
  bastille_prefix="/usr/local/bastille"                                 ## default: "/usr/local/bastille"
  bastille_backupsdir="${bastille_prefix}/backups"                      ## default: "${bastille_prefix}/backups"
  bastille_cachedir="${bastille_prefix}/cache"                          ## default: "${bastille_prefix}/cache"
  bastille_jailsdir="${bastille_prefix}/jails"                          ## default: "${bastille_prefix}/jails"
  bastille_releasesdir="${bastille_prefix}/releases"                    ## default: "${bastille_prefix}/releases"
  bastille_templatesdir="${bastille_prefix}/templates"                  ## default: "${bastille_prefix}/templates"
  bastille_logsdir="/var/log/bastille"                                  ## default: "/var/log/bastille"

  ## pf configuration path
  bastille_pf_conf="/etc/pf.conf"                                       ## default: "/etc/pf.conf"

  ## bastille scripts directory (assumed by bastille pkg)
  bastille_sharedir="/usr/local/share/bastille"                         ## default: "/usr/local/share/bastille"

  ## bootstrap archives, which components of the OS to install.
  ## base  - The base OS, kernel + userland
  ## lib32 - Libraries for compatibility with 32 bit binaries
  ## ports - The FreeBSD ports (3rd party applications) tree
  ## src   - The source code to the kernel + userland
  ## test  - The FreeBSD test suite
  ## this is a whitespace separated list:
  ## bastille_bootstrap_archives="base lib32 ports src test"
  bastille_bootstrap_archives="base"                                    ## default: "base"

  ## default timezone
  bastille_tzdata=""                                                    ## default: empty to use host's time zone

  ## default jail resolv.conf
  bastille_resolv_conf="/etc/resolv.conf"                               ## default: "/etc/resolv.conf"

  ## bootstrap urls
  bastille_url_freebsd="http://ftp.freebsd.org/pub/FreeBSD/releases/"          ## default: "http://ftp.freebsd.org/pub/FreeBSD/releases/"
  bastille_url_hardenedbsd="https://installers.hardenedbsd.org/pub/" ## default: "https://installer.hardenedbsd.org/pub/HardenedBSD/releases/"
  bastille_url_midnightbsd="https://www.midnightbsd.org/ftp/MidnightBSD/releases/"          ## default: "https://www.midnightbsd.org/pub/MidnightBSD/releases/"

  ## ZFS options
  bastille_zfs_enable="NO"                                              ## default: "NO"
  bastille_zfs_zpool=""                                                 ## default: ""
  bastille_zfs_prefix="bastille"                                        ## default: "bastille"
  bastille_zfs_options="-o compress=lz4 -o atime=off"                   ## default: "-o compress=lz4 -o atime=off"

  ## Export/Import options
  bastille_compress_xz_options="-0 -v"                                  ## default "-0 -v"
  bastille_decompress_xz_options="-c -d -v"                             ## default "-c -d -v"
  bastille_compress_gz_options="-1 -v"                                  ## default "-1 -v"
  bastille_decompress_gz_options="-k -d -c -v"                          ## default "-k -d -c -v"
  bastille_export_options=""                                            ## default "" predefined export options, e.g. "--safe --gz"

  ## Networking
  bastille_network_loopback="bastille0"                                 ## default: "bastille0"
  bastille_network_pf_ext_if="ext_if"                                   ## default: "ext_if"
  bastille_network_pf_table="jails"                                     ## default: "jails"
  bastille_network_shared=""                                            ## default: ""
  bastille_network_gateway=""                                           ## default: ""
  bastille_network_gateway6=""                                          ## default: ""

  ## Default Templates
  bastille_template_base="default/base"                                 ## default: "default/base"
  bastille_template_empty=""                                            ## default: "default/empty"
  bastille_template_thick="default/thick"                               ## default: "default/thick"
  bastille_template_clone="default/clone"                               ## default: "default/clone"
  bastille_template_thin="default/thin"                                 ## default: "default/thin"
  bastille_template_vnet="default/vnet"                                 ## default: "default/vnet"

Notes
-----

The options here are fairly self-explanitory, but there are some things to note.

* If you use ZFS, DO NOT create the bastille dataset. You must only create the
  parent. Bastille must be allowed to create the ``bastille`` child dataset, or
  you will have issues. So, if you want bastille to live at
  ``zroot/data/bastille`` you should set ``bastille_zfs_zpool`` to ``zroot`` and
  ``bastille_zfs_prefix`` to ``data/bastille`` but you should only create
  ``zroot/data`` before running bastille for the first time.

* Bastille will mount the dataset it creates at ``bastille_prefix`` which
  defaults to ``/usr/local/bastille``. So if you want to navigate to your jails,
  you will use the ``bastille_prefix`` as the location because this is where the
  will be mounted.

Custom Configuration
--------------------

Bastille now supports using a custom config in addition to the default one. This
is nice if you have multiple users, or want to store different
jails at different locations based on your needs.

The customized config file MUST BE PLACED INSIDE THE BASTILLE CONFIG FOLDER at
``/usr/local/etc/bastille`` or it will not work.

Simply copy the default config file and edit it according to your new
environment or user. Then, it can be used in a couple of ways.

1. Run Bastille using ``bastille --config config.conf bootstrap 14.2-RELEASE``
   to bootstrap the release using the new config.

2. As a specific user, export the ``BASTILLE_CONFIG`` variable using ``export
   BASTILLE_CONFIG=config.conf``. This config will then always be used when
   running Bastille with that user. See notes below...

- Exporting the ``BASTILLE_CONFIG`` variable will only export it for the current session. If you want to persist the export, see documentation for the shell that you use.

- If you use sudo, you will need to run it with ``sudo -E bastille bootstrap...`` to preserve your users environment. This can also be persisted by editing the sudoers file.

- If you do set the ``BASTILLE_CONFIG`` variable, you do not need to specify the config file when running Bastille as that specified user.
