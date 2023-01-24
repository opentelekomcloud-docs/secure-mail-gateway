==========================================
Installation examples / basic installation
==========================================

Basic installation
==================

Examples Postfix Ubuntu
-----------------------

(Source: `ubuntu postfix documentation <https://help.ubuntu.com/lts/serverguide/postfix.html>`_)

Installation
~~~~~~~~~~~~

To install postfix run the following command:

.. code:: console

   sudo apt install postfix

Simply press return when the installation process asks questions, the
configuration will be done in greater detail in the next stage.

Basic Configuration
~~~~~~~~~~~~~~~~~~~

To configure postfix, run the following command:

.. code:: console

   sudo dpkg-reconfigure postfix

The user interface will be displayed. On each screen, select the following
values:

.. code:: text

   1. Internet Site
   2. mail.example.com
   3. steve
   4. mail.example.com, localhost.localdomain, localhost
   5. No
   6. 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 192.168.0.0/24
   7. 0
   8. +
   9. all

Replace ``mail.example.com`` with the domain for which you'll accept email,
``192.168.0.0/24`` with the actual network and class range of your mail server, and
steve with the appropriate username.

Now is a good time to decide which mailbox format you want to use. By default
Postfix will use mbox for the mailbox format. Rather than editing the
configuration file directly, you can use the postconf command to configure all
postfix parameters. The configuration parameters will be stored in
``/etc/postfix/main.cf`` file. Later if you wish to re-configure a particular
parameter, you can either run the command or change it manually in the file.

To configure the mailbox format for Maildir:

.. code:: console

   sudo postconf -e 'home_mailbox = Maildir/'

This will place new mail in ``/home/username/Maildir`` so you will need to
configure your :term:`MDA` to use the same path.

SMTP Authentication
~~~~~~~~~~~~~~~~~~~

SMTP-AUTH allows a client to identify itself through an authentication
mechanism (SASL). :term:`TLS` should be used to
encrypt the authentication process. Once authenticated the SMTP server
will allow the client to relay mail.

#. Configure Postfix for SMTP-AUTH using SASL (Dovecot SASL):

   .. code:: console

      sudo postconf -e 'smtpd_sasl_type = dovecot'
      sudo postconf -e 'smtpd_sasl_path = private/auth'
      sudo postconf -e 'smtpd_sasl_local_domain ='
      sudo postconf -e 'smtpd_sasl_security_options = noanonymous'
      sudo postconf -e 'broken_sasl_auth_clients = yes'
      sudo postconf -e 'smtpd_sasl_auth_enable = yes'
      sudo postconf -e 'smtpd_recipient_restrictions = \\
        permit_sasl_authenticated,permit_mynetworks,reject_unauth_destination'

   The ``smtpd_sasl_path`` configuration is a path relative to the Postfix
   queue directory.

#. Next, generate or obtain a digital certificate for :term:`TLS`. See
   Certificates for details. This example also uses a Certificate Authority
   (CA). For information on generating a CA certificate see Certification
   Authority.

   .. note::

      MUAs connecting to your mail server via :term:`TLS` will need to recognize
      the certificate used for :term:`TLS`. This can either be done using a
      certificate from a commercial CA or with a self-signed certificate
      that users manually install/accept. For MTA to MTA :term:`TLS` certficates
      are never validated without advance agreement from the affected
      organizations. For MTA to MTA :term:`TLS`, unless local policy requires it,
      there is no reason not to use a self-signed certificate. Refer to
      `Creating a Self-Signed
      Certificate <https://help.ubuntu.com/lts/serverguide/certificates-and-security.html#creating-a-self-signed-certificate>`__
      for more details.

#. Once you have a certificate, configure Postfix to provide :term:`TLS`
   encryption for both incoming and outgoing mail:

   .. code:: console

      sudo postconf -e 'smtp_tls_security_level = may'
      sudo postconf -e 'smtpd_tls_security_level = may'
      sudo postconf -e 'smtp_tls_note_starttls_offer = yes'
      sudo postconf -e 'smtpd_tls_key_file = /etc/ssl/private/server.key'
      sudo postconf -e 'smtpd_tls_cert_file = /etc/ssl/certs/server.crt'
      sudo postconf -e 'smtpd_tls_loglevel = 1'
      sudo postconf -e 'smtpd_tls_received_header = yes'
      sudo postconf -e 'myhostname = mail.example.com'

#. If you are using your own Certificate Authority to sign the
   certificate enter:

   .. code:: console

      sudo postconf -e 'smtpd_tls_CAfile = /etc/ssl/certs/cacert.pem'

   .. note::

      Again, for more details about certificates see `Certificates
      <https://help.ubuntu.com/lts/serverguide/certificates-and-security.html>`_.

After running all the commands, Postfix is configured for SMTP-AUTH and
a self-signed certificate has been created for :term:`TLS` encryption.

Now, the file ``/etc/postfix/main.cf`` should look like this:

.. code:: linux-config

   # See /usr/share/postfix/main.cf.dist for a commented, more complete
   # version
   smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)
   biff = no
   # appending .domain is the MUA's job.
   append_dot_mydomain = no
   # Uncomment the next line to generate "delayed mail" warnings
   #delay_warning_time = 4h
   myhostname = server1.example.com
   alias_maps = hash:/etc/aliases
   alias_database = hash:/etc/aliases
   myorigin = /etc/mailname
   mydestination = server1.example.com, localhost.example.com, localhost
   relayhost =
   mynetworks = 127.0.0.0/8
   mailbox_command = procmail -a "$EXTENSION"
   mailbox_size_limit = 0
   recipient_delimiter = +
   inet_interfaces = all
   smtpd_sasl_local_domain =
   smtpd_sasl_auth_enable = yes
   smtpd_sasl_security_options = noanonymous
   broken_sasl_auth_clients = yes
   smtpd_recipient_restrictions =
   permit_sasl_authenticated,permit_mynetworks,reject \_unauth_destination
   smtpd_tls_auth_only = no
   smtp_tls_security_level = may
   smtpd_tls_security_level = may
   smtp_tls_note_starttls_offer = yes
   smtpd_tls_key_file = /etc/ssl/private/smtpd.key
   smtpd_tls_cert_file = /etc/ssl/certs/smtpd.crt
   smtpd_tls_CAfile = /etc/ssl/certs/cacert.pem
   smtpd_tls_loglevel = 1
   smtpd_tls_received_header = yes
   smtpd_tls_session_cache_timeout = 3600s
   tls_random_source = dev:/dev/urandom

The postfix initial configuration is complete. Run the following command
to restart the postfix daemon:

.. code:: console

   sudo systemctl restart postfix.service

Postfix supports SMTP-AUTH as defined in RFC2554. It is based on SASL.
However it is still necessary to set up SASL authentication before you
can use SMTP-AUTH.

Example EXIM Ubuntu
-------------------

(Source: `ubuntu exim documentation <https://help.ubuntu.com/lts/serverguide/exim4.html>`_)

Installation
~~~~~~~~~~~~

To install exim4, run the following command:

.. code:: console

   sudo apt install exim4

Configuration
~~~~~~~~~~~~~

To configure Exim4, run the following command:

.. code:: console

   sudo dpkg-reconfigure exim4-config

The user interface will be displayed. The user interface lets you configure
many parameters. For example, In Exim4 the configuration files are split among
multiple files. If you wish to have them in one file you can configure
accordingly in this user interface.

All the parameters you configure in the user interface are stored in
``/etc/exim4/update-exim4.conf.conf`` file. If you wish to re-configure, either
you re-run the configuration wizard or manually edit this file using your
favorite editor. Once you configure, you can run the following command to
generate the master configuration file:

.. code:: console

   sudo update-exim4.conf

The master configuration file, is generated and it is stored in
``/var/lib/exim4/config.autogenerated``.

At any time, you should not edit the master configuration file,
``/var/lib/exim4/config.autogenerated`` manually. It is updated automatically
every time you run ``update-exim4.conf``

You can run the following command to start Exim4 daemon.

.. code:: console

   sudo systemctl start exim4.service

SMTP Authentication
~~~~~~~~~~~~~~~~~~~

This section covers configuring Exim4 to use SMTP-AUTH with :term:`TLS` and
SASL.

The first step is to create a certificate for use with :term:`TLS`. Enter the
following into a terminal prompt:

.. code:: console

   sudo /usr/share/doc/exim4-base/examples/exim-gencert

Now Exim4 needs to be configured for :term:`TLS` by editing
``/etc/exim4/conf.d/main/03_exim4-config_tlsoptions`` add the following:

.. code:: linux-config

   MAIN_TLS_ENABLE = yes

Next you need to configure Exim4 to use the saslauthd for
authentication. Edit ``/etc/exim4/conf.d/auth/30_exim4-config_examples`` and
uncomment the ``plain_saslauthd_server`` and ``login_saslauthd_server``
sections:

.. code:: linux-config

   plain_saslauthd_server:
     driver = plaintext
     public_name = PLAIN
     server_condition = ${if saslauthd{{$auth2}{$auth3}}{1}{0}}
     server_set_id = $auth2
     server_prompts = :
     .ifndef AUTH_SERVER_ALLOW_NOTLS_PASSWORDS
     server_advertise_condition = ${if eq{$tls_cipher}{}{}{*}}
     .endif

   #
   login_saslauthd_server:
     driver = plaintext
     public_name = LOGIN
     server_prompts = "Username:: : Password::"
     # don't send system passwords over unencrypted connections
     server_condition = ${if saslauthd{{$auth1}{$auth2}}{1}{0}}
     server_set_id = $auth1
     .ifndef AUTH_SERVER_ALLOW_NOTLS_PASSWORDS
     server_advertise_condition = ${if eq{$tls_cipher}{}{}{*}}
     .endif

Additionally, in order for outside mail client to be able to connect to new
``exim`` server, new user needs to be added into ``exim`` by using the
following commands.

.. code:: console

   sudo /usr/share/doc/exim4-base/examples/exim-adduser

Users should protect the new exim password files with the following
commands.

.. code:: console

   sudo chown root:Debian-exim /etc/exim4/passwd
   sudo chmod 640 /etc/exim4/passwd

Finally, update the Exim4 configuration and restart the service:

.. code:: console

   sudo update-exim4.conf
   sudo systemctl restart exim4.service

Customizations
==============

Postfix
-------

Source: https://wiki.ubuntuusers.de/Postfix/

Authentication on the smarthost
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

However, if the SMTP server on the smart host requires a password to
send the mail, the newly created configuration ``/etc/postfix/main.cf``
must be edited again and these lines inserted:

.. code:: linux-config

   smtp_sasl_auth_enable = yes
   # noplaintext leave, if passwords have to be transferred in plain text:
   # (not recommended, only if it does not work differently)
   smtp_sasl_security_options = noplaintext noanonymous
   smtp_sasl_password_maps = hash:/etc/postfix/sasl_password

As shown in the configuration file, Postfix retrieves the access data
from the file ``/etc/postfix/sasl_password`` or from a database that is
generated from the sasl_password. The file should preferably be created
with the following command, otherwise conversion into a database is not
always possible. You have to open a terminal
window\ `[2] <https://wiki.ubuntuusers.de/Postfix/#source-2>`__ and
enter the following command:

.. code:: console

   sudo touch /etc/postfix/sasl_password

Now you write your data according to the following pattern in the file

.. code:: text

   smtp.mailanbieter.de username:securepassword

So that not everyone can read the password, you should limit the
permissions of the file (possibly this ist o be repeated for backup
copies or the database created below):

.. code:: console

   sudo chmod 600 /etc/postfix/sasl_password

Now the database has to be created:

.. code:: console

   sudo postmap hash:/etc/postfix/sasl_password

Then you have to restart postfix:

.. code:: console

   sudo /etc/init.d/postfix restart

EXIM
----

Help Source: https://wiki.debian.org/Exim

Example-Source:
https://somoit.net/linux/linux-exim-authenticated-and-tls-mail-through-smarthost

If it is necessary to configure exim by editing the config file (instead of using
dpkg-reconfigure), these are the related values:

.. code:: linux-config

   dc_eximconfig_configtype='satellite'
   dc_smarthost='smtp.bilbokoudala.lan::587'

Configure credentials to authenticate
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Exim has a password file called ``passwd.client`` that allows configurin a list of
credentials associated to each smarthost. In debian 9, for example, the full path is
``/etc/exim4/passwd.client``

Edit the file to add the credentials

.. code:: linux-config

   # password file used when the local exim is authenticating to a remote
   # host as a client.
   #
   # see exim4_passwd_client(5) for more documentation
   #
   # Example:
   ### target.mail.server.example:login:password
   smtp.domain.com:smtpuser:smtppassword
