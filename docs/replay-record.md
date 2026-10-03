# Ordinary replay and recording

`replay` and `record` are ordinary HGL library operators. Their shared data
type has two required fields:

```hgl
export struct TimedValue<T> {
    time: datetime
    value: delta<T>
}
```

`T` names the originating time-series shape; `value` stores its delta, which
reduces to the scalar type for a scalar shape.

`replay(const values: list<TimedValue<T>>) -> T` consumes a typed unbounded
ordinary list. An entry is one present publication at its absolute timestamp.
False, zero and empty text are values; silence has no entry. Equal values at
different times publish separately. Empty lists publish nothing, and their
concrete element type still fixes the result type. A fixed-size list is a
different type and is not implicitly converted.

Entries must have strictly increasing times, including skipped past entries.
Replay checks each reached yield; equal or decreasing times raise an error.
An admitted past entry skips, a due entry publishes, and a future entry
suspends. Replay neither sorts nor merges. Run start is inclusive and end
exclusive; entries at or beyond the end do not publish.

```hgl
const fn samples() -> list<TimedValue<i64>> {
    var values: list<TimedValue<i64>> = []
    push(values, TimedValue<i64>(time: @1970-01-01T00:00:00.000001Z, value: 0))
    push(values, TimedValue<i64>(time: @1970-01-01T00:00:00.000003Z, value: 0))
    return values
}

fn capture_samples() {
    record(replay(samples()), "samples")
}
```

`record(ts: T, const key: str)` binds that key to the exact ordinary type
`list<TimedValue<T>>` before hooks run. Binding does not initialize its value.
At start, the recorder installs a new typed empty list. On each admitted
input publication, it appends an independently retained value paired with
`clock.evaluation_time`. An unmodified input appends nothing, while repeated
equal publications append separate entries. Each writable borrow ends with
its hook. Record has no stop flush.

After all stop hooks, the run owner can obtain an independent recording from
the typed entry before destroying run storage. No ticks means an initialized
empty recording. Missing data or run failure is an error, not an empty success.
A failed push leaves earlier captures intact. A new run starts with a fresh
recording. These contracts do not reserve keys, arbitrate competing writers,
or define checkpoint accumulation.

Eval adapts dense inputs by mapping present slot i to run start plus i minimum
steps. It retains each original dense length separately, passes timed lists
to replay, chooses recording keys, then obtains recordings after stop and
materializes dense results. Thus empty and all-silent inputs can use the same
empty timed data while retaining different horizons. Callers can also compose
replay and record directly as in the example above.

The normative contract and cases are in
[ordinary replay and recording](https://github.com/hhenson/hgraph_spec/blob/f2435639af2c8a8c4dd1c5b319c30cf6ca3df5ba/library/ordinary_replay_record.md)
and [its cases](https://github.com/hhenson/hgraph_spec/blob/f2435639af2c8a8c4dd1c5b319c30cf6ca3df5ba/runtime/cases_ordinary_replay_record.md).
The admitted types are bool, i64, f64, str, date, time, datetime, duration,
sets of bool or i64, fixed-size lists, positional tuples, required-field
nominal structs, and maps with i64 keys. Structural children recursively use
this same profile. Each publication has the exact ordinary type `delta<T>`;
for a scalar this reduces to the scalar itself. A structural delta retains
only the supplied members or children, including nested sparse changes. It
never fills absent children from held values. Each retention into a timed
entry, list, or global entry owns its data independently.

Empty structural deltas are valid ordinary stored values but are outside the
admitted publication profile. Replay's output checks publication admission;
eval additionally checks its entire supplied input trace before starting any
node. Invalid set membership changes and removals of absent map keys fail;
they do not become silent publications. A map removal drops child state, so
reinsertion starts a fresh child. Fixed list size, tuple positions, and nominal
struct identity remain part of the exact delta type.

The [ordinary delta contract](https://github.com/hhenson/hgraph_spec/blob/f2435639af2c8a8c4dd1c5b319c30cf6ca3df5ba/language/docs/design/ordinary-delta-types.md)
defines storage and publication separately. Growing lists, windows, reference
designations, and additional scalar/provider types need their own admitted
contracts and are not implied by the generic declarations above.
