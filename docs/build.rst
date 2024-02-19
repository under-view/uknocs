Build uknocs
============

Documentation (Sphinx)
~~~~~~~~~~~~~~~~~~~~~~

uknocs uses sphinx framework for documentation. Primarily utilizing `The C Domain`_.

https://www.sphinx-doc.org/en/master/man/sphinx-build.html

============
Dependencies
============

- python3-pip

==========
Build Docs
==========

.. code-block:: bash

        $ git clone https://github.com/under-view/uknocs.git
        $ cd uknocs
        $ sudo pip3 install -r docs/requirements.txt

        # If no build directory exists
        $ meson setup build

	$ ninja -C build

.. _The C Domain: https://www.sphinx-doc.org/en/master/usage/restructuredtext/domains.html#the-c-domain
