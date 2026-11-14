# FormalVerificationDemos
Demos of core ideas in formal verification.

### Dependencies
- ocaml v5.2.0
- rocq-prover v9.0.1
- rocq-stdpp v1.13.0
- coq-paco v4.2.3

### Build

```sh
make
```

This generates `Makefile.coq` from `_CoqProject` and compiles everything under
`src/` — the library first, then the demos.

### Building the library only

A demo under `src/demos/` that is still in progress will fail the full build.
To compile just `src/lib/` and leave the demos alone:

```sh
rocq makefile -f _CoqProject -o Makefile.coq
make -f Makefile.coq src/lib/Prelude.vo src/lib/TransSystem.vo src/lib/LTSRefine.vo
```

`rocq makefile` works out the dependency order, so the three targets can be
given in any order. Once the `.vo` files exist, `Require Import` from a demo
resolves without rebuilding.

### Editing

Open any `.v` file in VS Code with the
[VsRocq](https://marketplace.visualstudio.com/items?itemName=rocq-prover.vsrocq)
extension. The extension needs a matching `vsrocq-language-server` in the same
opam switch; run `Rocq: Troubleshooting: Show setup` from the command palette
(`F1`) to check that the two versions agree.

VsRocq reads `_CoqProject` when the server starts, so after editing it run
`Developer: Reload Window`.