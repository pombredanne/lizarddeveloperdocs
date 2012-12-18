Development machine
===================


Use ubuntu
----------

Our basic assumption: **you develop on ubuntu**.

- Windows is pretty much out, too many things are hard to install. For
  instance, symlinks don't work unless you have special tools
  installed. Solution: use vagrant (see below).

- OSX is possible, but in practice collecting all the dependencies is
  hard. Plain python development is fine, but once you need mapnik, scipy,
  pyproj and postgresql bindings... See `Reinout's blog entry
  <http://reinout.vanrees.org/weblog/2012/09/18/vagrant.html>`_ for some notes
  on OSX' problems. Solution: use vagrant (see below).


Vagrant
-------

If you use windows or OSX as your regular machine, the solution is to run
ubuntu inside a virtualbox virtual machine, managed using `vagrant
<http://vagrantup.com/>`_. Note that this might also be a good idea on ubuntu
itself, but the need is less big there.

Go to the `vagrant site <http://vagrantup.com/>`_, read the quick install
guide, download virtualbox and try it out. Perhaps `Reinout's setup
<http://reinout.vanrees.org/weblog/2012/10/30/vagrant-osx-how.html>`_ gives
you some inspiration on how to set it up.


Checkouts, git, github
----------------------

Most of lizard's development takes place on github: see
https://github.com/lizardsystem/ . There's some utility stuff on
https://github.com/nens/, but all the lizard apps are in the main one.

(The lizard *sites*, which might include customer data and passwords, are in
https://github.com/nens/ in private repositories).

Access?

- You can fork everything, it is open source.

- You can request access if you need it and if you've got some work to
  show. (So: fork first). Ask https://github.com/reinout or
  https://github.com/remcogerlich . (If you're a developer at N&S you already
  ought to have access).

In the end, you'll end up with lots of different git checkouts. A tool
suggestion to help you with it: `checkoutmanager
<http://pypi.python.org/pypi/checkoutmanager>`_.

And, yes, most of it is on github, which means you'll need to learn Git, which
takes some getting used to. But it is The New Hotness and Github is great, so
you'll just need to dive in. Google is your friend. Let someone show you what
you can do with git and how it works and you'll know enough about git to get
you into trouble. You'll also know enough to google for the relevant terms to
get you out of the trouble again.


PostgreSQL setup
----------------

Most of the time, we use the PostgreSQL database. If you install the list of
:ref:`sec_osdependencies`, PostgreSQL and postgis is included. After that,
there are some configuration tasks that need doing.

- Modify ``/etc/postgresql/9.1/main/pg_hba.conf`` so that the ``local all all
  ident`` line looks like ``local all all md5``.

- Become the postgres user (``sudo su postgres``) and run the following
  commands::

    POSTGIS_SQL_PATH=`pg_config --sharedir`/contrib/postgis-1.5
    createdb -E UTF8 template_postgis # Create the template spatial database.
    createlang -d template_postgis plpgsql # Adding PLPGSQL language support.
    psql -d postgres -c "UPDATE pg_database SET datistemplate='true' WHERE datname='template_postgis';"
    psql -d template_postgis -f $POSTGIS_SQL_PATH/postgis.sql
    psql -d template_postgis -f $POSTGIS_SQL_PATH/spatial_ref_sys.sql
    psql -d template_postgis -c "GRANT ALL ON geometry_columns TO PUBLIC;"
    psql -d template_postgis -c "GRANT ALL ON spatial_ref_sys TO PUBLIC;"

  New system:
    POSTGIS_SQL_PATH=`pg_config --sharedir`/contrib/postgis-2.0

- Still logged in as postgres, create a "buildout" user that can create
  databases::

   $ createuser --createdb --no-createrole --no-superuser --pwprompt buildout

Note: use the password "buildout".

Now you're set. For every new application/site that you want to run you'll
need to create a database, of course.

  $ sudo su postgres
  $ createdb --template=template_postgis --owner=buildout <db_name>

For some versions of Django, there is a problem with postgres and
psycopg2. Solve "DatabaseError: invalid byte sequence for encoding "UTF8":
0x00":

  $ sudo emacs /etc/postgresql/9.1/main/postgresql.conf

Uncomment standard_conforming_strings and set it to "off".
