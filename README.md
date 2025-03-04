# Artifact for "TreeKEM: A Modular Machine-Checked Symbolic Security Analysis of Group Key Agreement in Messaging Layer Security"

## Link to the paper

[Cryptology ePrint Archive](https://eprint.iacr.org/2025/410)

## Structure of the repository

- comparse: external support library for parsers and serializers, imported, not part of this work
- dolev-yao-star: external support library for Dolev-Yao reasoning in F*, imported, not part of this work
- treekem: this work

## Functions, types, and theorems from the paper

Section 3.1: TreeKEM's Tree in F*

- [common tree type](treekem/fstar/common/code/MLS.Tree.fst#L49)
- [common path type](treekem/fstar/common/code/MLS.Tree.fst#L72)
- [treekem public state](treekem/fstar/treekem/code/MLS.TreeKEM.Types.fst#L20)
- [treekem private state](treekem/fstar/treekem/code/MLS.TreeKEM.Types.fst#L23)
- [decrypt path secret](treekem/fstar/treekem/code/MLS.TreeKEM.Operations.fst#L245)

Section 3.2: TreeKEM API

- [prepare process full commit](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L110)
- [prepare process add-only commit](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L122)
- [finalize process commit](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L133)
- [prepare commit creation](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L157)
- [continue commit creation](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L184)
- [prepare add-only commit creation](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L205)
- [finalize commit creation](treekem/fstar/treekem/code/MLS.TreeKEM.API.fst#L224)

Section 4.2: Preliminaries for TreeKEM security theorem

- [event containing local group state](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.EpochEvent.fst#L102)
- [epoch key](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.State.EpochSecrets.fst#L105)
- [signature key](treekem/fstar/treesync/symbolic/MLS.TreeSync.Symbolic.State.SignatureKey.fst#L33)
- [init key](treekem/fstar/bootstrap/symbolic/MLS.Bootstrap.Symbolic.State.InitKey.fst#L33)
- [node key](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.State.NodeKey.fst#L52)

Section 4.3: TreeKEM security theorem

- [security theorem](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.SecurityTheorem.fst#L299)

Section 5.1: Security lemmas for initialization keys

- [security lemma with labels](treekem/fstar/bootstrap/symbolic/MLS.Bootstrap.Symbolic.KeyPackage.fst#L161)

Section 5.2: Security lemmas for Welcome

- [GroupInfo signature creation](treekem/fstar/bootstrap/symbolic/MLS.Bootstrap.Symbolic.GroupInfo.fst#L78)
- [GroupInfo signature verification](treekem/fstar/bootstrap/symbolic/MLS.Bootstrap.Symbolic.GroupInfo.fst#L145)

Section 5.3: Security lemmas for the key schedule

- [joiner secret label](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.KeySchedule.fst#L406)

Section 5.4: Formally proving the tree invariant

- [TreeKEM invariant for one node](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.Tree.Labels.fst#L262)
- [TreeKEM invariant for all nodes](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.Tree.Labels.fst#L279)
- [TreeKEM invariant proof](treekem/fstar/treekem/symbolic/MLS.TreeKEM.Symbolic.Tree.Labels.fst#L725)

## Build

There are three ways to build this artifact, which were tested on x86_64 computers.

### Using nix (recommended)

This artifact is reproducible thanks to nix.
It uses the flakes feature, make sure to enable it.

    # This command will compile Z3, F*,
    # and the other dependencies to the correct version
    nix develop

    cd treekem
    # This command will verify proofs
    make
    # This command will run tests, see last section of this README
    make check

### Using docker (recommended)

If nix is not installed on the system, it can be used through a docker image we provide.

    # Build the docker image. This will compile Z3 and F* to the correct version.
    docker build . -t treekem_artifact
    # Start the image and start a shell with correct environment
    docker run -it treekem_artifact

    cd treekem
    # This command will verify proofs
    make
    # This command will run tests, see last section of this README
    make check

### Using a locally-installed F* (not recommended)

This artifact can also be built directly, assuming the host system has the correct dependencies.

This artifact is compatible with:
- F* commit c9586cb8712c6b979006cc2ede13d0940cae739b
- Z3 version 4.8.5
- OCaml version 4.14

However we can't guarantee everything will go smoothly with this method.

    # Change the PATH to have z3 and fstar.exe
    export PATH=$PATH:/path/to/z3/directory/bin:/path/to/fstar/directory/bin
    # Setup the environment
    export FSTAR_HOME=/path/to/fstar/directory
    eval $(opam env)

    cd treekem
    # This command will verify proofs
    make
    # This command will run tests, see last section of this README
    make check

## What is tested with `make check`?

There are four types of tests:
- internal tests of the high-level API, which sends messages within a small group
- tests for the RFC test vectors
- fuzzer for the correctness of TreeKEM
- benchmarks

We pass all test vectors for the following ciphersuites:
- `MLS_128_DHKEMX25519_AES128GCM_SHA256_Ed25519`
- `MLS_128_DHKEMX25519_CHACHA20POLY1305_SHA256_Ed25519`

If any of the test, fuzz or benchmark fails, the `make check` command will fail and the corresponding error is printed.

The test vectors comes from the [official repository](https://github.com/mlswg/mls-implementations), with the commit revision present in [this file](treekem/test_vectors/git_commit).

