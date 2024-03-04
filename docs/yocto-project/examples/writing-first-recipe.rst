Writing First Recipe
~~~~~~~~~~~~~~~~~~~~

Goal of this lesson is to write a recipe that will inject
bash scripts and text files into the system image.

You will need to utilize the ``IMAGE_INSTALL`` variable
discussed in previous sections to get content placed into
system image. Normally one should avoid updating ``local.conf``,
but for this tutorial adding it there is fine.

You also need to seperate the installed files into two seperate packages.

``scripts`` & ``text``

===============
Creating recipe
===============

.. code-block:: bash

	$ mkdir -p recipes-mydistro/injectme/{scripts,texts}
	$ touch recipes-mydistro/injectme/injectme_0.0.1.bb

=====================
Recipe Core Structure
=====================

Remember to revist the Yocto Project `Variable Glossary`_ if you don't know
what a given variable is used for.

An important variable to ``do_install`` task ``${D}`` (Destination).
It's usually set to a given recipes ``${WORKDIR}/image`` before being packaged.

Any files placed in ``${D}`` will go onto the system image in that exact
directory or in a recipes native sysroot (``${WORKDIR}/recipe-sysroot-native``).
Depending on what the recipe is setup to do.

.. code-block:: bash

	SUMMARY = "Inject files into Yocto built linux system image."
	HOMEPAGE = "here"

	# Doesn't matter
	SECTION = "examples"

	LICENSE = "MIT"
	LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

	DEPENDS = ""
	RDEPENDS:${PN} = "bash"

	# := Means expand variable immediately
	FILESEXTRAPATHS:prepend := "${THISDIR}/scripts:"
	FILESEXTRAPATHS:prepend := "${THISDIR}/texts:"

	SRC_URI = ""

	S = "${WORKDIR}"

	do_install() {

	}

	# List of packages a recipe can produce
	PACKAGES += ""
	PROVIDES += ""
	RPOVIDES:${PN} += ""

	FILES:${PN} += ""

===============
Files To Inject
===============

``set-bash-prompt.sh`` place in ``/etc/profile.d -> ${sysconfdir}/profile.d``

.. code-block:: bash

	set_bash_prompt() {
		fs_mode=$(mount | sed -n -e "s/^\/dev\/.* on \/ .*(\(r[w|o]\).*/\1/p")
		PS1='\[\e[91;1m\]\u@\h ${fs_mode:+($fs_mode)}\[\033[00m\]: \[\e[33;1m\]\W \[\e[32;1m\]\$ \[\033[0m\]'
	}

	PROMPT_COMMAND=set_bash_prompt

``extra-alias.sh`` place in ``/etc/profile.d -> ${sysconfdir}/profile.d``

.. code-block:: bash

	NORMAL=`echo -e '\x1b[0m'`
	RED=`echo -e '\x1B[31;1m'`
	LGREEN=`echo -e '\033[1;32m'`
	IBLUE=`echo -e '\x1B[30;1m'`
	LBLUE=`echo -e '\033[1;34m'`
	YELLOW=`echo -e '\x1B[33;1m'`
	MAGENTA=`echo -e '\e[37;1m'`
	IP_CMD=$(which ifconfig)

	colored_ip() {
		${IP_CMD} $@ | sed \
			-e "s/inet [^ ]\+ /${LGREEN}&${NORMAL}/g"\
			-e "s/ether [^ ]\+ /${RED}&${NORMAL}/g"\
			-e "s/netmask [^ ]\+ /${LBLUE}&${NORMAL}/g"\
			-e "s/broadcast [^ ]\+ /${IBLUE}&${NORMAL}/g"\
			-e "s/^default via .*$/${YELLOW}&${NORMAL}/g"\
			-e "s/^\([0-9]\+: \+\)\([^ \t]\+\)/\1${MAGENTA}\2${NORMAL}/g"
	}

	alias ifconfig='colored_ip'

	alias vi='vim'

	alias ls='ls --color'
	alias dir='dir --color=auto'
	alias vdir='vdir --color=auto'

	alias grep='grep --color=auto'
	alias fgrep='fgrep --color=auto'
	alias egrep='egrep --color=auto'

	# some more ls aliases
	alias ll='ls -alF'
	alias la='ls -A'
	alias l='ls -CF'

``testing.txt``

.. code-block:: bash

	Injecting Cool text file bro

================
Recipe Execution
================

~~~~~~~~~~~~
Build Recipe
~~~~~~~~~~~~

After running bellow bitbake will execute all task defined
in the ``injectme`` recipe.

.. code-block:: bash

	MACHINE="qemux86-64" DISTRO="mydistro" bitbake injectme

~~~~~~~~~~~~~~~
Run Recipe Task
~~~~~~~~~~~~~~~

To run an individual task/function

.. code-block:: bash

	MACHINE="qemux86-64" DISTRO="mydistro" bitbake injectme -c install

~~~~~~~~
Devshell
~~~~~~~~

The devshell is useful as it give a working environment to test in
prior to finishing a recipe. Very useful when debugging stick cross-compile
situations.

.. code-block:: bash

	MACHINE="qemux86-64" DISTRO="mydistro" bitbake injectme -c devshell

	# Most task can be executed by running the scripts in temp
	./temp/run.do_install

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

**Your interactive BASH shell should look alot more colorful**

.. code-block:: bash

	$ cat < /place/where/you/injected/text/testing.txt

.. _Variable Glossary: https://docs.yoctoproject.org/ref-manual/variables.html
