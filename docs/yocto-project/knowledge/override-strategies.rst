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

==================
Variable Overrides
==================

==================
Function Overrides
==================

=======================
BitBake Class Overrides
=======================

You can't override a BitBake class the same way you override a recipe.
To override a class you have to utilize some of the above aforementioned
methods. But ``bbclass`` files may be overwritten as well.
