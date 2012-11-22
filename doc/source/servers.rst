Nelen & Schuurmans server setup
===============================

.. note::

    Note: this is Nelen & Schuurmans' setup. We use this developer
    documentation also for our internal developers, that's why this chapter is
    in here. If you don't work for us, it might still be useful for giving you
    ideas on how to set up your own infrastructure.


Serverinfo: which sites are where?
----------------------------------

Sometimes you don't know which site is hosted where. For that, look at
http://buildbot.lizardsystem.nl/serverinfo/ . The sites overview on that page
lists all sites. For instance,
http://buildbot.lizardsystem.nl/serverinfo/sites/test.demo.lizard.net.html
tells you that that site is hosted on s-web-ws-00-d3.external-nens.local.

If a site that you know exists is missing, here are some tips. Is it on a new
server? Perhaps serverinfo hasn't been installed there yet. Can you find any
of the other sites on that server, perhaps?

To find the actual servers proxied by http://proxy.lizard.net, go to http://proxy.lizard.net/allhosts.htm.

Some notes:

- Serverinfo is currently installed and set up by a fabric script or
  manually. We'll fix this up later via chef and/or debian packages.

- It runs from a cronjob. There might be a 20 minute delay between you setting
  up a new site and it showing up on the serverinfo page.

- The serverinfo code is, for historical reasons, in Reinout's github
  repository: https://github.com/reinout/serverinfo/


Incoming traffic routing
------------------------

So how does a request get from http://test.demo.lizard.net to some Django site
on the s-web-ws-00-d3.external-nens.local server?

- All ``*.lizard.net`` and ``*.lizardsystem.nl`` traffic (and some specific
  other ones) domains point at ``proxy.lizard.net``.

- The ``proxy.lizard.net`` runs apache. Its apache configuration proxies the
  requests to the backend hosts, like s-web-ws-00-d3.external-nens.local.

  Many of the regular sites are configured using the `apache proxy generator
  <https://github.com/nens/apacheproxygenerator>`_ with the
  ``/etc/apache2/generated_proxy.ini`` file.

- For https traffic, the proxy also has `pound <http://www.apsis.ch/pound>`_
  running on port 443. This one "terminates" the https connection and simply
  proxies all ``*.lizard.net`` traffic to the proxy's apache port as regular
  http traffic.

- On the actual backend servers, nginx is running. Lizard sites generate an
  nginx config file in their own ``etc/`` folder. These are symlinked into
  nginx's ``/etc/nginx/sites-available/`` folder. The nginx config file tells
  nginx to proxy a certain domainname to a Django listening on a local port.

  Note: some servers are using apache: the site with php (lizard.net), an old
  one with ancient lizard versions and the one with trac/svn.

- The sites themselves are running Django with `gunicorn
  <http://gunicorn.org/>`_ on some port. The port's number is defined in one
  place in the site's buildout configuration. This single number is used both
  to start gunicorn on the proper port *and* to put the right port number in
  the abovementioned symlinked nginx file.

**To recap**, a request goes to proxy.lizard.net. This proxies it to nginx on
the backend server. Nginx proxies it to a locally running gunicorn.
