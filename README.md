# Design

## Brainstorming

Some goals of MereBlocks:

1. "Microservices contained in a single binary"
2. Package Rust code (potentially other languages too?) into a framework that allows for an event based architecture (using PubSub or messages), and generate a single static binary.
3. Allow for "hot reloading" of the components of the binary.
4. Provide supervisors that monitor and restart the components as needed.

(Points 3 and 4 inspired by Erlang)

Some ideas for the architecture:

- Use threads: in Rust world they are safer and more ergonomic and might allow for simpler implementation of 3 and 4.

- Idea for the general architecture: Given a repository A with Rust code that follows a given contract (implemented through events):

    - Compile A into a static binary
    - Include that binary into the static binary of MereBlocks
    - At runtime, MereBlocks spawns a thread to handle A, which in turn spawns a process with the static binary for A (using [memfd](https://man7.org/linux/man-pages/man2/memfd_create.2.html) as a way to create a process **without** having to write to the filesystem).
    - "Hot reloading" could be done by launching a new instance of MereBlock and communicating with the old instance. The new instance will obtain from the old instance the file descriptors of the processes that it wants to keep, while other processes can be started again under the control of the new instance.
    - MereBlocks itself contains a PubSub that allows for communication between components and even from/to the outside (i.e. other MereBlocks in the same or different computer).

## Experiments

Some experiments as a proof of concept:

1. Build a static binary that contains another binary. The contained binary should be dumped to memory and should be able to spawn a new process.

2. Build a simple "supervisor" of threads.

3. Build the "hot loading" system.

4. Research build system to package multiple repos into the MereBlock binary.

## Questions

1. How much can be shrink the sizes of the blocks to minimize the total size of the fat binary?
