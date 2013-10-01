Comments on specific packages and versions
==========================================

Some packages are harder to install or need specific versions. Sometimes you
need a newer version than provided by ubuntu 12.04 LTS. Here are per-package
instructions.


.. _sec_mapnik07:

Mapnik-on-latest-ubuntu problem
-------------------------------

** Needs updating **
As luck would have it, ubuntu 12.04 LTS includes mapnik 2.0; we still need
0.7.x. We're made on a handy backported ubuntu package, though. (For details
on how we created the package, see https://github.com/nens/deb-packages).

Instructions on how to get it working are here:
http://packages.lizardsystem.nl/ubuntu/precise64/

Lastly, install mapnik::

    $ sudo apt-get install python-mapnik


Mapnik 2.2.0
------------

Some packages need mapnik 2.2.0. Install mapnik 2.2.0 using the following
steps::

    $ sudo apt-get purge libmapnik* mapnik-utils python-mapnik
    $ sudo apt-get remove libmapnik* mapnik-utils python-mapnik
    $ sudo apt-get autoremove
    $ sudo add-apt-repository ppa:mapnik/v2.2.0
    $ sudo apt-get update
    $ sudo apt-get install libmapnik mapnik-utils python-mapnik

See also::

- https://github.com/mapnik/mapnik/wiki/Mapnik2_Changes
- https://github.com/mapnik/mapnik/wiki/API-changes-between-v2.0-and-v2.1
- https://github.com/mapnik/mapnik/wiki/Api-changes-between-v2.1-and-v2.2


.. _sec_gdal19:


Installation of GDAL 1.9.x
--------------------------

The new features of gdal 1.9.x are increasingly used in websites,
including the *waterschadeschatter*, the *3di wms server* and the
*ror export server*. To install gdal 1.9.x, add the ubuntugis-stable
repository to the list of software sources::

    $ sudo apt-get install python-software-properties
    $ sudo add-apt-repository ppa:ubuntugis/ppa --yes

    $ sudo apt-get update
    $ sudo apt-get upgrade python-gdal

.. note::

   The mapnik instructions above *also* require you to use the
   ubuntugis-stable, btw, so that's fine.

There have been problems with pyproj causing segmentation faults. To
avoid, do not install python-pyproj via apt-get, but use easy_install
to get the latest version from pypi, or even better, add pyproj to the
buildout configurateion on a per-project basis.

Make sure you have at least version 1.9::

    >>> import osgeo.gdal
    >>> osgeo.gdal.__version__
    '1.9.1'

Some segmentation faults have been reported during using some combination
of pyproj, spatialite and gdal 1.9.x, but no specifics were given.



python-dateutil
---------------

With python-dateutil you can get an error like this::

    Error: There is a version conflict.
    We already have: python-dateutil 1.4.1
    but celery 3.0.11 requires 'python-dateutil>=1.5,<2.0'.

The problem is that the machine in question hasn't been updated to ubuntu
``12.04 LTS`` yet and has an old python-dateutil. And dateutil gets picked up
from the syseggs because matplotlib, which we use as a sysegg,
provides/installs python-dateutil.

The solution is threefold. First install python-dateutil 1.5 manually on the
server. Note that this doesn't break matplotlib, as far as we know::

    $ sudo pip install python-dateutil==1.5

Second add python-dateutil to your site's setup.py::

    install_requires = [
        ...
        'python-dateutil',
        ...
        ],

Third add python-dateutil to your sysegg part in the ``development.cfg``
buildout configuration file. Above matplotlib, otherwise it won't get picked
up reliably::

    [sysegg]
    # Add eggs here that are best handled through OS-level packages.
    recipe = osc.recipe.sysegg
    force-sysegg = true
    eggs =
        python-dateutil
        matplotlib
        ...

After you've done this, re-run buildout on the server. You ought to see a
comforting line like ``sysegg: Using
/usr/local/lib/python2.7/dist-packages/python_dateutil-1.5-py2.7.egg for
python-dateutil`` somewhere in the output.


Newer Numpy version for pandas
------------------------------

The pandas version we use needs a newer numpy than available in ubuntu 12.04,
so we pin it in buildout (``1.6.2``, for instance, at the time of
writing) and remove it from the ``[sysegg]`` part.

Numpy builds correctly and installs itself into buildout's egg
cache. **Problem:** it isn't picked up by pandas' elaborately mangled
``setup.py`` script.

The solution is to do a one-time move trick::

    $ cd /usr/lib/pymodules/python2.7
    $ sudo mv numpy numpy_orig
    $ sudo ln -s /home/buildout/.buildout/eggs/numpy-1.6.2-py2.7-linux-x86_64.egg/numpy

Then run your buildout as you normally would. This creates the pandas egg that
will use the buildout-provided numpy just fine.

As it is only the install that needed the move trick, we can restore the
proper situation again::

    $ cd /usr/lib/pymodules/python2.7
    $ sudo rm numpy
    $ sudo mv numpy_orig numpy
