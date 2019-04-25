Outgoing Mobility Learning Agreement Get endpoint
=================================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the client to retrieve information on specific
learning agreements from the sending HEI.


Request method
--------------

 * Requests MUST be made with either HTTP GET or HTTP POST method. Servers MUST
   support both these methods. Servers SHOULD reject all other request methods.

 * Clients are advised to use POST when passing large number of parameters
   (servers MAY set a limit on their GET query string length).


Request parameters
------------------

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `sending_hei_id` (required)

SCHAC ID of the mobilities' owner HEI (in EWP, the *sending* HEI is always the
mobility's "owner"). This parameter MUST be required by the server even if the
server covers only a single institution.


### `omobility_id` (repeatable, required)

A list of identifiers (no more than `<max-omobility-ids>` items) of mobilities
for which the client wants to retrieve learning agrrements. All of these mobilities
should be the outgoing mobilities of the sending HEI provided in the
`sending_hei_id` parameter (otherwise, they will be ignored).

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.

Server implementers provide their own chosen value of `<max-omobility-ids>` via
their manifest entry (see [manifest-entry.xsd](manifest-entry.xsd)). Clients
SHOULD parse this value (or assume it's equal to `1`).


Permissions
-----------

Only a subset of all known learning agreements should be made available to the caller.
It seems reasonable to apply the same rules as in [Outgoing Mobilities API][omobilities]:

 * If the caller covers the receiving HEI of this mobility, then he MUST be
   allowed access to its learning agreement.

 * If the caller covers the sending HEI of this mobility, then he SHOULD be
   allowed access to its learning agreement. (It seems reasonable, but we leave this decision
   to your team.)

 * All other callers probably SHOULD NOT be allowed access, but we leave this
   decision to your team.

 * Note, that server implementers need to verify these access rights for each
   ID on the `omobility_id` list. It is possible that the caller has access to
   only some of the IDs he provided. If this seems problematic, then you (the
   server implementer) can always set your `<max-omobility-ids>` to `1`.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid (unknown) `omobility_id` values MUST be **ignored**. Servers MUST
   return a valid (HTTP 200) XML response in such cases, but the response will
   simply not contain the information on the unknown `omobility_id` values. If
   all values are invalid/unknown, servers MUST respond with an empty
   `<response>` element. This requirement is true even when
   `<max-omobility-ids>` is `1`.

 * If the caller doesn't have permission to read some of the `omobility_ids`,
   then such `omobility_ids` MUST also be **ignored**, exactly as above. Servers
   MUST return a valid (HTTP 200) XML response in such cases, and this response
   MUST include all the remaining learning agreements (the ones that the requester *has*
   access to). If the requester doesn't have access to none of the requested
   `omobility_ids`, an empty HTTP 200 `<response>` element MUST be returned.

 * Note, that currently clients have no way of telling the difference between
   "this learning agreements does not exist" and "it does exist, but I don't have access
   to read it". In both cases, the proper learning agreement element will simply be
   missing from the response.

 * If the length of `omobility_id` list is greater than `<max-omobility-ids>`,
   servers MUST respond with HTTP 400.


Response
--------

Servers MUST respond with a valid XML document described by the
[get-response.xsd](get-response.xsd) schema. See the schema annotations for
further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[omobilities]: https://github.com/erasmus-without-paper/ewp-specs-api-omobilities
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
