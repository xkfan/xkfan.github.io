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
