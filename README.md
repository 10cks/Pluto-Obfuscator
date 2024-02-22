# Pluto-Obfuscator
Pluto is an obfuscator based on LLVM 12.0.1, mainly developed by 34r7h4mn.
> Pluto is a dwarf planet in the Kuiper belt, a ring of bodies beyond the orbit of Neptune.

**I have been very busy recent days, so all issues will be handled together later on (probably in the mid of June)**
## Environment
This project was developed and tested on the following environment:
- Ubuntu 20.04.3 LTS
- Clang/LLVM 12.0.1
- CMake 3.16.3
- Ninja 1.10.0

You can also build this project on Windows and MacOS, or even embed it in Android NDK toolchain (need some adjustment, tested on Android NDK r23).

## Features
- Control Flow Flattening
- Bogus Control Flow
- Instruction Substitution
- Random Control Flow
- Variable Substitution
- String Encryption
- Globals Encryption
- [Trap Angr (Experimental)](TrapAngr.md)
- MBA Obfuscation

## Usage

直接运行`./build.sh`或者像下面这样手动操作：

### Building on Linux/Windows
The following commands work on both Linux and Windows:
```shell
sudo apt install libz3-dev
sudo apt install ninja-build cmake python3 gcc g++

cd build
cmake -G "Ninja" -DLLVM_ENABLE_PROJECTS="clang" \
    -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD="X86" \
    -DBUILD_SHARED_LIBS=On ../llvm
ninja
```
### Building on MacOS

```shell
mkdir -p build
cd build
cmake -G "Ninja" -DLLVM_ENABLE_PROJECTS="clang" \
    -DCMAKE_BUILD_TYPE=Release \
    -DDEFAULT_SYSROOT=$(xcrun --show-sdk-path) \
    -DCMAKE_OSX_SYSROOT=/Library/Developer/CommandLineTools/SDKs/MacOSX11.3.sdk \
    -DCMAKE_OSX_ARCHITECTURES="arm64;x86_64" \
    ../llvm
ninja
```



## Test
### Fast test on AES
Run a test case of AES to check out buggy code quickly and roughly.

See [fast-check.sh](fast-check.sh) and [test/aes](test/aes/).
### Full test on libsecp256k1
We have a full test on a crypto library named libsecp256k1 from [bitcoin-core/secp256k1](https://github.com/bitcoin-core/secp256k1), to insure our passes work fine in most cases.

Passed:
- Flattening: `-O2 -mllvm -fla` 控制流平坦化，引入一个或多个状态变量和一个大的 switch-case 语句来实现
- BogusControlFlow: `-O2 -mllvm -bcf` 在程序中插入条件语句和跳转
- Substitution: `-O2 -mllvm -sub` 开启指令替换
- GlobalsEncryption: `-O2 -mllvm -gle` 全局变量加密
- MBAObfuscation: `-O2 -mllvm -mba -mllvm -mba-prob=100`  100% 的适用情况下都会应用 MBA 混淆。这是一个混淆程度的量化表示，它告诉混淆器对所有可能的代码路径使用 MBA 混淆技术
- FullProtection (**HIGHLY RECOMMENDED**): `-mllvm -mba -mllvm -mba-prob=100 -mllvm -fla -mllvm -gle`

See [check.sh](check.sh) and [test/secp256k1](test/secp256k1/).
