Getting Started
~~~~~~~~~~~~~~~

=======================
Installing Dependencies
=======================

Ever dependency you will need to start building linux
distribution with the Yocto Project may be found here.

`Yocto Project Build Host Packages`_

Debian Packages
===============

.. code-block:: bash

	$ sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential \
	                   chrpath socat cpio python3 python3-pip python3-pexpect \
	                   xz-utils debianutils iputils-ping python3-git python3-jinja2 \
	                   libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev \
	                   zstd liblz4-tool file locales libacl1
	$ sudo locale-gen en_US.UTF-8

============
Cloning Poky
============

.. code-block:: bash

	$ git clone git://git.yoctoproject.org/poky
	Cloning into 'poky'...
	remote: Enumerating objects: 638877, done.
	remote: Total 638877 (delta 0), reused 0 (delta 0), pack-reused 638877
	Receiving objects: 100% (638877/638877), 202.90 MiB | 40.27 MiB/s, done.
	Resolving deltas: 100% (465363/465363), done.

=====================
Examine Poky Contents
=====================
	
.. code-block:: bash

	$ cd poky ; ls
	bitbake  documentation  LICENSE.GPL-2.0-only  MAINTAINERS.md  meta       meta-selftest  meta-yocto-bsp     README.hardware.md  README.OE-Core.md  README.qemu.md  SECURITY.md
	contrib  LICENSE        LICENSE.MIT           MEMORIAM        meta-poky  meta-skeleton  oe-init-build-env  README.md           README.poky.md     scripts

MAINTAINERS
===========

Look inside the ``MAINTAINERS.md`` if you ever have issues with a given recipe contact the maintainer.
For instance if you have issues with the ``vulkan-validation-layers`` recipe contact the maintainer

.. code-block:: bash

	$ cat < meta/conf/distro/include/maintainers.inc | grep Vincent
	RECIPE_MAINTAINER:pn-vulkan-validation-layers = "Vincent Davis Jr <vince@underview.tech>"

bitbake
=======

Look inside the ``bitbake`` folder. This is where the recipe parser and recipe task executor is located.

``bitbake`` is utilized to generate final linux distribution based upon the contents of multiple recipe packages.

In short,

BitBake is a make-like build tool with the special focus of distributions and packages for embedded
Linux cross compilation, although it is not limited to that.

https://en.wikipedia.org/wiki/BitBake

.. code-block:: bash

	$ ls bitbake/
	AUTHORS  bin  ChangeLog  contrib  doc  lib  LICENSE  LICENSE.GPL-2.0-only  LICENSE.MIT  README  SECURITY.md  toaster-requirements.txt

	$ ls bitbake/bin/
	bitbake           bitbake-dumpsig  bitbake-hashclient  bitbake-layers  bitbake-selftest  bitbake-worker    toaster
	bitbake-diffsigs  bitbake-getvar   bitbake-hashserv    bitbake-prserv  bitbake-server    git-make-shallow  toaster-eventreplay 

meta
====

Look inside the ``meta`` folder. This is where your core recipes, classes, configs, and python wic plugins reside.
These recipes all build linux packages that are typically on any linux distro you download off the internet. Some
recipes are special in that they build cross compilers or build tools that run natively (recipes-sysroot-native) on
build host to build other recipe packages. That are then run on target architecture.

.. code-block:: bash

	$ ls meta
	classes         classes-recipe  COPYING.MIT  lib          recipes-connectivity  recipes-devtools  recipes-gnome     recipes-kernel      recipes-rt    recipes-support  site
	classes-global  conf            files        recipes-bsp  recipes-core          recipes-extended  recipes-graphics  recipes-multimedia  recipes-sato  recipes.txt

meta-poky
=========

Look inside the ``meta-poky`` folder. This contains poky's ``DISTRO`` config file. That you'll utilize
for your first build.

``DISTRO`` configs generally store global variables and inherit clasess whose functions will be
utilized across the entire project.

.. code-block:: bash

	$ ls meta-poky/
	classes  conf  README.poky.md  recipes-core

	$ ls meta-poky/conf/distro/
	include  poky-altcfg.conf  poky-bleeding.conf  poky.conf  poky-tiny.conf

	# Default distro used to build poky
	$ vim meta-poky/conf/distro/poky.conf

Some key global variables usually in ``DISTRO`` config files.

See Yocto Project `Variable Glossary`_ for more details.

	* **DISTRO_FEATURES**
	* **PACKAGE_CLASSES**
	* **INIT_MANAGER**
	* **DISTROOVERRIDES**

oe-init-build-env
=================

Script used to setup yocto project build environment. One should source it to gain access
bitbake and do anything related to building their linux distribution.

========
Building
========

Enter Build Environment
=======================

.. code-block:: bash

	source oe-init-build-env

Adding OE Layers
================

Because this is the reference distribution. Layers you need to build
are already added.

.. code-block:: bash

	$ cat < conf/bblayers.conf
	# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
	# changes incompatibly
	POKY_BBLAYERS_CONF_VERSION = "2"

	BBPATH = "${TOPDIR}"
	BBFILES ?= ""

	BBLAYERS ?= " \
	  ../meta \
	  ../meta-poky \
	  ../meta-yocto-bsp \
	  "

**BBLAYERS** this variable is utilized by bitbake to decide which layers to parse.

Generally one would first clone a layer then add it mainly to the ``BBLAYERS`` variable
or with the use of the command ``bitbake-layers add-layer <layer name>``.

.. code-block:: bash

	$ git clone git://git.openembedded.org/meta-openembedded ../meta-openembedded
	$ bitbake-layers add-layer ../meta-openembedded/meta-oe
	$ bitbake-layers add-layer ../meta-openembedded/meta-python
	$ bitbake-layers add-layer ../meta-openembedded/meta-networking

.. code-block:: bash

	$ cat < conf/bblayers.conf 
	# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
	# changes incompatibly
	POKY_BBLAYERS_CONF_VERSION = "2"

	BBPATH = "${TOPDIR}"
	BBFILES ?= ""

	BBLAYERS ?= " \
	  ../meta \
	  ../meta-poky \
	  ../meta-yocto-bsp \
	  ../meta-openembedded/meta-oe \
	  ../meta-openembedded/meta-python \
	  ../meta-openembedded/meta-networking \
	  "

Updating local.conf
===================

You can think of ``local.conf`` as your projects core config file with the ability to 
overrides all other conf file variables. When developing it's best not to update this
file. Updating ``local.conf`` will result in every layer you have in ``BBLAYERS`` variable
being in reparsed by bitbake.

.. code-block:: bash

	$ vim conf/local.conf

	# Add the bellow replacing 8 with half your CPU cores
	# Trust you don't want to run into the OOM killer.
	PARALLEL_MAKE = "-j 8"
	BB_NUMBER_THREADS = "8"

Generally the variables you care the most about are

See Yocto Project `Variable Glossary`_ for more details.

	* ``MACHINE``
		| Selects machine config file. In short defines compiler flags and what ever else
		| is required to run on target architecture.

	* ``DISTRO``
		| Selects distro config file.

	* ``PACKAGE_CLASSES``
		| Used to select package type that each recipe will store it's build output in.

	* ``EXTRA_IMAGE_FEATURES``
		| A list of additional features to include in an image. 

	* ``PARALLEL_MAKE``
		| Number of CPU cores to use when building a package. You may also export variable
		| so that it's viewable by bitbake and will be used during builds.

	* ``BB_NUMBER_THREADS``
		| Number of CPU cores bitbake may used to parse recipes and execute their task.
		| You may also export variable so that it's viewable by bitbake and will be used
		| during builds.

Building A System Image
=======================

.. code-block:: bash

	# Start system image build
	$ MACHINE="qemux86-64" DISTRO="poky" bitbake core-image-base

	# If something fails due to OOM killer. Clean recipe shared state
	# cache, manually rebuild and restart system image build.
	$ MACHINE="qemux86-64" DISTRO="poky" bitbake -c cleansstate llvm
	$ MACHINE="qemux86-64" DISTRO="poky" bitbake llvm

===============
Testing In A VM
===============

.. code-block:: bash

	$ runqemu tmp/deploy/images/qemux86-64/core-image-base-qemux86-64.rootfs.qemuboot.conf \
		  core-image-base \
		  slirp \
		  serialstdio

.. _Yocto Project Build Host Packages: https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html#build-host-packages
.. _Variable Glossary: https://docs.yoctoproject.org/ref-manual/variables.html
