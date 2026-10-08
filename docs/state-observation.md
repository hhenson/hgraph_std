# Independently sampled endpoint state

[The shared HGL tests](../hgl/hgraph/tests/state_observation_values.hgl) sample
live endpoints using a separate `step` input. Mutation actions and observations
occur in different evaluations. Repeated observations also cover an idle
evaluation after initialization and after child invalidation.

The three cases cover:

- An initially invalid scalar, its first valid value, and held values during silence.
- An initialized map child that becomes invalid while its key remains present,
  then becomes valid through `update`, is removed, and is inserted again.
- An initialized list child that becomes invalid while its position remains
  present; later push and pop change the tail while preserving that invalid child.

The map and list each retain another valid child throughout the assertions.
These tests therefore do not depend on the validity of a parent whose last valid
child has been invalidated. Invalid child payloads are never read. Runtime
iteration retains child metadata, and counts positions without inspecting their
payloads.

Each list observation publishes a complete atomic tuple snapshot containing
the live-position count, first-child validity, and immediate-child completeness.
These metrics are ordinary values calculated together during that observation.

The expectations follow specification commit `027475d`:

- [Handler selectors and validity guards](https://github.com/hhenson/hgraph_spec/blob/027475d/language/docs/user-guide/functions.md):
  `when modified(step) && valid(step)` restricts activation and required validity
  to `step`. The watched endpoint can be invalid, and `if valid(child)` guards a
  subsequent child payload read.
- [Output access](https://github.com/hhenson/hgraph_spec/blob/027475d/language/docs/user-guide/functions.md#output-access)
  and [runtime output](https://github.com/hhenson/hgraph_spec/blob/027475d/language/docs/developer-guide/syntax-and-semantics.md#runtime-output):
  map/list invalidation preserves structure and invalidates an existing child;
  insert/update/remove/pop enforce their absent/present/nonempty preconditions.
  Push appends and initializes a trailing child.
- [Temporal metadata and collection views](https://github.com/hhenson/hgraph_spec/blob/027475d/language/docs/user-guide/types-and-expressions.md#temporal-metadata):
  `all_valid` checks immediate live children, `contains` checks membership even
  when a map child is invalid, and temporal list/map iteration preserves child
  views and endpoint metadata.
- [Node-time traversal](https://github.com/hhenson/hgraph_spec/blob/027475d/language/docs/design/iteration.md#node-time-traversal):
  a runtime traversal enumerates current child views and supports evaluation-local
  counting, without wiring new nodes.
- [Time-series rules TS-5, TS-7, TS-9, TS-19](https://github.com/hhenson/hgraph_spec/blob/027475d/runtime/time_series.md#rules):
  child validity changes are distinct from published-value deltas; invalidation
  notifies separately from a tick; live invalid children prevent `all_valid`; and
  a dictionary child's invalidation does not remove its key.

These cases observe the mutating producer directly. The publication-only
`pass_through` tests do not establish full state copying across invalidation.
There is no new invalidation delta syntax here, and no assertion about empty
publications, initial creation of invalid children, whole-output invalidation,
or reference behavior. The proposed delta/state extension supplies none of the
expectations in these cases.
