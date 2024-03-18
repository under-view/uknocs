Override Strategies
~~~~~~~~~~~~~~~~~~~

Far too often you'll find yourself needing to override a particular behaviour
that was done in another recipe in some OE layer included in your project.
There are many ways to override the contents of a recipe. We'll discuss a few
methodologies in this section.

================
Recipe Overrides
================

Typically recipe overrides happen via:

	* The use of a ``bbappend`` file.

          Typically two forms:

		* <recipe_name>_%.bbappend

			| Means no matter the version override behaviour of recipe.

 		* <recipe_name>_<recipe_version>.bbappend

			| Means given a specific version override behaviour of recipe.

	* Including the same file name with different version in your layer.
	  Then set the ``PREFERRED_VERSION_<recipe>`` variable accordingly to
	  choose which version of the recipe you want to utilize.

If you choose the ``bbappend`` route. Bellow sections talk a bit about common override strategies.

==================
Variable Overrides
==================

There are many ways to override variables, but in order to override we must first talk about
variable assignment.

	* MYVAR ??= "some value"
		| Very weakly assign variable. Making extremely easy to override.

	* MYVAR ?= "some value"
		| Weakly assign variable. Making it easy to override.

	* MYVAR = "some value"
		| Strongly assign variable. Making it almost impossible to override.

**Techinally speaking overrides for variables.**

	* MYVAR += "some value"
		| Append with spaces.

	* MYVAR .= " some value"
		| Append without spaces.

	* MYVAR:append = " some value"
		| Append without spaces.

	* MYVAR =+ "some value"
		| Prepend with spaces.

	* MYVAR =. " some value"
		| Prepend without spaces.

	* MYVAR:prepend = " some value"
		| Prepend without spaces.

If you have a variable weakly assigned in one layer. You can easily override it in another layer.

Typically strongly type variable overrides happen via: 

	* `CLASSOVERRIDE`_
		| Internal to OE build system. This is a variable that allows you to
		| set a variable or function for a specific type of build. So, lets
		| say you only want to assign a variable for target builds.
 		| Then you would

		* MYVAR:class-target = "some value"

		|
		| Values of `CLASSOVERRIDE`_  are as such

		* class-target
		* class-native
		* class-nativesdk

	* `MACHINEOVERRIDES`_
		| Typically set in machine configs. Allows for overriding
		| and typing variables to a specific machine.

	* `DISTROOVERRIDES`_
		| Typically set in distro configs. Allows for overriding
		| and typing variables to a specific distro.

	After setting these two variables if a variable is strongly set
	in a layer other than your custom layer:

		* MYVAR:my-machine-override = "Some more important value"
		* MYVAR:my-distro-override = "Some more important value"

	Will override the strongly set value ensuring your variable takes priority.

==================
Function Overrides
==================

Typically to override a function all one has to do is re-implement the function
in a ``bbappend`` or just a normal ``bb`` file.

Just like variables functions in Yocto also allow you to ``append`` or ``prepend`` to them.

.. code-block:: bash

	do_install:prepend:class-target() {
		install -d ${D}/my/custom/location
		install ${S}/my-custom-file.txt ${D}/my/custom/location
	}

	do_install:prepend:class-native() {
		install -d ${D}/my/custom/location
		install ${S}/my-custom-file.txt ${D}/my/custom/location
	}

.. code-block:: bash

	do_install:append:class-target() {
		install -d ${D}/my/custom/location
		install ${S}/my-custom-file.txt ${D}/my/custom/location
	}

	do_install:append:class-native() {
		install -d ${D}/my/custom/location
		install ${S}/my-custom-file.txt ${D}/my/custom/location
	}

Yocto also allows you to inject functions or delete them entirely via the commands

	* addtask
	* deltask

=======================
BitBake Class Overrides
=======================

You can't override a BitBake class the same way you override a recipe.
To override a class you have to utilize some of the above aforementioned
methods.

.. _MACHINEOVERRIDES: https://docs.yoctoproject.org/ref-manual/variables.html#term-MACHINEOVERRIDES
.. _DISTROOVERRIDES: https://docs.yoctoproject.org/ref-manual/variables.html#term-DISTROOVERRIDES
.. _CLASSOVERRIDE: https://docs.yoctoproject.org/ref-manual/variables.html#term-CLASSOVERRIDE
