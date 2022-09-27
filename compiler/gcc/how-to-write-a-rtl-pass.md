# How to write a rtl pass

We use a simple rtl pass to explain how to write a rtl pass.
The pass prints the following infomation to the screen:

1. function name
2. the number of insts in each basic block

This example is based on gcc-11.1.0.

## 1. Create a new source file

We create a new source file named *`rtl-pass.c`*.

And add the newly created file in *`gcc/Makefile.in`*.

```patch
diff --git a/gcc/Makefile.in b/gcc/Makefile.in
index a9c3c3c00..e83e8a1b9 100644
--- a/gcc/Makefile.in
+++ b/gcc/Makefile.in
@@ -1543,6 +1543,7 @@ OBJS = \
 	reorg.o \
 	resource.o \
 	rtl-error.o \
+	rtl-pass.o \
 	rtl-ssa/accesses.o \
 	rtl-ssa/blocks.o \
 	rtl-ssa/changes.o \
```

## 2. Create a compiler option for the new pass

We add a new compiler option *`-frtl-pass`* to control the execution of our pass.

### 2.1 Create a compiler option

The compiler option *`-frtl-pass`* is defined in *`common.opt`*.

```patch
diff --git a/gcc/common.opt b/gcc/common.opt
index 387e7580a..eb730c1f2 100644
--- a/gcc/common.opt
+++ b/gcc/common.opt
@@ -3057,6 +3057,10 @@ ftree-ssa-gimple-pass
 Common Var(flag_tree_ssa_gimple_pass) Optimization
 Perform gimple optimization.
 
+frtl-pass
+Common Var(flag_rtl_pass) Optimization
+Perform rtl optimization.
+
 ; -fverbose-asm causes extra commentary information to be produced in
 ; the generated assembly code (to make it more readable).  This option
 ; is generally only of use to those who actually need to read the
```

### 2.2 Initialize the internal flag variable

An internal flag variable *`flag_rtl_pass`* is related to the compiler option *`-frtl-pass`*.
The flag variable is initialized in *`opts.c`*.

```patch
diff --git a/gcc/opts.c b/gcc/opts.c
index 093747e85..f892c80ef 100644
--- a/gcc/opts.c
+++ b/gcc/opts.c
@@ -1770,6 +1770,7 @@ enable_fdo_optimizations (struct gcc_options *opts,
   SET_OPTION_IF_UNSET (opts, opts_set, flag_unroll_jam, value);
   SET_OPTION_IF_UNSET (opts, opts_set, flag_tree_loop_distribution, value);
   SET_OPTION_IF_UNSET (opts, opts_set, flag_tree_ssa_gimple_pass, value);
+  SET_OPTION_IF_UNSET (opts, opts_set, flag_rtl_pass, value);
 }
 
 /* -f{,no-}sanitize{,-recover}= suboptions.  */
```
