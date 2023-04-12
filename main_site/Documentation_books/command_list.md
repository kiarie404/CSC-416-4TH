### Rust configs

#### Installing, updating and switching between the rust toolchains : {compiler versions, compiler targets}
A toolchain is a group software development tools that help you write, debug, compile source code on a host machine for target machines. Each host sof needs a toolchain.\
Toolchains have components\
Components such as : compiler, debugger, linker\
check the components at : ls ~/.rustup/toolchains/toolchain_name/bin

A toolchain will be able to make binary files for a target device because it has access to "supporting libraries" that were specificaly made for that specific target.\
To view the supporting targets, navigate to : ls ~/.rustup/toolchains/toolchain_name/lib/rustlib/
If you add a new target, a new set of supporting libraries will get added


- rustc --print target-list
- rustup update                 Update Rust toolchains and rustup
- rustup show                   Show the active and installed toolchains or profiles
- rustup toolchain list         List installed toolchains
- rustup toolchain install nightly / 1.8.0 / stable  Install or update a given toolchain
- rustup default nightly        set default toolchain
- rustup target add riscv64gc-unknown-none-elf
- cargo install cargo-binutils
- rustup component add llvm-tools-preview
- rustup component add rust-src
- riscv64-unknown-elf-gdb --version


#### setting up Qemu commands
qemu-riscv64 --version
sudo apt install qemu-user




##### setting up toolchain
- git clone --recursive https://github.com/riscv/riscv-gnu-toolchain.git
- sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build   #### Do this procedurally
- ./configure --prefix=/opt/riscv   ##### OR
- ./configure --prefix=/opt/riscv --enable-multilib
- make linux

Add the /opt/riscv/bin to your system PATH

Modify the Makefile as follows in order to avoid "can't link double-float modules with soft-float modules riscv compiler error":
+++ CFLAGS+=-march=rv64gc -mabi=lp64d
--- CFLAGS+=-march=rv64gc -mabi=lp64  