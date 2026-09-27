# hgraph_std

The public standard library for HGL, written in HGL. It contains operator
contracts, HGL bodies, HGL tests and shared native declarations. It contains
no Python, C++ or Rust implementation and selects no target provider.

- [Standard module](hgl/hgraph/standard.hgl), with control, stream and temporal parts
- [Operators](hgl/hgraph/operators.hgl)
- [Native support](docs/native-support.md)

The [language specification](https://github.com/hhenson/hgraph_spec) defines
these constructs. [hgraph_spec_audit](https://github.com/hhenson/hgraph_spec_audit)
exercises them against released Python and C++ hgraph.

A compiler build selects this repository at a fixed commit and supplies its
own native implementation. C++ providers live in hgraph; Rust providers live
in the private Rust implementation. No generated native code is checked in here.
The existing C++ build materializes the pinned HGL files before compilation;
its standard-library tests execute the HGL tests and installed-SDK consumer.
