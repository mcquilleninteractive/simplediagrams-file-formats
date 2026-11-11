# Refused: object in two groups

**Rule broken:** Each element, connector and group belongs directly to at most one group. Here rectangle A is a member of two groups.

**Stated in:** [connections.md](../../connections.md#membership-rules), Groups: membership rules.

**Caught by the JSON Schemas:** No. The check spans every group on the page.

**The app's message (English):** "Group 068B780A-CBE2-51AA-A15D-CD28EAD7FE6F contains an invalid relationship."

The identifier is the second group's.

## In this folder

`object-in-two-groups.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Nest the groups instead: make the first group, not rectangle A, a member of the second.
