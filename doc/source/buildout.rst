Python and buildout: setting up code
====================================

Dependencies
------------

On your :doc:`machine`, you'll need to install a couple of ubuntu
packages. Here's a copied list, install it with ``sudo apt-get install
....``. It assumes, mostly, the latest 12.04 ubuntu "long term support"
release. The list really should be taken from a canonical source (debian
package, chef script, whatever), but for now this is a good basis::

    $ sudo apt-get install \
      binutils \
      build-essential \
      bzr \
      coffeescript \
      curl \
      emacs23-nox \
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

Mapnik-on-latest-ubuntu problem
-------------------------------

As luck would have it, this ubuntu version includes mapnik 2.0; we still need
0.7.x. There's nothing you can do at the moment apart from compiling it from
source. We're working on a handy backported ubuntu package, though.

First, some more ``sudo apt-get install`` work::

    $ sudo apt-get install \
    clang \
    cpp \
    g++ \
    libboost-dev \
    libboost-filesystem-dev \
    libboost-iostreams-dev \
    libboost-program-options-dev \
    libboost-python-dev \
    libboost-regex-dev \
    libboost-system-dev \
    libboost-thread-dev \
    libcairo2 \
    libcairo2-dev \
    libcairomm-1.0-1 \
    libcairomm-1.0-dev \
    libfreetype6 \
    libfreetype6-dev \
    libgdal1-dev \
    libgeotiff-dev \
    libicu-dev \
    libjpeg-dev \
    libltdl-dev \
    libltdl7 \
    libpng-dev \
    libproj-dev \
    libsqlite3-dev \
    libtiff-dev \
    libtiffxx0c2 \
    libxml2 \
    libxml2-dev \
    postgresql-9.1 \
    postgresql-9.1-postgis \
    postgresql-contrib-9.1 \
    postgresql-server-dev-9.1 \
    python-cairo \
    python-cairo-dev \
    python-dev \
    python-gdal \
    python-nose \
    python-software-properties \
    ttf-dejavu \
    ttf-dejavu-core \
    ttf-dejavu-extra \
    ttf-unifont

As root, you'll need to download, compile and install mapnik::

    $ sudo su
    $ cd /root
    $ git clone https://github.com/mapnik/mapnik.git
    $ cd mapnik
    $ git checkout 0.7.x
    $ python scons/scons.py configure PREFIX=/usr
    $ python scons/scons.py
    $ python scons/scons.py install

The ``PREFIX`` is important, otherwise you'll need to hand-copy some ``.so``
files around...


Buildout
--------

We use buildout to manage our projects. For every project, you need to collect
several python dependencies (with the right versions). See
http://reinout.vanrees.org/weblog/2010/04/14/buildout.html for a good
introduction.

The dependencies are specified in python's ``setup.py`` files. Every lizard
app and every lizard site has one. See
http://reinout.vanrees.org/weblog/2010/02/22/packaging-with-setuptools.html
for a good introduction.

In fact, read the whole of Reinout's blog entries `about software releases
<http://reinout.vanrees.org/weblog/tags/softwarereleasesseries.html>`_ to get
a good feel for Nelen & Schuurmans' software release setup. But you'll have to
replace "svn" with "git" when reading it.


Nensskel
--------

The last of that software releases series is about `software skeletons
<http://reinout.vanrees.org/weblog/2010/07/30/skeleton.html>`_. Our software
skeleton generator is nensskel: http://pypi.python.org/pypi/nensskel .

With it, you can generate a complete python library or a lizard app or a
lizard site, ready to start working. We made it because there are a lot of
moving parts in a python project. A ``setup.py``, a buildout configuration,
etcetera.


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


Making releases
---------------

TODO.

http://packages.lizardsystem.nl

Some on pypi.

All actual tagging and version-upping happens with `zest.releaser
<zestreleaser.readthedocs.org/>`_. See
http://reinout.vanrees.org/weblog/2010/02/24/zest.releaser-easy-tags.html for
a quick introduction.


Versions ("KGS")
----------------

Explain http://packages.lizardsystem.nl/kgs as used in our buildouts.
