Creating Custom Layer
~~~~~~~~~~~~~~~~~~~~~

============
Create Layer
============

.. code-block::

	$ bitbake-layers create-layer ../<layer name>

=========
Add Layer
=========

.. code-block::

	$ bitbake-layers add-layer ../<layer name>

==================
Ensure Layer Added
==================

.. code-block::

	$ cat < conf/bblayers.conf
