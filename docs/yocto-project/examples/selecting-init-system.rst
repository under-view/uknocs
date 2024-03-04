Selecting Init System
~~~~~~~~~~~~~~~~~~~~~

This section won't discuss the different available init systems
or what they are just how to select one in yocto project.

Specifically how to select systemd as the choosen init system.

======================
Creating Distro Config
======================

Change directories into your custom layer created in previous section
and create a poky distro config.

.. code-block:: bash

	mkdir -p conf/distro
	touch conf/distro/mydistro.conf

Add the following into ``conf/distro/mydistro.conf``

========================
Select Systemd Over SysV
========================

.. code-block:: bash

	require conf/distro/poky.conf

	# Replaces sysvinit init system with systemd init system
	VIRTUAL-RUNTIME_initscripts = ""
	VIRTUAL-RUNTIME_init_manager = "systemd"
	VIRTUAL-RUNTIME_dev_manager = "udev"

	PREFERRED_PROVIDER_udev = "systemd"
	PREFERRED_PROVIDER_udev-utils = "systemd"

	DISTRO_FEATURES_BACKFILL_CONSIDERED:append = " sysvinit"
	DISTRO_FEATURES += "usrmerge systemd"

See Yocto Project `Variable Glossary`_ for more details.

	* ``PREFERRED_PROVIDER_<package name>``
		| Used if multiple recipes provide the same file(s). In the
		| recipe however you have to set the ``PROVIDES`` variable.
		| When yocto project goes to build a system image whatever
		| recipe provides ``<package name>`` files will be used
		| during builds and in the final system image.
		|
		| In the example above the provider for the linux kernel device
		| manager is ``udev`` this value could be similarly set to
		| ``busybox-mdev``.

	* ``VIRTUAL-RUNTIME``
		| Similar to ``PREFERRED_PROVIDER`` selects which package gets
 		| used for a specific functionality at runtime. Thus, specifying
		| whatever recipe ``PROVIDES`` said package. That package will then
		| be used to perform functionality.
		|
		| In above example ``init_manager`` choosen is `systemd`. This could
		| very well be set to `sysvinit`. The chosesn ``dev_manager`` (device manager)
		| is ``udev`` this value could be similarly set to ``busybox-mdev``.
		|
		| ``VIRTUAL-RUNTIME`` gets utilized by variables such as ``IMAGE_INSTALL``,
		| ``RDEPENDS``, ``RRECOMMENDS``, etc.. internally by multiple recipes.

	* ``DISTRO_FEATURES_BACKFILL_CONSIDERED``
		| Features that should not be added into ``DISTRO_FEATURES`` during builds.

	* ``DISTRO_FEATURES``
		| Each linux distro has a set of features that make it unique.
		| In terms on the Yocto project this variable is used by multiple
		| recipes and classes to specify what actions need to be performed
		| to support a given linux system image feature.
		| See `Yocto Project Distro Features`_ for more information.

=====================
Building System Image
=====================

.. code-block:: bash

	$ MACHINE="qemux86-64" DISTRO="mydistro" bitbake core-image-base

===============
Testing In A VM
===============

You should now see systemd in the serial output.

.. code-block:: bash

	$ runqemu tmp/deploy/images/qemux86-64/core-image-base-qemux86-64.rootfs.qemuboot.conf \
		  core-image-base \
		  slirp \
		  serialstdio

.. _Variable Glossary: https://docs.yoctoproject.org/ref-manual/variables.html
.. _Yocto Project Distro Features: https://docs.yoctoproject.org/ref-manual/features.html#distro-features
