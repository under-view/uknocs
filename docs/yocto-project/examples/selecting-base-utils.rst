Selecting Base Utils
~~~~~~~~~~~~~~~~~~~~

This section won't discuss the different available linux base utilities
or what they are just how to select one in yocto project.

Specifically how to select coreutils over busybox.

=============================
Select Coreutils Over Busybox
=============================

Append bellow to ``conf/distro/mydistro.conf``.

**NOTE:** To fully support coreutils over busybox you have to override
``recipes-core/initrdscripts/initramfs-framework.bb``. Utilizing a bbappend
(BitBake Append) file. Since we only test in a VM there's no need for
the override.

.. code-block:: bash

	# Replaces busybox,uutils-coreutils with coreutils
	SKIP_RECIPE[uutils-coreutils] = "Don't build this"
	SKIP_RECIPE[busybox] = "Don't build this"
	VIRTUAL-RUNTIME_syslog = ""
	VIRTUAL-RUNTIME_login_manager = "shadow-base"
	VIRTUAL-RUNTIME_base-utils = "packagegroup-core-base-utils"
	VIRTUAL-RUNTIME_base-utils-hwclock = "util-linux-hwclock"
	VIRTUAL-RUNTIME_base-utils-syslog = ""
	VIRTUAL-RUNTIME_vim = "vim"

	PREFERRED_PROVIDER_virtual/base-utils = "packagegroup-core-base-utils"
	PREFERRED_PROVIDER_coreutils = "coreutils"
	PREFERRED_PROVIDER_uutils-coreutils = "coreutils"

Most of these variables where already discussed in the previous section.

See Yocto Project `Variable Glossary`_ for more details.

	* ``SKIP_RECIPE``
		| Used to ensure a given recipe does not build.

	* ``base-utils``
		| Includes cp,rm,mv,pwd,ls,sed,dd,etc..

	* ``uutils-coreutils``
		| A rust implementation of GNU Coreutils. Provided by meta-openembedded.

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

.. code-block:: bash

	$ ls /usr/bin/*.coreutils

	# Make sure the busybox implementation is not there
	$ ls /usr/bin/*.busy*

.. _Variable Glossary: https://docs.yoctoproject.org/ref-manual/variables.html
