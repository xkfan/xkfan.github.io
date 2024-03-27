# LLVM

记录一些对llvm代码的分析以及使用技巧

## LLVM开发

TODO

1. How to enable gold as default
2. How to write a llvm pass
3. How to use LLVM as a library


[LLVM Language Reference Manual](https://llvm.org/docs/LangRef.html)

## Some useful options

### Controlling Value Names in LLVM IR

```bash
-fno-discard-value-names     # keeps actual names of variables,
                             # instead of emitting '%1', '%2', ...
-fno-integrated-as           # Disable the integrated assembler
```

### Diagnostics

```bash
-Rpass=loop-vectorize          # identifies loops that were successfully
                               # vectorized
-Rpass-missed=loop-vectorize   # identifies loops that failed vectorization
                               # and indicates if vectorization was specified
-Rpass-analysis=loop-vectorize # identifies the statements that caused
                               # vectorization to fail.
                               # If in addition '-fsave-optimization-record'
                               # is provided, multiple causes of vectorization
                               # failure may be listed
```
