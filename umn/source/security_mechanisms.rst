Security Mechanisms
===================

Good / Bad Gateway / Anti Spam
------------------------------

The Good / Bad Routing which redirects Spam Mails internally to the Bad Gateway
helps to ensure that the Good Gateways do not end up on external blacklists.
The Good / Bad Routing thus helps that the mails of the customers do not
influence each other.

.. table::

 +------+--------------------------------------------------------------------+
 | Good | If the mail is unremarkable, it will be sent via one of the Good   |
 |      | Gateways.                                                          |
 +------+--------------------------------------------------------------------+
 | Bad  | In the case of suspected spam, the mail is sent via one of the     |
 |      | Bad Gateways (higher probability of a blacklist.) If the Bad       |
 |      | Gateway is on a blacklist, it will be checked if this can be       |
 |      | repaired, but there are no guarantees.                             |
 +------+--------------------------------------------------------------------+

On the Secure Mail Gateway all mails are checked for spam. If Mails classified
as spam are redirected to the BAD Gateway within the Secure Mail Gateway
environment and sent to the Internet from there.

.. note::
   The Secure Mail Gateway: ``otc-de-mta03.mms.t-systems-service.com`` is the
   Bad Gateway. Only mails are sent via this Secure Mail Gateway if mails from
   the Open Telekom Cloud have already been classified as spam.  It is
   therefore possible that this system (Bad Gateway) will be listed under
   certain circumstances on "blacklists". Regular mails are not sent to the
   Internet via this bad gateway. If an :term:`NDR` message is
   sent from the Bad Gateway to the customer, it should be checked on the
   customer's side why this mail was classified as spam.

Virus check
-----------

On the Secure Mail Gateways all mails are checked for viruses. Mails
include a virus are rejected with a corresponding info and sent an
:term:`NDR` to the sender of the mail.

.. note::
   Virus scanning only takes place "outbound". The sender will be informed via
   info mail.

Blacklist Monitoring
--------------------

An automated monitoring the Blacklists by the Blacklist Provider has been set
up: the script runs daily at 5am. If a blocked list is detected, ``FMB
Change-OpenTelekomCloud (DD-OTC-SDM@telekom.de)`` will be informed; Removal
requests from blacklist by the Blacklist Providers are created immediately.
Depending on the processing time of the blacklist provider, this lock will be
removed accordingly.

Sender Domain Check
-------------------

The mail domain used to send mail from the Open Telekom Cloud must be a valid
public domain. This means that the domain must have a public MX record or at
least a public A record. Mails with sender addresses where these criteria are
not met are discarded.

.. note::
   Error message if rejected: 550 Sender Domain must resolve

Forced TLS
----------

Based on sender and recipient domain, a forced TLS communication can be set up.
For Mails where those setup is hitting the connection between the secure mail
Gateway and the external mailserver is mandatory encrypted via TLS. (In case or
error the incoming mail will be directly rejected or hold back, if a TLS
secured connection to the external mailserver can’t be established.)

DKIM signing
-------------

Based on sender domain a DKIM signing can be configured for outgoing mails.
(DKIM signing can be requested over `Cloud Handling Support <https://open-telekom-cloud.com/en/contact>`_ contact.)
