Outgoing Mobility Learning Agreements API
=========================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobility Learning Agreements API**.
This API is implemented by the sending institution. It allows the receiving HEI
to read and accept Learning Agreements stored on the sending HEI's servers
and propose changes to them.


Reminder on vocabulary
----------------------

Keep in mind that definitions of "sending HEI" and "receiving HEI" come from
the "mobility vocabulary", not the "HTTP vocabulary". In case of this
particular API this means that:

* **sending HEI == responding HEI** (HEI which is sending the student == HEI
  which implements the API, *receives* the HTTP request, and responds to it),
* **receiving HEI == requesting HEI** (HEI which is receiving the student ==
  HEI which implements the client, and *sends* the HTTP request).

As long as we use these terms consistently, there shouldn't be much confusion
though.


Security
--------

This version of this API uses [standard EWP Authentication and Security, Version 2][sec-v2].
Server implementers choose which security methods they
support by declaring them in their Manifest API entry.

This API handles data which is considered private. Server implementers are
allowed to forbid less-secure methods of authentication and encryption for this
API (by dropping support for them). Currently, we leave it for the server
implementers to decide which methods are "secure enough". These recommendations
MAY change in the future.


Endpoints to be implemented
---------------------------

Server implementers MUST:

 * Implement the [`get` endpoint](endpoints/get.md).
 * Implement the [`index` endpoint](endpoints/index.md).
 * Implement the [`update` endpoint](endpoints/update.md).
 * Put the URLs of these endpoints in their [manifest file][discovery-api], as
   described in [manifest-entry.xsd](manifest-entry.xsd).

The details on each of these endpoints are described on separate pages of this
API specification (use the links above).


Outgoing Mobility Learning Agreement API vs. Outgoing Mobilities API
--------------------------------------------------------------------

The Learning Agreement, which was initially part of the Outgoing Mobilities API,
has been separated into the Outgoing Mobility Learning Agreements API.
However, the learning agreement object is still identified by the mobility identifier (omobility_id).
If the HEI implements both the Outgoing Mobilities API and the Outgoing Mobility Learning Agreements
(which we encourage), then it must ensure that every learning agreement object will have
the same identifier as the corresponding outgoing mobility objects served by the Outgoing Mobilities API.


Data model entities involved in the response
--------------------------------------------

 * Mobility
 * Person
 * Coordinator
 * IIA Partner
 * Learning Agreement
 * Learning Agreement Component
 * Learning Opportunity Specification
 * Learning Opportunity Instance
 * Academic Term


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[sec-v2]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v2
