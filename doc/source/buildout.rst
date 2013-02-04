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
configs, for those ``extends = http://some.server/version12.cfg`` lines) in
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

``setup.py`` and buildout have a different goal. ``setup.py`` is for managing
your dependencies. What other packages does your package need? The basic rule
is **if you import from it, you depend on it**. So if you have an ``import
mapnik`` in your code, you need to have ``mapnik`` in your
``install_requires`` list in your ``setup.py``.


Versions and version pinning ("KGS")
------------------------------------

Buildout uses ``setup.py`` to install your package plus its dependencies, but
it also provides isolation. And it installs a bunch of other things like cron
jobs or nginx templates. And it pins your versions.

We depend on large set of packages published on the Python package index
(`PyPI <http://pypi.python.org>`_) and on our own custom package
server. Because buildout typically prefer the latest release for a given
package, it is easy to end up in a situation where an application ends up
pulling in a too-recent release that does not work well with other packages.

You can deal with this in two ways. Typically you should use both.

- You can set minimal and maximal versions in your ``setup.py``.

- You can pin versions exactly in buildout.

Set minumum or maximum versions in your package's ``setup.py`` if you know for
sure that it won't work with an older or newer version of some other
package. If you depend on lizard-ui and you need the ``do_something()``
function that was added in version 2.14, add ``'lizard-ui >= 2.14'`` to
``install_requires`` instead of just ``'lizard-ui'``.

Similarly if you know your code won't work with mapnik 2.0 or higher, add
``'mapnik < 2.0'``.

.. warning::

   Don't ever pin exact versions in your ``setup.py``. You cannot ever override
   that anymore ``'lizard-ui = 2.14'`` stays ``'lizard-ui = 2.14'``. The
   ``2.14.1`` bugfix release that you really really need dosn't match the requirement...

Your buildout, on the other hand, has the task to actually collect all the
necessary packages. For use in one specific project, so it is fine to pin
versions there. It is even **recommended**, as you won't be surprised half a
year later by a new version of some package. The buildout ideal is to have a
**repeatable build**. When a site hasn't been modified in a year and you
suddenly have to fix a bug, you ought to be able to just grab the buildout and
get the very same site with the very same package versions as you find on the
server.

In all our buildouts you'll find a "buildout-versions" extension that prints
unpinned versions at the end of the buildout run. The output looks like::

    Versions had to be automatically picked.
    The following part definition lists the versions picked:
    [versions]
    pbp.recipe.noserunner = 0.2.6

    # Required by:
    # collective.recipe.sphinxbuilder==0.7.0
    docutils = 0.10

You can copy/paste the reported pins to the ``[versions]`` part in the buildout.

For a lizard site, the number of dependencies is big. Pinning them all to
correct versions and especially updating them later is tricky. The solution is
to use what we call a **known good set**, or **KGS**.

The demo site (http://demo.lizard.net) contains the most-used lizard apps. And
it is well-tested. So the versions used there work well together. You can use
the versions of the demo site in your apps or sites. Look at
http://packages.lizardsystem.nl/kgs .

How does it work? The versions part of the demo site's buildout isn't in the
buildout itself, but in `a separate versions.cfg
<https://github.com/nens/demo/blob/master/versions.cfg>`_. Every time a
release is made of the demo site, a new KGS is made and put up on
http://packages.lizardsystem.nl/kgs .

In site buildouts, pick one of the specific KGSs, like
http://packages.lizardsystem.nl/kgs/3.1.23/versions.cfg . In lizard apps,
where you normally want to develop against the latest versions, use
http://packages.lizardsystem.nl/kgs/latest.cfg (which always points at the
latest one).


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

See http://doc.lizardsystem.nl/sites/packages.lizardsystem.nl/index.html for
the documentation on how packages.lizardsystem.nl works.

Some on pypi.

All actual tagging and version-upping happens with `zest.releaser
<zestreleaser.readthedocs.org/>`_. See
http://reinout.vanrees.org/weblog/2010/02/24/zest.releaser-easy-tags.html for
a quick introduction.
