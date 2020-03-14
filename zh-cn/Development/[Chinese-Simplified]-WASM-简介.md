## 简介

PlatON区块链支持使用WebAssembly (WASM)来执行用户编写的智能合约，WASM是一种为栈式虚拟机设计的二进制指令集。WASM被设计为可供类似C/C++/Rust等高级语言的平台编译目标，最初设计目的是解决 JavaScript 的性能问题。WASM是由 W3C 牵头正在推进的 Web 标准，并得到了谷歌、微软和 Mozilla 等浏览器厂商的支持。

开发高性能和安全的智能合约，C++是最好的语言。PlatON WASM合约支持C++编写，同时在目前最为成熟的编译工具链clang/llvm的C/C++编译器基础上定制一个符合PlatON协议标准的编译器，本手册将指导用户如何使用C++编写一个PlatON的智能合约。