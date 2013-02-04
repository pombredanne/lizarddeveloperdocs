Python and buildout: setting up code
====================================

Note beforehand: there are two different kinds of dependencies.

- OS-level dependencies that need to be compiled or that are otherwise best
  handled with ubuntu packages.

- Regular Python packages. Buildout collects and installs those for us, unless
  they need too much compile work, in those cases we often use ubuntu
  packages.

.. _sec_osdependencies:


OS-level dependencies
---------------------

On your :doc:`machine`, you'll need to install a couple of ubuntu
packages. Here's a copied list, install it with ``sudo apt-get install
....``. It assumes, mostly, the latest 12.04 ubuntu "long term support"
release (called *precise pangolin*). The list really should be taken from a
canonical source (debian package, chef script, whatever), but for now this is
a good basis::

    $ sudo apt-get install \
      binutils \
      build-essential \
      bzr \
      coffeescript \
      curl \
      emacs23-nox \
      gettext \
      git \
      lessc \
      libjpeg-dev \
      lynx-cur \
      memcached \
      mercurial \
      nginx \
      postgresql-9.1-postgis \
      python-dev \
      python-gdal \
      python-imaging \
      python-lxml \
      python-matplotlib \
      python-psycopg2 \
      python-pyproj \
      python-pysqlite2 \
      python-scipy \
      python-setuptools \
      python-virtualenv \
      spatialite-bin \
      subversion \
      unzip

.. note::

   Some packages are harder to install or need specific versions or have
   special instructions.

   .. toctree::
      :maxdepth: 2

      packagecomments


Buildout
--------

We use buildout to manage our projects. For every project, you need to collect
several python dependencies (with the right versions). See
http://reinout.vanrees.org/weblog/2010/04/14/buildout.html for a good
introduction.

One handy (read: **very necessary**) time saver on your development machine:
create a ``.buildout/`` directory in your home dir and put eggs, downloads and
configs directories underneath that. Then add a ``default.cfg`` in that
``.buildout/`` directory::

    [buildout]
    eggs-directory = /home/reinout/.buildout/eggs
    download-cache = /home/reinout/.buildout/downloads
    extends-cache = /home/reinout/.buildout/configs

This tells buildout (ALL your buildouts) to store the eggs, downloads (and
configs, for those extends=http://some.zope.server/version12.cfg lines) in
those directories. Download once, use everywhere. Saves you a lot of time when
you have lots of similar projects.


Python dependencies ("setup.py")
--------------------------------

The dependencies are specified in python's ``setup.py`` files. Every lizard
app and every lizard site has one. See
http://reinout.vanrees.org/weblog/2010/02/22/packaging-with-setuptools.html
for a good introduction.

.. note::

    In fact, read the whole of Reinout's blog entries `about software releases
    <http://reinout.vanrees.org/weblog/tags/softwarereleasesseries.html>`_ to
    get a good feel for Nelen & Schuurmans' software release setup. But you'll
    have to replace "svn" with "git" when reading it.


Nensskel
--------

The last of that software releases series is about `software skeletons
<http://reinout.vanrees.org/weblog/2010/07/30/skeleton.html>`_. Our software
skeleton generator is nensskel: http://pypi.python.org/pypi/nensskel .

With it, you can generate a complete python library or a lizard app or a
lizard site, ready to start working. We made it because there are a lot of
moving parts in a python project. A ``setup.py``, a buildout configuration,
etcetera.

To create a new lizard site run::

  $ nensskel SITENAME -t nens_lizardsite

For a lizard app::

  $ nensskel lizard-APPNAME -t nens_djangoapp


Quality
-------

Another reason for nensskel, mentioned above, is to help you make quality
software.

- A test command is ready for you (``bin/test``) with a sample test. You only
  have to create more of them!

- There is already a ``README.rst``. So fill it in!

- If you need more documentation, the ``doc/`` directory is ready for you if
  you need to make more elaborate documentation, this uses `sphinx
  <http://sphinx.pocoo.org/>`_. Run sphinx with ``bin/sphinx``.

- There is already a ``CHANGES.rst`` where you can fill in major changes.

So: a lot is in place to help you write good software!


Usage statistics: gaug.es
-------------------------

Instead of google analytics, we use https://get.gaug.es at
Nelen&Schuurmans. Log in with username ``info@nelen-schuurmans.nl``.

- Add a new site (and set the time zone to Amsterdam).

- Click, for that new site, on "sharing" and add two users:
  ``gauges@nelen-schuurmans.nl`` and ``joep.grispen@nelen-schuurmans.nl``.

- CLick on "tracking code" and copy the "data-site-id" number::

      t.setAttribute('data-site-id', '50d02ecsdfsdf6f5a1cedsdf');

- Add that site ID to your ``settings.py`` (production site) or
  ``stagingsettings.py`` (staging site) as ``UI_GAUGES_SITE_ID``.



Making releases
---------------

To make a release we need a git tag first. To do this automatically run::

   $ bin/fullrelease

And answer the questions asked by fullrelease.
To release a release on a server for the first time::

  $ bin/fab staging init

To update a site::

  $ bin/fab staging update

TODO: More on releases and explain staging/production sites.

http://packages.lizardsystem.nl

Some on pypi.

All actual tagging and version-upping happens with `zest.releaser
<zestreleaser.readthedocs.org/>`_. See
http://reinout.vanrees.org/weblog/2010/02/24/zest.releaser-easy-tags.html for
a quick introduction.


Versions ("KGS")
----------------

We depend on large set of eggs (in the setuptools sense) published on PyPI
(pypi.python.org) and other indices. Because setuptools (and zc.buildout) will
typically prefer the latest release for a given package, it is easy to end up
in a situation where an application ends up pulling in a too-recent release
that does not work well with the other packages in the system.

This problem can be solved with pinning all releases to a "known good set".

Explain http://packages.lizardsystem.nl/kgs as used in our buildouts.
