# staple

This package implements an engine for static plugins. This crate is a WIP.

## Static plugins

Traditionally, plugins have required separate processes to be spawned or shared
libraries to be linked. This crate is a new solution to the plugin problem which
proposes that the final executable/binary of some pluggable software actually just
do nothing at all. `staple` is then inserted into its `build.rs` and will go through
every dependency and look for every crate that depends directly on `staple`. If any
dependency depends on an incompatible version of staple, the build will fail.
`staple` will then generate calls to the plugin loaders of each of the plugins.

When your binary goes to run, `staple`'s generated loader will start a `specs` ECS
dispatcher and run an `actix` actor system. It will then go and register every
entity, component, system, and actor by calling a function in the crate that loads them in.

## Q&A

If lots of people use this, wont it result in unrelated things talking in the same
system?

- Yes, and that is fully intended

Why? Wouldn't coupling be bad?

- I want an OS to eventually be written that has applications written with `staple` where all I/O and events generated by other applications are part of the same async actor system. The Rust standard library on such a system would send messages over the actor system for I/O, since that would be the actual OS mechanism for interacting with resources.