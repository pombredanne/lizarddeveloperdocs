Comments on specific packages and versions
==========================================

Some packages are harder to install or need specific versions. Sometimes you
need a newer version than provided by ubuntu 12.04 LTS. Here are per-package
instructions.


.. _sec_mapnik07:

Mapnik-on-latest-ubuntu problem
-------------------------------

As luck would have it, ubuntu 12.04 LTS includes mapnik 2.0; we still need
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
files around... (Jacks test case: it all works as advertised without changes)


.. _sec_gdal19:

GDAL 1.9
--------

In some cases (for instance the *WaterSchadeSchatter*) you need GDAL
1.9.x. A complication: once you installed 1.9, python-pyproj will give a
segmentation fault.

First install GDAL 1.9.x::

    $ sudo aptitude install python-software-properties
    $ sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable
    $ sudo aptitude update
    $ sudo aptitude upgrade gdal-bin

Afterwards you need to install a new pyproj from pypi. This overrides ubuntu's version::

    $ sudo apt-get install python-pip
    $ sudo pip install pyproj
