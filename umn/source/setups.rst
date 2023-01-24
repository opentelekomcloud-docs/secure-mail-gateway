Set up OPTIONS
==============

In order to be able to use the Secure Mail Gateway, the following setup
steps are necessary on the customer side (mail server or application of
the customer).

Smarthost
---------

The following DNS A Record must be stored as SMARTHOST in the mail server or
application:

.. code:: text

   otc-de-out.mms.t-systems-service.com

For the connection of systems from the Open Telekom Cloud to the SMARTHOST the
**Port 25** is available. (Other ports are not active on the secure mail
gateway for mail acceptance.)

Login-Data
----------

Authentication with username is now necessary to reduce the suspicioin of spam
and increase security through TLS authentication. Username will be provided by
Cloud Handling Support (contact: service@open-telekom-cloud.com).

**Customer costs**: ~23€/month. Will be invoiced via OTC..

T-Systems eMail Protect Pro (in case of incoming mail requirement)
------------------------------------------------------------------

This is a direct and full managed service from T-Systems (not an OTC service!)
available at: https://cloud.telekom.de/de/magenta-security/e-mail-protect-pro

| **Contact:** Available contacts: ``FMB Secure-E-Mail <secure-email@t-systems.com>``

| **Customer costs:** to be requested at Secure Mail team. Will not be invoiced by OTC.
