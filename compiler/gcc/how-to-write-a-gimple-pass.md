# How to write a gimple pass

We use a simple gimple pass to illustrate how to write a gimple pass.
The pass prints the following infomation to the screen:

1. function name
2. the name and type of each name

This example is based on gcc-11.1.0.

## 1. Create a new source file

We create a new source file named *`tree-ssa-gimple-pass.c`*.

And add the newly created file in *`Makefile.in`*.

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
