# Any capability edges

[The executed-value tests](../hgl/hgraph/tests/any_capability_edges.hgl) require
capabilities from the contained type, including when a populated box contains
an empty container. An empty `list<timezone>` has equality/hash but no order;
an empty list of structural deltas has no equality/hash because its element
type supplies neither. All four same-type order operators and same-type
delta-list equality fail with `value.capability` where required.

Owning structural deltas may occur in ordinary list elements, tuple type
arguments and struct fields under the
[ordinary delta storage contract](https://github.com/hhenson/hgraph_spec/blob/99d46b8a6f8709ecb682adb5beb857670694bc25/language/docs/design/ordinary-delta-types.md).
The suite boxes those complete ordinary containers without making their types
temporal endpoints or atomic delta payloads. Boxing retains data; it adds no
delta inspection, equality, hash or order. ANY-5 checks actual contained
key capabilities even for the first set member or after a different-type map
key. No direct ordinary hash primitive is added.

Empty Any boxes remain equal, hashable and before populated boxes. Different
contained types remain unequal/unordered without requiring their missing
same-type capabilities; key use still requires equality/hash. Constructors
and assertions execute in test bodies, so optional folding must preserve
execution failures rather than reject the tests during checking.

Required constant capability failures remain checking errors without a
catalogued dedicated source code. The existing delta constructor catalogue
separately covers `delta.index_bounds`, `delta.argument_name`,
`delta.entry_constant` and `delta.entry_type`; those formation failures precede
executed operations. The suite adds no source-rejection annotations or new
error code, and admits no ordinary delta field/index inspection.

Static source/contract, whitespace and delimiter review only; no HGL backend
execution or conformance is claimed.
