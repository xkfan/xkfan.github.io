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

## 3. Write the new pass

### 3.1 Describe the *`pass_data`* of the new pass

Each rtl pass contains a pass description in a data structure called *`pass_data`*

```c
const pass_data pass_data_rtl_pass =
{
  RTL_PASS, /* type */
  "rtl_pass", /* name */
  OPTGROUP_ALL, /* optinfo_flags */
  TV_RTL_PASS, /* tv_id */
  0, /* properties_required */
  0, /* properties_provided */
  0, /* properties_destroyed */
  0, /* todo_flags_start */
  0, /* todo_flags_finish */
};
```

- The *`type`* field describes the type of the pass, *`GIMPLE_PASS`* or *`GIMPLE_PASS`*.
- The *`name`* field describes the name of the pass.
- The *`tv_id`* field defines a *`timing variable`* id, which is used to measure run-time performance of the compiler.

### 3.2 Define the main part of the new pass

We define the new rtl pass as *`pass_rtl_pass`*, which is a derived class of *`rtl_opt_pass`*.

```c
class pass_rtl_pass : public rtl_opt_pass
{
public:
  pass_rtl_pass (gcc::context *ctxt)
    : rtl_opt_pass (pass_data_rtl_pass, ctxt)
  {}

  /* opt_pass methods: */
  virtual bool gate (function *) { return flag_rtl_pass; }
  virtual unsigned int execute (function *);

}; // class pass_rtl_pass
```

A *`rtl_opt_pass`* includes two important functions:

- *`gate`*: determines whether this pass will be executed.
- *`execute`*: describes how to perform this pass.

Function *`execute`* iterates over all basic blocks.
In each basic block, it counts the number of instructions, dumps the data in the screen.

```c
unsigned int
pass_rtl_pass::execute (function *fun) {

  const char *fname = IDENTIFIER_POINTER (DECL_NAME (cfun->decl));
  fprintf(stderr, "fname: %s\n", fname);

  basic_block bb;

  FOR_EACH_BB_FN (bb, fun) {
    rtx_insn *insn;
    int inst_num = 0;
    for (insn = BB_HEAD(bb); insn != NEXT_INSN(BB_END(bb));
         insn = NEXT_INSN(insn)) {
      if (GET_CODE(insn) == INSN)
        inst_num++;
    }
    fprintf(stderr, "basic block: %d, inst num: %d\n", bb->index, inst_num);
  }
  fprintf(stderr, "\n");

  return 0;
}
```

### 3.3 Define pass registration function

```c
rtl_opt_pass *
make_pass_rtl_pass (gcc::context *ctxt)
{
  return new pass_rtl_pass (ctxt);
}
```

### 3.4 *`rtl-pass.c`*

Putting it all together

```patch
diff --git a/gcc/rtl-pass.c b/gcc/rtl-pass.c
new file mode 100644
index 000000000..b9ecef1ae
--- /dev/null
+++ b/gcc/rtl-pass.c
@@ -0,0 +1,94 @@
+#include "config.h"
+#include "system.h"
+#include "coretypes.h"
+#include "backend.h"
+#include "target.h"
+#include "rtl.h"
+#include "tree.h"
+#include "gimple.h"
+#include "predict.h"
+#include "alloc-pool.h"
+#include "tree-pass.h"
+#include "ssa.h"
+#include "optabs-tree.h"
+#include "gimple-pretty-print.h"
+#include "alias.h"
+#include "fold-const.h"
+#include "gimple-fold.h"
+#include "gimple-iterator.h"
+#include "gimplify.h"
+#include "gimplify-me.h"
+#include "stor-layout.h"
+#include "tree-cfg.h"
+#include "tree-dfa.h"
+#include "tree-ssa.h"
+#include "builtins.h"
+#include "internal-fn.h"
+#include "case-cfn-macros.h"
+#include "optabs-libfuncs.h"
+#include "tree-eh.h"
+#include "targhooks.h"
+#include "domwalk.h"
+#include "cfgloop.h"
+#include "tree-vectorizer.h"
+#include "cfghooks.h"  // split_edge
+
+
+namespace {
+
+const pass_data pass_data_rtl_pass =
+{
+  RTL_PASS, /* type */
+  "rtl_pass", /* name */
+  OPTGROUP_ALL, /* optinfo_flags */
+  TV_RTL_PASS, /* tv_id */
+  0, /* properties_required */
+  0, /* properties_provided */
+  0, /* properties_destroyed */
+  0, /* todo_flags_start */
+  0, /* todo_flags_finish */
+};
+
+class pass_rtl_pass : public rtl_opt_pass
+{
+public:
+  pass_rtl_pass (gcc::context *ctxt)
+    : rtl_opt_pass (pass_data_rtl_pass, ctxt)
+  {}
+
+  /* opt_pass methods: */
+  virtual bool gate (function *) { return flag_rtl_pass; }
+  virtual unsigned int execute (function *);
+
+}; // class pass_rtl_pass
+
+unsigned int
+pass_rtl_pass::execute (function *fun) {
+
+  const char *fname = IDENTIFIER_POINTER (DECL_NAME (cfun->decl));
+  fprintf(stderr, "fname: %s\n", fname);
+
+  basic_block bb;
+
+  FOR_EACH_BB_FN (bb, fun) {
+    rtx_insn *insn;
+    int inst_num = 0;
+    for (insn = BB_HEAD(bb); insn != NEXT_INSN(BB_END(bb));
+         insn = NEXT_INSN(insn)) {
+      if (GET_CODE(insn) == INSN)
+        inst_num++;
+    }
+    fprintf(stderr, "basic block: %d, inst num: %d\n", bb->index, inst_num);
+  }
+  fprintf(stderr, "\n");
+
+  return 0;
+}
+
+} // anon namespace
+
+rtl_opt_pass *
+make_pass_rtl_pass (gcc::context *ctxt)
+{
+  return new pass_rtl_pass (ctxt);
+}
```

## 4. Define the *`tv_id`* of our pass *`rtl_pass`*

*`tv_id`* is defined in *`timevar.def`*

```patch
diff --git a/gcc/timevar.def b/gcc/timevar.def
index 23803049e..418312800 100644
--- a/gcc/timevar.def
+++ b/gcc/timevar.def
@@ -222,6 +222,7 @@ DEFTIMEVAR (TV_TREE_RECIP            , "gimple CSE reciprocals")
 DEFTIMEVAR (TV_TREE_SINCOS           , "gimple CSE sin/cos")
 DEFTIMEVAR (TV_TREE_WIDEN_MUL        , "gimple widening/fma detection")
 DEFTIMEVAR (TV_GIMPLE_PASS           , "gimple pass")
+DEFTIMEVAR (TV_RTL_PASS              , "rtl pass")
 DEFTIMEVAR (TV_TRANS_MEM             , "transactional memory")
 DEFTIMEVAR (TV_TREE_STRLEN           , "tree strlen optimization")
 DEFTIMEVAR (TV_TREE_MODREF	     , "tree modref")
 ```
