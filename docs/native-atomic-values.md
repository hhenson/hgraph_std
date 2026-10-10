# Native atomic value fixture

Builds validating [the shared tests](../hgl/hgraph/tests/native_atomic_values.hgl)
add [this test-only native interface](../hgl/hgraph/native/atomic_values_fixture.hgl)
to `hgraph.std`. Each target supplies its implementations and an existing package
descriptor under [NVAL-1–5](https://github.com/hhenson/hgraph_spec/blob/52ee9850b3b31a1786d79d19c7f36a72f1b1a189/language/docs/design/native-atomic-values.md).
No provider code or opaque resource state is part of this fixture.

The descriptor maps `Token`, `TokenOther` and `TextOnly` to three distinct
canonical native scalar identities, shared by every target. Each contains
owning text. The six declared helpers construct or read the exact type; a
constructor retains its input and a reader returns independent ordinary text.
Their calls are permitted in executed test setup, cold materialization and
node value phases, without runtime-only capabilities. They are never executed
by source checking or required constant evaluation.

| Type | Shared capabilities |
|---|---|
| `Token` | Owning copy, text, equality, hash, order and serialization for typed replay/record |
| `TokenOther` | Same capabilities and logical layout as Token; distinct canonical identity |
| `TextOnly` | Owning copy, text and serialization for typed replay/record; no equality, hash or order |

Equality and hash for Token and TokenOther use exact contained text within
one canonical type; order is lexicographic text order. Each text helper returns
the supplied text unchanged, including empty text. Replacement never modifies
an earlier retained native value. Serialization preserves canonical identity
and contents. Providers remain available until retained values are destroyed.
These requirements describe observable scalar values, not RAII resource state.

The tests use scalar text observers for TextOnly transport and replay, and
`value.capability` for executed boxed operations requiring absent capabilities.
Known-type capability rejection, missing/incompatible descriptors, exported
imports and same-canonical-identity aliases remain checking/provider cases in
the specification; no new source-error codes are assigned here. Global copying tests both native scalars returned as independent ordinary
values and an enclosing struct's lexical borrow with explicit constructor
retention. Replacing a scalar local leaves its global entry unchanged.

This corpus has been reviewed statically. Target execution and provider lifetime
validation are performed by each consumer; this document claims no target run.
