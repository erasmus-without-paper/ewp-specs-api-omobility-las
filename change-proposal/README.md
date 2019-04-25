Learning agreement: schema simplification
=========================================

The current version of the learning agreement is quite complicated.
We want to propose a simpler version that still will be compatible with the Commission's guidelines
(https://ec.europa.eu/programmes/erasmus-plus/resources/documents/applicants/learning-agreement_en).

In the current version of the scheme, both `components-studied` (tables A / A2) and `components-recognized` (tables B / B2) consist of six parts:
- `before-mobility-changes`
- `before-mobility-snapshot`
- `latest-approved-changes`
- `latest-approved-snapshot`
- `latest-draft-changes`
- `latest-draft-snapshot`

In short, our suggestion is to remove all `changes` parts and slightly modify the `snapshot` parts,
so that we can express the reasons for the changes in LA after it has been first signed (by all three parties - tables A2 and B2).

As the `components-studied` and `components-recognized` parts are symmetric, in the rest of this description we will focus on
the `components-studied` part. Changes in the second part will be symmetric.

**Why we want to get rid of the `changes` parts?**

- Keeping the whole history of changes is not required in the LA process.
- Expressing changes in the current version of the API is complicated.
  Because we do not have any required identifiers or codes for the courses on the receiving HEI (`components-studied`),
  and it seems that we can not require them, we use the position numbers on the list (indexes).
  When the change consists of several removal and addition operations, the indexes may be interpreted differently.
  Of course, we have to impose one way of interpretation, but it can still easily lead to errors.
- Not everyone stores the entire change history in the system and the current specification allows it.
  In such cases, the server is allowed to calculate the `changes` parts based on the current and previous version before sending.
  It seems unnecessary. Equally, the client can do such a calculation if for some reason he wants to use it.
- Courses lists in LA are not long. Coordinators can (or even may prefere) to view entire, ready versions of LA.
  Or the client systems, even using poor algorithms, can calculate these changes if needed.

**What we would like to change in get response**

- Remove `before-mobility-changes` and `latest-approved-changes`.
- Remove "snapshot suffix" from all three parts. 
- Rename `latest-draft(-snapshot)` to `latest-proposal`.
- Make `latest-proposal` element optional. Previously, if a LA was accepted and there was currently
  no proposal for change, then a copy of the approved version was sent in the `latest-proposal`.
  Now we suggest to send this element only if there are actually any changes.
- In `latest-approved` and `latest-proposal`, with each course it will be possible to additionally say that,
  in relation to the `before-mobility` version, it has been removed or added, and send the reason of the change.
  The new fields (inserted/removed flag and the reason) will be used only when the LA has been already first signed.
  The flag can be computed based on the courses listed in the `before-mobility` and `latest-approved`,
  but we need a place to explain the reason of such a change.
- Rename `latest-draft-changes` to `latest-changes`. At fist we wanted to remove this element.
  We decided to keep it as it allows to express changes (and reasons for them) compared to the `latest-approved` version,
  which can be useful for the host institutions, especially if the course was once added and then removed.
- In the `latest-changes` part we will use course names instead of indexes.

**What we get after this changes in get response**

After these changes we will have four parts: `before-mobility`, `latest-approved`, `latest-changes` and `latest-proposal`.

- `before-mobility` - The first signed version of the Learning Agreement, equivalent to table A of the LA.
  Will not be present before the first version of the LA is signed. After that it will be fixed.
- `latest-approved` - The current version of the Learning Agreement. In the last version it is equivalent to Table A2 of the LA.
  It contains courses which are actually attended by the student and changes in relation to the `before-mobility` version (with reasons).
  This part will not be present before the first version of the LA is signed.
  When the first version of the LA is signed, then it will be equal to the `before-mobility`.
  After further changes, it will differ from it and contain reasons of this differences.
- `latest-changes` - Proposal of changes to the `latest-approved` version (course names used), not signed yet
  (usually signed by the student and the sending HEI, but not by the receiving HEI).
  When LA is signed and we do not want to change it, the `latest-changes` part will not be present in the get response.
- `latest-proposal` - Proposal of a new version of the LA, not signed yet (usually signed by the student and the sending HEI, but not by the receiving HEI).
  When LA is signed and we do not want to change it, the `latest-proposal` part will not be present in the get response.
  When the proposal is signed by all three parties, the `latest-proposal` is copied to `latest-approved`.

**What we would like to change in update request**

If we change the `get` response scheme, we should also change the update request similarly. We propose to:

- Rename `approve-components-studied-draft-v1` to `approve-components-studied-proposal-v1`.
- Rename `current-latest-draft-snapshot` to `latest-proposal`. Use the same structure as the `latest-proposal` in the `get` response.
- Remove `suggested-changes` element.
- Rename `snapshot-with-changes-applied` to `suggestion`. Use the same structure as the `latest-proposal` in the `get` response.

Additionally, we suggest to add `comment` field to the `update-components-studied-v1` request (this is the request
which is sent by the receiving HEI when it wants to suggest changes) and make the structured suggestion optional.

**Example scenario**

For clarity, the examples contain only information about courses that the student wants to attend at the receiving HEI (`components-studied`).
Attached files *are not* valid get responses or valid update requests.

* 01-initial-version.xml - first proposal of the LA, already accepted by the student and the sending HEI (get response)
* 02-update-components-studied.xml - the receiving HEI does not accept the LA and proposes some changes (update request)
* 03-second-version-get.xml - second proposal of the LA, taking into account the comments of the partner,
  already accepted by the student and the sending HEI (get response)
* 04-approve-components-studied-draft.xml - the receiving HEI accepts the LA (update request)
* 05-signed-version-before.xml - first version of the LA which is signed by all three parties - Table A and first version of Table A2 (get response)
* 06-changes-during-proposal.xml - student wants to change one course - Table A, first version of Table A2 and proposals (get response)
* 07-approve-components-studied-draft.xml - the receiving HEI accepts the change (update request)
* 08-changes-during-proposal.xml - change applied; student wants to change two more courses - one of them added in previous change
  - Table A, second version of Table A2 and proposals (get response)
* 09-update-components-studied.xml  - the receiving HEI does not accept the change (update request). This time it uses comment instead of structured suggestion.
* 10-changes-during-fixed-proposal.xml - student wants to change one more course, taking into account the receiving HEI comments
  - Table A, second version of Table A2 and proposals (get response)
* 11-approve-components-studied-draft.xml - the receiving HEI accepts the change (update request)
* 12-final-version.xml - final version of the LA, with two changes during the mobility - Table A and A2 (get response) 
