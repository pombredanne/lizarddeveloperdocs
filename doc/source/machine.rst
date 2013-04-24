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

Github has a great page about `getting started with github
<https://help.github.com/articles/set-up-git>`_.

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


SSH: domain login via a config file + connecting from home
----------------------------------------------------------

To install sites on our servers, you need to connect to them with ssh. Most of
the servers you should have access to are hooked up to our active directory domain,
so you can log in with your Nelen & Schuurmans domain username::

    $ ssh reinout.vanrees@p-web-ws-00-d10.external-nens.local

.. note::

    If you cannot log in to a server or if you cannot run ``sudo`` and you
    think you should, talk to Daniël.

Your local username is probably not the same as your domain username and
adding ``your.username@`` in front of every host is a pain. Luckily you can
automate this with a few lines. Edit ``~/.ssh/config`` and add the following::

    Host *.external-nens.local
        User your.username

    Host ssh.lizard.net
        User your.username

That ``ssh.lizard.net`` server is the only one you can connect to from outside
the office. From there, you can ssh to the other servers. Domain login works
on this one, too.

**Extra** To make your life easier, you can create an ssh key. This way you
don't need to log in every time: ssh transparently logs you in based on your
local secret key and your public key on the server. See github's documentation
on `how to set up an ssh key
<https://help.github.com/articles/generating-ssh-keys#step-2-generate-a-new-ssh-key>`_.

For every server, you need to do a one-time-only action: copy your new public
key to a server::

    $ ssh-copy-id p-web-ws-00-d10.external-nens.local

After that, you can ssh to that server without needing a password. If you want
the same luxury when connecting from home through ``ssh.lizard.net``, adjust
``~/.ssh/config`` by adding ``ForwardAgent yes``, this forwards your "ssh
agent connection" which is the one managing your keys. So it looks like this
then::

    Host ssh.lizard.net
        User your.username
        ForwardAgent yes


PostgreSQL setup
----------------

Most of the time, we use the PostgreSQL database. If you install the list of
:ref:`sec_osdependencies`, PostgreSQL and postgis are included. After that,
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

  On a really really new ubuntu, you get 2.0 instead of 1.5 postgis. The first
  line then has to become::

     POSTGIS_SQL_PATH=`pg_config --sharedir`/contrib/postgis-2.0

- Still logged in as postgres, create a "buildout" user that can create
  databases::

     createuser --createdb --no-createrole --no-superuser --pwprompt buildout

  Note: use the password "buildout".

Now you're set. For every new application/site that you want to run you'll
need to create a database, of course::

    $ sudo su postgres
    $ createdb --template=template_postgis --owner=buildout <db_name>

.. note::

    For some versions of Django, there is a problem with postgres and
    psycopg2. You get an error like::

      DatabaseError: invalid byte sequence for encoding "UTF8": 0x00

    To fix it, edit ``/etc/postgresql/9.1/main/postgresql.conf`` and uncomment
    ``standard_conforming_strings`` and set it to "off".
