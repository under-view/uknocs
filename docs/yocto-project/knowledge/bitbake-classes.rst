BitBake Classes
~~~~~~~~~~~~~~~

=========================
What are BitBake Classes?
=========================

BitBake classes are files that typically contain functions and variables
that are common in multiple recipes. These recipes then ``inherit`` the class
in order to gain access to functions and variable. Thus, allowing multiple recipes
a way acquire simaliar functionality without needing to implement the specific
functionality on an individual bases.

In terms of writing a class it's no different than writing a recipe. Just make sure your
custom class is placed in your layers classes directory with the suffix ``.bbclass``.

=====================
Normally Used Classes
=====================

	* `core-image.bbclass`_
		| Used by most system image recipes (`core-image-base.bb`_) to build the final system image.

	* `native.bbclass`_
		| Typically inherited via ``BBCLASSEXTNED``. Enables a recipe
		| to be installed on the native (host) side of a recipes
		| working environment ``RECIPE_SYSROOT_NATIVE``.

		.. code-block:: bash

			BBCLASSEXTEND += "native"

	* `nativesdk.bbclass`_
		| Typically inherited via ``BBCLASSEXTNED``. Enables a recipe
		| to be installed on the native (host) side of the SDK.

		.. code-block:: bash

			BBCLASSEXTEND += "nativesdk"

	* `pkgconfig.bbclass`_
		| Useful to select what project configurations you want along with
		| any given dependecies that particular configuration requires.
		| Any configuration you want to request need to be added into the
		| ``PACKAGECONFIG`` variable. The ``PACKAGECONFIG`` variable is
		| formatted as such:

		.. code-block:: bash

			PACKAGECONFIG[feature] = "Configuration if specified in PACKAGECONFIG, Configuration if not specified in PACKAGECONFIG, dependencies"

		| Bellow shows typical use case with BitBake classes.

	* `meson.bbclass`_
		| May be used to simplify building of meson based projects. Typical recipes that
		| build projects with meson as build system have something like such.

		.. code-block:: bash

			# Corresponds to meson options in kmsroots/meson_options.txt
			PACKAGECONFIG[examples] = "-Dexamples=true,-Dexamples=false,"
			PACKAGECONFIG[tests] = "-Dtests=true,-Dtests=false,"
			PACKAGECONFIG[debugging] = "-Ddebugging=enabled,-Ddebugging=disabled,"
			PACKAGECONFIG[kms] = "-Dkms=enabled,-Dkms=disabled,libdrm virtual/libgbm"
			PACKAGECONFIG[libseat] = "-Dlibseat=enabled,-Dlibseat=disabled,systemd udev seatd"
			PACKAGECONFIG[libinput] = "-Dlibinput=enabled,-Dlibinput=disabled,libinput"
			PACKAGECONFIG[xcb] = "-Dxcb=enabled,Dxcb=disabled,libxcb xcb-util-wm"
			PACKAGECONFIG[wayland] = "-Dwayland=enabled,-Dwayland=disabled,wayland wayland-native"
			PACKAGECONFIG[shaderc] = "-Dshaderc=enabled,-Dshaderc=disabled,shaderc glslang"

			PACKAGECONFIG ??= "kms xcb wayland libseat libinput shaderc"

			inherit meson pkgconfig

			EXTRA_OEMESON += "--buildtype release"

	* `cmake.bbclass`_
		| May be used to simplify building of cmake based projects. Typical recipes that
		| build projects with cmake as build system have something like such.

		.. code-block:: bash

			PACKAGECONFIG[feature-openxr-service] = "-DXRT_FEATURE_SERVICE=ON,-DXRT_FEATURE_SERVICE=OFF,"
			PACKAGECONFIG[opengl] = "-DXRT_HAVE_OPENGL=ON,-DXRT_HAVE_OPENGL=OFF,virtual/libgl"
			PACKAGECONFIG[openglx] = "-DXRT_HAVE_OPENGL_GLX=ON,-DXRT_HAVE_OPENGL_GLX=OFF,virtual/libglx"
			PACKAGECONFIG[opengles] = "-DXRT_HAVE_OPENGLES=ON,-DXRT_HAVE_OPENGLES=OFF,virtual/libgles1 virtual/libgles2"
			PACKAGECONFIG[egl] = "-DXRT_HAVE_EGL=ON,-DXRT_HAVE_EGL=OFF,virtual/egl"
			#PACKAGECONFIG[realsense] = "-DXRT_HAVE_REALSENSE=ON,-DXRT_HAVE_REALSENSE=OFF,"
			PACKAGECONFIG[hidapi] = "-DXRT_HAVE_HIDAPI=ON,-DXRT_HAVE_HIDAPI=OFF,hidapi"
			PACKAGECONFIG[wayland] = "-DXRT_HAVE_WAYLAND=ON -DXRT_HAVE_WAYLAND_DIRECT=ON, \
						  -DXRT_HAVE_WAYLAND=OFF -DXRT_HAVE_WAYLAND_DIRECT=OFF, \
						  wayland-native wayland wayland-protocols libdrm"
			PACKAGECONFIG[sdl2] = "-DXRT_HAVE_SDL2=ON,-DXRT_HAVE_SDL2=OFF,libsdl2"

			PACKAGECONFIG ??= "\
			    ${@bb.utils.contains('DISTRO_FEATURES', 'systemd', 'feature-openxr-service', '', d)} \
			    ${@bb.utils.contains('DISTRO_FEATURES', 'opengl', 'opengl opengles egl', '', d)} \
			    ${@bb.utils.contains('DISTRO_FEATURES', 'opengl x11', 'openglx', '', d)} \
			    ${@bb.utils.contains('DISTRO_FEATURES', 'wayland', 'wayland', '', d)} \
			    "

			inherit cmake pkgconfig

			EXTRA_OECMAKE += "-DBUILD_SHARED_LIBS=ON"

	* `autotools.bbclass`_ & `autotools-brokensep.bbclass`_
		| May be used to simplify building of autools based projects. Typical recipes that
		| build projects with autotools as build system have something like such.

		.. code-block:: bash

			PACKAGECONFIG[curses] = "--enable-curses,--disable-curses,ncurses"

			PACKAGECONFIG ??= "curses"

			inherit autools autotools-brokensep pkgconfig

	* `base.bbclass`_
		| Not something you typically need to ``inherit`` directly.
		| Contains implementation of ``oe_runmake``.

.. _core-image-base.bb: https://git.openembedded.org/openembedded-core/tree/meta/recipes-core/images/core-image-base.bb
.. _core-image.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/core-image.bbclass
.. _native.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/native.bbclass
.. _nativesdk.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/nativesdk.bbclass
.. _pkgconfig.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/pkgconfig.bbclass
.. _meson.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/meson.bbclass
.. _cmake.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/cmake.bbclass
.. _autotools.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/autotools.bbclass
.. _autotools-brokensep.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-recipe/autotools-brokensep.bbclass
.. _base.bbclass: https://git.openembedded.org/openembedded-core/tree/meta/classes-global/base.bbclass
