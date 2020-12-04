Outgoing Mobility Learning Agreements Update endpoint
=====================================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the receiving HEI to comment or approve a Learning
Agreement proposal, stored on the *sending* HEI's servers.


Update request versioning
-------------------------

We introduced a simple versioning on update types. As you see, each update
type has the `-v1` suffix. If, in the future, we decide that some type of
update should require more request parameters, then we will release `-v2` of
this update type, while allowing the previous versions to work in a
backward-compatible manner. This system is not as flexible as EWP's API
versioning is, but it should be flexible enough.


Request method and parameters
-----------------------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.

 * The body of the request MUST contain a raw XML element described in the
   [update-request.xsd](update-request.xsd) schema. The request SHOULD also
   contain a matching `Content-Type` header (such as `text/xml`).

   (This endpoint is **NOT** using the regular `application/x-www-form-urlencoded`
   request format which most other EWP endpoints do.)


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * If some required parameters (the ones with `minOccurs="1"`) are
   missing, or are passed in an invalid format, then the server MUST respond
   with HTTP 400 error response.

 * If the request contains unknown parameters (extra elements not allowed in
   the schema), then the server MUST ignore such parameters, and continue with
   processing the request.

 * If updated learning agreement does not exist, or the requester does not cover
   the receiving HEI of the mobility, then server MUST respond with HTTP 400 error
   response.

 * If `<sending-hei-id>` doesn't match `<omobility-id>`'s sending HEI, then the
   server MUST respond with HTTP 400 error response.

 * More requirements - in context of specific types of updates - are described
   in the [update-request.xsd](update-request.xsd) file.


Handling edit conflicts
-----------------------

[Edit conflicts](https://en.wikipedia.org/wiki/Edit_conflict) are special cases
of bad requests. Such conflicts can always happen (they cannot be completely
avoided), and both server and client implementers MUST be aware of them.

In context of this particular API, edit conflicts occur when the receiving HEI
attempts to change something, which has been concurrently changed on the sending HEI's servers.
For example, it tries to approve a version of the LA which is no longer the latest proposal.

Some edit conflicts MAY be automatically resolved be the server (the sending
HEI), but most won't be. They must be detected though. For this reason:

 * The server (the sending HEI) is REQUIRED to perform some additional
   assertions to make sure that the client (the receiving HEI) had up-to-date
   data before it made the request. If it turns out that the client didn't have
   to up-to-date data, then the server will respond - as instructed by the
   [general EWP error handling rules][error-handling] - with
   the [HTTP 409 Conflict][http-409] error.

 * The client (the receiving HEI) is REQUIRED to expect these HTTP 409
   responses, and deal with them "appropriately". Usually this means that you
   need to fetch the fresh data from the server and ask your user to repeat his
   action once again. For example, you can display a message of this sort:

   *Sorry, it turns out that a new version of the LA has been published by the
   sending HEI just before you clicked 'Approve'. It seems that someone else is
   doing modifications to this LA in the same time you were viewing it, and he
   made his change "first", so your approval couldn't be saved. Please review
   this new version and, if it's still okay with you, click 'Approve' again!*

Every update type generates edit conflicts of its own. That's why **we don't**
explain all possible conflicts here - instead, we do that in the
[update-request.xsd](update-request.xsd) file. (BTW - this is often true for
all other EWP specifications too - many vital EWP requirements are described in
the XSD annotations! You MUST read those. Always.)


Response
--------

Servers MUST respond with a valid XML document described by the
[update-response.xsd](update-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[http-409]: https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.10
