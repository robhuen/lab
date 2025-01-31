.. author:: Felix Förtsch <https://felixfoertsch.de>

.. tag:: lang-nodejs
.. tag:: web

.. highlight:: console

.. sidebar:: Logo

  .. image:: _static/images/mailtrain.png
      :align: center

#########
Mailtrain
#########

.. tag_list::

Mailtrain_ is a self-hosted open-source newsletter app built on top of `Nodemailer <https://nodemailer.com/>`_.

.. error::

  This guide seems to be **broken** for the current versions of Mailtrain, we would be
  happy if you want to work on a solution and create a Pull Request.
  See also the related issue: https://github.com/Uberspace/lab/issues/1026


----

.. note:: For this guide you should be familiar with the basic concepts of

  * :manual:`Node.js <lang-nodejs>` and its package manager :manual_anchor:`npm <lang-nodejs.html#npm>`
  * :manual:`MySQL <database-mysql>`
  * :manual:`supervisord <daemons-supervisord>`
  * :manual:`domains <web-domains>`

License
=======

Mailtrain_ is released under the `GPL v3.0`_.

Prerequisites
=============

This guide uses :manual:`Node.js <lang-nodejs>` version 12, which is the `default <https://manual.uberspace.de/lang-nodejs.html#standard-version>`_ at  at the moment.

Set up your URL:

.. include:: includes/web-domain-list.rst

.. include:: includes/my-print-defaults.rst

Installation
============

Clone the GitHub_ repository:

.. code-block:: console

  [isabell@stardust ~]$ git clone git://github.com/Mailtrain-org/mailtrain.git
  [isabell@stardust ~]$


Install the required dependencies:

.. code-block:: console

  [isabell@stardust ~]$ cd mailtrain
  [isabell@stardust mailtrain]$ npm install --production
  [isabell@stardust mailtrain]$

Configuration
=============

Database Setup
--------------

Create a new database:

.. code-block:: console

  [isabell@stardust mailtrain]$ mysql -e "CREATE DATABASE ${USER}_mailtrain;"
  [isabell@stardust mailtrain]$

Mailtrain Config
----------------

Copy the example config file:

.. code-block:: console

  [isabell@stardust mailtrain]$ cp config/default.toml config/production.toml
  [isabell@stardust mailtrain]$

Update ``production.toml`` with your MySQL settings; look for the ``[mysql]`` block:

.. code-block:: console

  ...

  [mysql]
  host="localhost"
  user="isabell"
  password="MySuperSecretPassword"
  database="isabell_mailtrain"

  ...

Web Backend Config
------------------

.. note::

    Mailtrain_ is running on port 3000.

.. include:: includes/web-backend.rst

Supervisord Daemon Setup
------------------------

Create ``~/etc/services.d/mailtrain.ini`` with the following content:

.. code-block:: ini

  [program:mailtrain]
  directory=%(ENV_HOME)s/mailtrain/
  command=env NODE_ENV=production /bin/node index.js
  autostart=yes
  autorestart=yes

.. include:: includes/supervisord.rst

If it's not in state RUNNING, check your configuration.

Login and Change Admin Credentials
----------------------------------

.. warning:: Change the default admin credentials to prevent unauthorized access of your data!

Your Mailtrain_ installation should now be reachable on ``https://isabell.uber.space``. Log in with the username ``admin`` and the password ``test``.

Go to ``https://isabell.uber.space/users/account`` and change your email address as well as your password.

.. note:: It is not possible to change the username in the GUI. If you want to change the default username ``admin`` to something else or add additional users, you have to do it directly in the database.


Finishing installation
======================

.. warning:: We do not allow mass mailings from our servers (see the `Uberspace House Rules <https://dashboard.uberspace.de/register?lang=en>`_). However, you can use Mailtrain_ as the admin interface for your mailing needs. Use the SMTP services from `AWS SES <https://aws.amazon.com/ses/>`_, `Sendgrid <https://sendgrid.com/>`_, `Mailgun <https://www.mailgun.com/>`_, etc. for the actual mailing.

Go to ``https://isabell.uber.space/settings``. In the **General Settings** section change the **Service Address (URL)** to ``https://isabell.uber.space/``.

In the **Mailer Settings** section change the following entries according to the documentation of your mailing service:

  * *Hostname*,
  * *Port*,
  * *Encryption*,
  * *username*,
  * *password* and
  * test your settings by pressing the Button **Check Mailer Config**.

Best Practices
==============

* Test the configuration by creating a new list and subscribing yourself to it.
* Craft your campaign with love and dedication.
* Don't spam users that don't want your newsletter.


----

Tested on Uberspace v7.7.0 with NodeJS v12 and MariaDB 10.3.23.

.. author_list::

.. _Mailtrain: https://mailtrain.org/
.. _GitHub: https://github.com/Mailtrain-org/mailtrain
.. _README: https://github.com/Mailtrain-org/mailtrain/blob/master/README.md
.. _GPL v3.0: https://github.com/Mailtrain-org/mailtrain/blob/master/LICENSE
