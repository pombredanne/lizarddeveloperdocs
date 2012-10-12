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
guide, download virtualbox and try it out.


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