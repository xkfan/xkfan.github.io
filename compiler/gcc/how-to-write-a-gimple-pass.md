# How to write a gimple pass

We use a simple gimple pass to illustrate how to write a gimple pass.
The pass prints the following infomation to the screen:

1. function name
2. the name and type of each name

This example is based on gcc-11.1.0.

## 1. Create a new source file

We create a new source file named *`tree-ssa-gimple-pass.c`*.

And add the newly created file in *`gcc/Makefile.in`*.

```patch
diff --git a/gcc/Makefile.in b/gcc/Makefile.in
index 8a5fb3fd9..a9c3c3c00 100644
--- a/gcc/Makefile.in
+++ b/gcc/Makefile.in
@@ -1629,6 +1629,7 @@ OBJS = \
 	tree-ssa-dom.o \
 	tree-ssa-dse.o \
 	tree-ssa-forwprop.o \
+	tree-ssa-gimple-pass.o \
 	tree-ssa-ifcombine.o \
 	tree-ssa-live.o \
 	tree-ssa-loop-ch.o \
```

## 2. Create a compiler option for the new pass

We add a new compiler option *`-ftree-ssa-gimple-pass`* to control the execution of our pass.

### 2.1 Create a compiler option

The compiler option *`-ftree-ssa-gimple-pass`* is defined in *`common.opt`*.

```patch
diff --git a/gcc/common.opt b/gcc/common.opt
index a75b44ee4..387e7580a 100644
--- a/gcc/common.opt
+++ b/gcc/common.opt
@@ -3053,6 +3053,10 @@ ftree-scev-cprop
 Common Var(flag_tree_scev_cprop) Init(1) Optimization
 Enable copy propagation of scalar-evolution information.
 
+ftree-ssa-gimple-pass
+Common Var(flag_tree_ssa_gimple_pass) Optimization
+Perform gimple optimization.
+
 ; -fverbose-asm causes extra commentary information to be produced in
 ; the generated assembly code (to make it more readable).  This option
 ; is generally only of use to those who actually need to read the
```

### 2.2 Initialize the internal flag variable

An internal flag variable *`flag_tree_ssa_gimple_pass`* is related to the compiler option *`-ftree-ssa-gimple-pass`*.
The flag variable is initialized in *`opts.c`*.

```patch
diff --git a/gcc/opts.c b/gcc/opts.c
index 24bb64198..093747e85 100644
--- a/gcc/opts.c
+++ b/gcc/opts.c
@@ -1769,6 +1769,7 @@ enable_fdo_optimizations (struct gcc_options *opts,
   SET_OPTION_IF_UNSET (opts, opts_set, flag_loop_interchange, value);
   SET_OPTION_IF_UNSET (opts, opts_set, flag_unroll_jam, value);
   SET_OPTION_IF_UNSET (opts, opts_set, flag_tree_loop_distribution, value);
+  SET_OPTION_IF_UNSET (opts, opts_set, flag_tree_ssa_gimple_pass, value);
 }
 
 /* -f{,no-}sanitize{,-recover}= suboptions.  */
```
