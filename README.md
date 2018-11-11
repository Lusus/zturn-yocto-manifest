zturn-poky-manifest
===================

Quick hack Yocto Repo manifest repository, for zturn board. Originally based on https://github.com/Lusus/zturn-angstrom-manifest.

These are the setup scripts for the Yocto Project, with tweaks for using the https://github.com/Lusus/meta-zturn layer.

To configure the scripts and download the build metadata, do:

	$ mkdir ~/bin
	$ PATH=~/bin:$PATH

	$ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
	$ chmod a+x ~/bin/repo

Run repo init to bring down the latest version of Repo with all its most recent bug fixes. You must specify a URL for the manifest, which specifies where the various repositories included in the Yocto source will be placed within your working directory.

	$ repo init -u git://github.com/Lusus/zturn-angstrom-manifest -b default

When prompted, configure Repo with your real name and email address.

A successful initialization will end with a message stating that Repo is initialized in your working directory. Your client directory should now contain a .repo directory where files such as the manifest will be kept.

To pull down the metadata layers to your working directory from the repositories as specified in the default manifest, run

	$ repo sync

When downloading from behind a proxy (which is common in some corporate environments), it might be necessary to explicitly specify the proxy that is then used by repo:

	$ export HTTP_PROXY=http://<proxy_user_id>:<proxy_password>@<proxy_server>:<proxy_port>
	$ export HTTPS_PROXY=http://<proxy_user_id>:<proxy_password>@<proxy_server>:<proxy_port>

Setup Yocto Environment and building
------------------------------------
First enter the poky directory:
	$ cd poky
For first-time initialisation run
	$ TEMPLATECONF=meta-zturn/conf source oe-init-build-env
For subsequent runs use
	$ source oe-init-build-env

Now an image can be built:
	$ MACHINE=<machine> bitbake <image>
	e.g. MACHINE=zturn-zynq7 bitbake zturn-image

Deploying to SD-card
--------------------
To create a bootable SD-card, first repartition and format a SD-card, with a small (say 64 MB) FAT primary partition, followed by a EXT4 partition.

To populate the FAT partition, copy the files to which the symlinks listed below point to (all located in the deploy/glibc/images directory) to the FAT partition:
boot.bin => boot.bin
u-boot.img => u-boot.img
uImage => uImage
uImage-zynq-zturn.dtb => devicetree.dtb

Copy the desired bitstream to the FAT partition, and rename to system.bit.bin 

To populate the filesystem, sudo untar the zturn-image-zturn-zynq7.tar.gz archive to the second EXT4 partition.

Booting up
----------
Simply supply power to the board, via 5V or USB_UART.

Use a serial communication application (eg kermit) to connect to the USB serial device created when USB_UART gets enumarated, at 115200 baud. This link may be useful: http://denx.de/wiki/view/DULG/SystemSetup

Login as root without a password.

Tweaks
------
Look at layers/meta-zturn (after repo checked everything out) for all the custom recipes for the zturn, especially the README.md file.
