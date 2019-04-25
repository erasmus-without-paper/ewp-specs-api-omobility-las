Outgoing Mobility Learning Agreements Index endpoint
====================================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the receiving institution to access a list of all
learning agreements it can read on the sending institution's server.


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

An identifier of the institution which is the sending partner of the
mobilities. This parameter MUST be required by the server even if it covers
only a single institution. It is *not* repeatable.

**By default**, the server returns all learning agreements the requester *has access to*
(regardless of their receiving partner, arrival date, nor status).
This includes very old learning agreements.


### `receiving_hei_id` (repeatable, optional)

A list of institution identifiers. If given, then the results returned MUST
contain only those learning agreements whose receiving institution matches **at least
one** of the given `receiving_hei_id` identifiers.

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.


### `receiving_academic_year_id` (optional)

An academic year identifier, in the `YYYY/YYYY` format, as defined in the
[EWP Academic Term Data Types](https://github.com/erasmus-without-paper/ewp-specs-types-academic-term)
document. If given, then the server MUST return learning agreements for only such mobilities
which have taken place (or are planned to take place) during this single academic year.


### `modified_since` (optional)

A datetime string in the [`xs:dateTime` format][xs-datetime], e.g.
`2019-04-02T15:19:21+01:00`.

If given, then the server SHOULD filter the returned IDs to the ones
for which the learning agreement has been either created or modified after the given point in time.

 * Servers MAY include learning agreements which were *not* modified. For example, if
   the server only *suspects* that the learning agreement has been modified, then it is
   okay to include such ID in the response.

 * As we previously explained [here][index-pulling], clients MAY use the
   `index` and `get` endpoints as a pull-based method of synchronization,
   alternative (or rather complementary) to CNRs. It is RECOMMENDED for the
   servers to support this parameter, to avoid unnecessary network traffic.


Permissions
-----------

Only selected learning agreements should be accessible to the caller.
Visibility of objects SHOULD be kept in sync with the visibility described in
the `get` endpoint. That is:

 * Servers MUST publish IDs of every learning agreement which is accessible
   (to the same caller) via the `get` endpoint.

 * Server MUST NOT publish IDs of objects which would NOT be accessible (to the
   same caller) via its `get` endpoint. Clients which receive IDs from the `index` endpoint
   should be able to fetch all objects referenced by those IDs.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Note, that parameters are "AND-ed" together, but their values are "OR-ed".
   This means that invalid or unknown values provided in `sending_hei_id` and
   `receiving_hei_id` parameters MUST be **allowed** (but **not ignored**) by
   the server.

   The difference between "being allowed" and "being ignored" becomes clear
   once you try to analyze the scenario when one of these lists contains
   **only** such IDs which are unknown to the server. The server MUST respond
   with an empty `<response>` element in this case, regardless of all the other
   parameters. For example:

   * `?sending_hei_id=known&receiving_hei_id=known&receiving_hei_id=UNKNOWN`
     will return some results,
   * `?sending_hei_id=known&receiving_hei_id=known` will return exactly the
     same results,
   * `?sending_hei_id=known&receiving_hei_id=UNKNOWN` will **NEVER** return any
     results,
   * `?sending_hei_id=known` will return results again (but not necessarily the
     same results as the ones returned in the first two bullets).


Response
--------

Servers MUST respond with a valid XML document described by the
[index-response.xsd](index-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[index-pulling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#index-pulling
[xs-datetime]: https://www.w3.org/TR/xmlschema11-2/#dateTime
