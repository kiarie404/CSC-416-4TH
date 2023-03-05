### Rust configs

#### Installing, updating and switching between the rust toolchains : {compiler versions, compiler targets}
- rustc --print target-list
- rustup update                 Update Rust toolchains and rustup
- rustup show                   Show the active and installed toolchains or profiles
- rustup toolchain list         List installed toolchains
- rustup toolchain install nightly / 1.8.0 / stable  Install or update a given toolchain
- rustup default nightly        set default toolchain
- rustup target add riscv64gc-unknown-none-elf
- 