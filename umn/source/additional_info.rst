Additional information
======================

SPF - Sender Policy Framework
-----------------------------

If the customer has set up an SPF record for his email domains, the
customer must include the following DNS A record to his SPF record:

.. code:: text

   a:otc-de-spf.mms.t-systems-service.com

.. tip::

   The :term:`SPF` is a technique to make it more difficult to falsify the
   sender of an e-mail.


IP addresses contained in the A Record:

.. table::
   Table 1: SPF Record IPs

   +----------------------------------------+------------------+--------------------+
   | Servername                             | IP               | Typ                |
   +========================================+==================+====================+
   | otc-de-mta01.mms.t-systems-service.com | 80.158.29.159    | Good Gateway       |
   +----------------------------------------+------------------+--------------------+
   | otc-de-mta02.mms.t-systems-service.com | 80.158.29.232    | Good Gateway       |
   +----------------------------------------+------------------+--------------------+
   | otc-de-mta03.mms.t-systems-service.com | 80.158.30.229    | BAD Gateway        |
   +----------------------------------------+------------------+--------------------+


DNS - Configuration Sender Mail Domains
---------------------------------------

To ensure a proper mailhandling over the Secure Mail Gateway it is necessary to
create an MX Record or minimum an DNS A Record for the from customer used
sender mail domain. Ideally the MX Record should point to a mailserver which is
capable of receiving replies and :term:`NDR` for the used mail
domain.

.. tip::
   In case there is no mailserver available for the used sender mail
   domain, then the reply-to header could be added to the outgoing mails.
   With this header it is possible to reroute the reply and :term:`NDR`
   messages to an existing Mailbox (for example to an functional mailbox
   below an existing customer mail domain).
