# Owned REF output route tests

[Shared HGL tests](../hgl/hgraph/tests/reference_owner_export_values.hgl)
exercise [GRF-27](https://github.com/hhenson/hgraph_spec/blob/codex/ref-owner-export/runtime/graph.md)
and TS-20 from Spec95, together with TS-16/17/25. Validation is static only;
no HGL backend execution or conformance is claimed.

Two groups each repeat an eval in a fresh graph. Nested conditional REF results
export an actual branch-owned scalar through two owner boundaries; toggling the
inner and outer selectors follows the current producer and fresh reentry.
A concrete nominal pair assembles two branch-owned scalar connections and
exports their designation tree. Its ordinary same-shaped consumer preserves
left-only/right-only deltas, samples only a changed right child while retaining
the left designation, then samples both new endpoints on an outer retarget,
including an equal-valued left child.

The suite uses graph construction and ordinary scalar/delta eval publications.
It introduces no raw REF harness inputs, ordinary REF state or empty reference
constructor. Direct unexported/parallel/rank violations have no admitted HGL
formation fixture here, so this suite adds no negative source syntax or code.
