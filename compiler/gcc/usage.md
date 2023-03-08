# GCC

记录一些对gcc代码的分析以及使用技巧

## Show the optimizations ENABLED/DISABLED by a specific Oxx level

```bash
gcc -Q -O3 --help=optimizers
```
