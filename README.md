# Clang in WASM

This repo demonstrates how to run clang & it's linker using only WebAssembly and [Wasmer](https://wasmer.io/).

This repo is showcasing the great work from Ben Smith [exposed in his CppCon 2019 WebAssembly talk](https://www.youtube.com/watch?time_continue=4&v=5N4b-rU-OAA).

The WebAssembly binaries are copied from Ben's fork of [llvm-project](https://github.com/binji/llvm-project/releases).

# Compile the C file to a WASM WASI file

First, you will need to create an example.c file. You can take the source [from here]().

```
# Compile example.c to an object file (example.o)

wapm run clang --dir=. -- -cc1 -triple wasm32-unkown-wasi -isysroot /sys -internal-isystem /sys/include -emit-obj -o ./example.o ./example.c

# Compile example.o to example.wasm
wapm run wasm-ld --dir=. -- -L/sys/lib/wasm32-wasi /sys/lib/wasm32-wasi/crt1.o ./example.o -lc -o ./example.wasm
```

# Run it with Wasmer! 

```
wasmer run example.wasm
```
