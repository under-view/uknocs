Debugging
~~~~~~~~~

===================
GDB Useful Commands
===================

.. code-block:: bash

	$ gdb -tui ./<program name>

.. code-block:: bash

	$ gdbserver 127.0.0.1:5555

May add some of bellow to ``$HOME/.gdbinit``.

.. code-block:: bash

	set pagination off
	set history save on
	set history expansion on

.. code-block:: bash

	set sysroot <sysroot path>
	set solib-absolute-prefix <sysroot path>
	set solib-search-path <sysroot path>

.. code-block:: bash

	target remote 127.0.0.1:5555

.. code-block:: bash

	info threads
	info sharedlibrary

.. code-block:: bash

	// Switch to thread
	thread #
	set schedular-locking on
	break 120 thread 2

.. code-block:: bash

	tui layout regs
