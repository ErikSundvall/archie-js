We want different file fomats to be represented in different ways. Format can be deterninged based on file endings used as headings below.

# .wt.json
This is an openEHR "Web template" definition (A kind of schema.) The attributes in the (partly reopeated) structure are desctibed in the section https://deepwiki.com/better-care/web-template/3.4-webtemplate-structure#webtemplatenode-properties. This meanins that when the D3 tree structure is built up, the child branching is to be done on the `children` attribute and that the D3 node label is based on the `name` attribute. All attributes of a node should be made available in the D3 `data` field

# .json
Arbitrary json file that is branched on normal JSON objects and/or array

# TODO
- fix hoover lift
- fix collapse/expand (not for cluster?)
- print cardinality on branch [0..*] 
- 