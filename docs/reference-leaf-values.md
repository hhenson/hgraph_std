# Held references to owning leaf values

[The shared leaf suite](../hgl/hgraph/tests/reference_leaf_values.hgl) follows
bytes, Any boxes and native Token through an opaque generic REF relay and
REF output into an ordinary same-shaped delta consumer. Leading/trailing
silence, empty values, equal publications and fresh evaluations preserve their
ordinary contracts. Changing an Any box's contained type changes its payload,
not its temporal Any shape or target designation.

Assemble the suite with the existing
[native atomic fixture](../hgl/hgraph/native/atomic_values_fixture.hgl) and its
[shared provider contract](native-atomic-values.md). TextOnly transport is
observed solely through its checked text helper. A separate scalar observer
uses [REF-EQ-1](https://github.com/hhenson/hgraph_spec/pull/91) to distinguish the
same endpoint from separately wired equal-text endpoints; it requires no
TextOnly payload equality, hash or order.

The branch is based on Native Std #44 and its Bytes/Any dependencies, rather
than a local integration branch. No raw REF values enter eval/replay/record,
no ordinary REF state or empty REF constructor is introduced, and no new
source rule is proposed. Structural REF and expiry suites remain separate.

Static source/contract, whitespace and delimiter review only; no HGL backend
execution or conformance is claimed.
