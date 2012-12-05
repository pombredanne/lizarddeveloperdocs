Lizard dev docs project
==========================================


The intention of these docs is to help:

- New developers at Nelen & Schuurmans.

- Actually everyone wanting to work with `Lizard <http://lizard.org>`_.

The documentation is automatically build upon every push to github and shown
on readthedocs.org: https://lizard-developer-docs.readthedocs.org

The source code is at https://github.com/nens/lizarddeveloperdocs

`Reinout van Rees <http://reinout.vanrees.org>`_ wrote most of it (at least
initially) and he liberally sprayed it with pointers at his weblog. So feel
free to fix it up :-)


Update the online Lizard dev docs
---------------------------------

Just edit and commit the docs. The docs on readthedocs are automatically
updated via a github webhook.


Build the lizard dev docs
-------------------------

To build the documentation locally, do the familiar bootstrap/buildout dance
to set up the project::

    $ python bootstrap.py
    $ bin/buildout

Then you can actually build the documentation with::

    $ bin/sphinx

Sphinx, which we use for the documentation, is explained at
http://sphinx.pocoo.org/ .
