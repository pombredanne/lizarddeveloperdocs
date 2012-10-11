Python and buildout: setting up code
====================================

Dependencies
------------

On your :doc:`machine`, you'll need to install a couple of ubuntu
packages. Here's a copied list, install it with ``sudo apt-get install
....``. It assumes, mostly, the latest 12.04 ubuntu "long term support"
release. The list really should be taken from a canonical source (debian
package, chef script, whatever), but for now this is a good basis::

    binutils
    build-essential
    bzr
    coffeescript
    curl
    emacs23-nox
    git
    lessc
    libjpeg-dev
    lynx-cur
    memcached
    mercurial
    nginx
    postgresql-9.1-postgis
    python-dev
    python-gdal
    python-imaging
    python-lxml
    python-matplotlib
    python-psycopg2
    python-pyproj
    python-pysqlite2
    python-scipy
    python-setuptools
    python-virtualenv
    spatialite-bin
    subversion
    unzip

Mapnik-on-latest-ubuntu problem
-------------------------------

As luck would have it, this ubuntu version includes mapnik 2.0; we still need
0.7.x. There's nothing you can do at the moment apart from compiling it from
source. We're working on a handy backported ubuntu package, though.

First, some more ``sudo apt-get install`` work::

    clang
    cpp
    g++
    libboost-dev
    libboost-filesystem-dev
    libboost-iostreams-dev
    libboost-program-options-dev
    libboost-python-dev
    libboost-regex-dev
    libboost-system-dev
    libboost-thread-dev
    libcairo2
    libcairo2-dev
    libcairomm-1.0-1
    libcairomm-1.0-dev
    libfreetype6
    libfreetype6-dev
    libgdal1-dev
    libgeotiff-dev
    libicu-dev
    libjpeg-dev
    libltdl-dev
    libltdl7
    libpng-dev
    libproj-dev
    libsqlite3-dev
    libtiff-dev
    libtiffxx0c2
    libxml2
    libxml2-dev
    postgresql-9.1
    postgresql-9.1-postgis
    postgresql-contrib-9.1
    postgresql-server-dev-9.1
    python-cairo
    python-cairo-dev
    python-dev
    python-gdal
    python-nose
    python-software-properties
    ttf-dejavu
    ttf-dejavu-core
    ttf-dejavu-extra
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



Nensskel
--------
