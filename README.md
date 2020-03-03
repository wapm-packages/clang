# Clang

This package ships the full `clang` compiler and it's Wasm linker (`wasm-ld`), so we can compile C programs to WASI using just WebAssembly.

> Note: This repo is extending the great work from [Ben Smith](https://twitter.com/binjimint) [published in his CppCon 2019 WebAssembly talk](https://www.youtube.com/watch?time_continue=4&v=5N4b-rU-OAA). The WebAssembly binaries are copied from Ben's fork of [llvm-project](https://github.com/binji/llvm-project/releases).

## Run clang!

In this example, we will pass our C program to `clang` via stdin.
Then, we will run the Wasm linker to generate the final `.wasm` program.

```bash
# Run the compiler
echo 'int printf(const char *, ...); int main(){printf("hello world!\n");}' | wapm run clang -cc1 -triple wasm32-unkown-wasi -isysroot /sys -internal-isystem /sys/include -emit-obj -o ./example.o -

# Run the Wasm linker
wapm run wasm-ld -L/sys/lib/wasm32-wasi /sys/lib/wasm32-wasi/crt1.o ./example.o -lc -o ./example.wasm
```

And last, but not least... run it with a [WebAssembly runtime](https://github.com/wasmerio/wasmer)! 

```bash
wasmer example.wasm
```

## Limitations

When you run clang normally (eg. `clang example.c -o ./example`), it will spawn two different process under the hood:

1. The compiler: `clang -cc1 -triple wasm32-unkown-wasi -isysroot /sys -internal-isystem /sys/include -emit-obj -o ./example.o ./example.c`
2. The linker: `wasm-ld -L/sys/lib/wasm32-wasi /sys/lib/wasm32-wasi/crt1.o ./example.o -lc -o ./example.wasm`

However the `posix_spawn` required syscall is not available in WASI.

Because of that, **we need to run this two different calls ourselves**.
