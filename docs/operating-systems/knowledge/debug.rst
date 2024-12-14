Debugging
~~~~~~~~~

May add some of bellow to ``$HOME/.gdbinit``.

.. code-block:: bash

	set pagination off
	set history save on
	set history expansion on

==========================
GDB With Yocto Project SDK
==========================

.. code-block:: bash

	$ gdbserver --multi 127.0.0.1:5555

.. code-block:: bash

	set remote exec-file <path to program>
	target extended-remote 127.0.0.1:5555

Useful when debugging with the yocto project SDK.
May add bellow to ``$HOME/.gdbinit``.

.. code-block:: bash

	python
	import gdb
	import os

	sysroot = os.environ['SDKTARGETSYSROOT']

	solib_search_path = os.environ['SDKTARGETSYSROOT'] + '/usr/' + os.environ['OECORE_BASELIB']
	solib_search_path += ':' + os.getcwd() + '/build'

	solib_abs_prefix = os.environ['SDKTARGETSYSROOT'] + '/usr/' + os.environ['OECORE_BASELIB']
	solib_abs_prefix += ':' + os.getcwd() + '/build'

	gdb.execute('set sysroot ' + sysroot)
	gdb.execute('set solib-absolute-prefix ' + solib_abs_prefix)
	gdb.execute('set solib-search-path ' + solib_search_path)
	end

=======
GDB TUI
=======

Enable terminal user interface (tui).
May add bellow to ``$HOME/.gdbinit``.

.. code-block:: bash

	tui enable
	tui new-layout important {-horizontal src 1 asm 1} 2 status 0 {-horizontal cmd 1 regs 1} 2
	layout important
	tui window width regs -25
	focus cmd

For switching gdb terminal user interface windows.

.. code-block:: bash

	focus asm  # gdb tui assembly window
	focus regs # gdb tui register tui window
	focus cmd  # gdb tui command window
	focus src  # gdb tui source window

===================
GDB Multi-Threading
===================

.. code-block:: bash

	info threads

.. code-block:: bash

	thread <number> # Switch to thread
	set schedular-locking on
	break 120 thread 2

======================================
GDB Debug Child Processes (inferior's)
======================================

More information may be found at `gdb fork docs`_.

.. code-block:: gdb

	set follow-fork-mode child
	set detach-on-fork off
	set schedule-multiple on

.. code-block:: bash

	detach inferiors
	kill inferiors

=============
GDB x Command
=============

Based on `gdb x command`_.

Displays the memory contents at a given address using the specified format.

.. code-block::

	x [Address expression]
	x /[Format] [Address expression]
	x /[Length][Format] [Address expression]
	x

.. _gdb x command: https://visualgdb.com/gdbreference/commands/x
.. _gdb fork docs: https://sourceware.org/gdb/current/onlinedocs/gdb.html/Forks.html
