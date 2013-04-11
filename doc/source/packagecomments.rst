Comments on specific packages and versions
==========================================

Some packages are harder to install or need specific versions. Sometimes you
need a newer version than provided by ubuntu 12.04 LTS. Here are per-package
instructions.


.. _sec_mapnik07:

Mapnik-on-latest-ubuntu problem
-------------------------------

As luck would have it, ubuntu 12.04 LTS includes mapnik 2.0; we still need
0.7.x. We're made on a handy backported ubuntu package, though. (For details
on how we created the package, see https://github.com/nens/deb-packages).

Here are the instructions to get it working. First add our custom package
repository to the bottom of ``/etc/apt/sources.list``::

    deb http://packages.lizardsystem.nl/ubuntu/precise64/ ./

Then import the package verification key and update the package database::

    $ wget -q http://packages.lizardsystem.nl/ubuntu/public.gpg -O- | sudo apt-key add -
    $ sudo apt-get update

Lastly install mapnik::

    $ sudo apt-get install python-mapnik


.. _sec_gdal19:

GDAL 1.9
--------

In some cases (for instance the *WaterSchadeSchatter*) you need GDAL
1.9.x. A complication: once you installed 1.9, python-pyproj will give a
segmentation fault.

First install GDAL 1.9.x::

    $ sudo aptitude install python-software-properties

(choose stable or unstable)
    $ sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable
    $ sudo add-apt-repository ppa:ubuntugis/ppa

    $ sudo aptitude update
    $ sudo aptitude upgrade gdal-bin

Afterwards you need to install a new pyproj from pypi. This overrides ubuntu's version::

    $ sudo apt-get install python-pip
    $ sudo pip install pyproj


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
