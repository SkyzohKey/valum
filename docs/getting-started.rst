Quickstart
==========

Assuming that Valum is built and installed correctly (view :doc:`installation`
for more details), you are ready to create your first application!

Unless you have installed Valum with ``--prefix=/usr`` or obtained it from your
distribution, you might have to export both ``PKG_CONFIG_PATH`` and
``LD_LIBRARY_PATH`` environment variables:

.. code-block:: bash

    export LD_LIBRARY_PATH=/usr/local/lib
    export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig

Some distributions store 64-bit libraries in a separate folder, typically
``lib64``.

Simple 'Hello world!' application
---------------------------------

You can use this sample application and project structure as a basis. The full
`valum-framework/example`_ is available on GitHub and is kept up-to-date with
the latest changes in the framework.

.. _valum-framework/example: https://github.com/valum-framework/example

::

    using Valum;
    using VSGI;

    var app = new Router ();

    app.get ("/", (req, res) => {
        return res.expand_utf8 ("Hello world!");
    });

    Server.new_with_application ("http", "org.valum.example.App", app.handle).run ({"app", "--port", "3003"});

Typically, the ``run`` function contains CLI argument to make runtime the
parametrizable.

It is suggested to use the following structure for your project, but you can do
pretty much what you think is the best for your needs.

::

    build/
    src/
        app.vala

Building with valac
-------------------

Simple applications can be built directly with ``valac``:

.. code-block:: bash

    valac --pkg=valum-0.3 -o build/app src/app.vala

The ``vala`` program will build and run the produced binary, which is
convenient for testing:

.. code-block:: bash

    vala --pkg=valum-0.3 src/app.vala

Building with waf
-----------------

It is preferable to use a build system like `waf`_ to automate all this
process. Get a release of ``waf`` and copy this file under the name ``wscript``
at the root of your project.

.. _waf: https://code.google.com/p/waf/

.. code-block:: python

    def options(cfg):
        cfg.load('compiler_c')

    def configure(cfg):
        cfg.load('compiler_c vala')
        cfg.check_cfg(package='valum-0.3', uselib_store='VALUM', args='--libs --cflags')

    def build(bld):
        bld.load('compiler_c vala')
        bld.program(
            packages = 'valum-0.3',
            target   = 'app',
            source   = 'src/app.vala',
            use      = 'VALUM')

You should now be able to build by issuing the following commands:

.. code-block:: bash

    ./waf configure
    ./waf build

Building with Meson
-------------------

`Meson`_ is highly-recommended for its simplicity and expressiveness. It's not
as flexible as waf, but it will handle most projects very well.

.. _Meson: http://mesonbuild.com/

.. code-block:: python

    project('example', 'c', 'vala')

    valum = dependency('valum-0.3')

    executable('app', sources: ['src/app.vala'], dependencies: valum)

.. code-block:: bash

    meson . build
    ninja -C build

Running the example
-------------------

VSGI produces process-based applications that are either self-hosted or able to
communicate with a HTTP server according to a standardized protocol.

The :doc:`vsgi/server/http` implementation is self-hosting, so you just have to
run it and point your browser at http://127.0.0.1:3003 to see the result.

.. code-block:: bash

    ./build/app
