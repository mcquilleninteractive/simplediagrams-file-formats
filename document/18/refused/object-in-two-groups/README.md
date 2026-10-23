# Refused: object in two groups

**Rule broken:** Each element, connector and group belongs directly to at most one group. Here rectangle A is a member of two groups.

**Stated in:** [connections.md](../../connections.md#membership-rules), Groups: membership rules.

**Caught by the JSON Schemas:** No. The check spans every group on the page.

**The app's message (English):** "Group 068B780A-CBE2-51AA-A15D-CD28EAD7FE6F contains an invalid relationship."

The identifier is the second group's.

## In this folder

`object-in-two-groups.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Nest the groups instead: make the first group, not rectangle A, a member of the second.
