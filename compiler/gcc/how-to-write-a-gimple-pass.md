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

## 3. Write the new pass

### 3.1 Describe the *`pass_data`* of the new pass

Each gimple pass contains a pass description in a data structure called *`pass_data`*

```c
const pass_data pass_data_gimple_pass =
{
  GIMPLE_PASS, /* type */
  "gimple_pass", /* name */
  OPTGROUP_ALL, /* optinfo_flags */
  TV_GIMPLE_PASS, /* tv_id */
  PROP_ssa, /* properties_required */
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

We define the new gimple pass as *`pass_gimple_pass`*, which is a derived class of *`gimple_opt_pass`*.

```c
class pass_gimple_pass : public gimple_opt_pass
{
public:
  pass_gimple_pass (gcc::context *ctxt)
    : gimple_opt_pass (pass_data_gimple_pass, ctxt)
  {}

  /* opt_pass methods: */
  virtual bool gate (function *) {
      return flag_tree_ssa_gimple_pass;
  }

  virtual unsigned int execute (function *);

}; // class pass_gimple_pass
```

A *`gimple_opt_pass`* includes two important functions:

- *`gate`*: determines whether this pass will be executed.
- *`execute`*: describes how to perform this pass.

Function *`execute`* iterates over all basic blocks.
In each basic block, it iterates over all variable declares, dumps their names and types in the screen.

```c
unsigned int
pass_gimple_pass::execute (function *fun) {

  const char *fname = IDENTIFIER_POINTER (DECL_NAME (cfun->decl));
  fprintf(stderr, "fn name: %s\n", fname);

  /// traverse over blocks
  tree block;
  for (block = DECL_INITIAL (current_function_decl);
       block; block = BLOCK_CHAIN(block)) {
    tree t;
    /// traverse over trees
    for (t = BLOCK_VARS (block); t; t = TREE_CHAIN (t)) {
      /// dump var decls
      if (TREE_CODE(t) == VAR_DECL) {
        fprintf(stderr, "val: %s\n", print_generic_expr_to_str(t));
        fprintf(stderr, "val type: %s\n", print_generic_expr_to_str(TREE_TYPE (t)));
      }
    }
  }  

  return 0;
}
```

### 3.3 Define pass registration function

```c
gimple_opt_pass *
make_pass_gimple_pass (gcc::context *ctxt)
{
  return new pass_gimple_pass (ctxt);
}
```

### 3.4 *`tree-ssa-gimple-pass.c`*

Putting it all together

```patch
diff --git a/gcc/tree-ssa-gimple-pass.c b/gcc/tree-ssa-gimple-pass.c
new file mode 100644
index 000000000..6b66bedec
--- /dev/null
+++ b/gcc/tree-ssa-gimple-pass.c
@@ -0,0 +1,103 @@
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
+
+#include "cfgloop.h"
+#include "tree-vectorizer.h"
+#include "cfghooks.h"  // split_edge
+
+namespace {
+
+const pass_data pass_data_gimple_pass =
+{
+  GIMPLE_PASS, /* type */
+  "gimple_pass", /* name */
+  OPTGROUP_ALL, /* optinfo_flags */
+  TV_GIMPLE_PASS, /* tv_id */
+  PROP_ssa, /* properties_required */
+  0, /* properties_provided */
+  0, /* properties_destroyed */
+  0, /* todo_flags_start */
+  0, /* todo_flags_finish */
+};
+
+class pass_gimple_pass : public gimple_opt_pass
+{
+public:
+  pass_gimple_pass (gcc::context *ctxt)
+    : gimple_opt_pass (pass_data_gimple_pass, ctxt)
+  {}
+
+  /* opt_pass methods: */
+  virtual bool gate (function *) {
+      return flag_tree_ssa_gimple_pass;
+  }
+
+  virtual unsigned int execute (function *);
+
+}; // class pass_gimple_pass
+
+unsigned int
+pass_gimple_pass::execute (function *fun) {
+
+  const char *fname = IDENTIFIER_POINTER (DECL_NAME (cfun->decl));
+  fprintf(stderr, "fn name: %s\n", fname);
+
+  /// traverse over blocks
+  tree block;
+  for (block = DECL_INITIAL (current_function_decl);
+       block; block = BLOCK_CHAIN(block)) {
+    tree t;
+    /// traverse over trees
+    for (t = BLOCK_VARS (block); t; t = TREE_CHAIN (t)) {
+      /// dump var decls
+      if (TREE_CODE(t) == VAR_DECL) {
+        fprintf(stderr, "val: %s\n", print_generic_expr_to_str(t));
+        fprintf(stderr, "val type: %s\n", print_generic_expr_to_str(TREE_TYPE (t)));
+      }
+    }
+  }
+
+  return 0;
+}
+
+} // anon namespace
+
+gimple_opt_pass *
+make_pass_gimple_pass (gcc::context *ctxt)
+{
+  return new pass_gimple_pass (ctxt);
+}
```

## 4. Define the *`tv_id`* of our pass *`gimple_pass`*

*`tv_id`* is defined in *`timevar.def`*

```patch
diff --git a/gcc/timevar.def b/gcc/timevar.def
index 63c0b3306..23803049e 100644
--- a/gcc/timevar.def
+++ b/gcc/timevar.def
@@ -221,6 +221,7 @@ DEFTIMEVAR (TV_TREE_SWITCH_LOWERING,   "tree switch lowering")
 DEFTIMEVAR (TV_TREE_RECIP            , "gimple CSE reciprocals")
 DEFTIMEVAR (TV_TREE_SINCOS           , "gimple CSE sin/cos")
 DEFTIMEVAR (TV_TREE_WIDEN_MUL        , "gimple widening/fma detection")
+DEFTIMEVAR (TV_GIMPLE_PASS           , "gimple pass")
 DEFTIMEVAR (TV_TRANS_MEM             , "transactional memory")
 DEFTIMEVAR (TV_TREE_STRLEN           , "tree strlen optimization")
 DEFTIMEVAR (TV_TREE_MODREF	     , "tree modref")
 ```

## 5. Register our new pass

A pass is registered in *`tree-pass.h`*

```patch
diff --git a/gcc/tree-pass.h b/gcc/tree-pass.h
index 15693fee1..ee8053b26 100644
--- a/gcc/tree-pass.h
+++ b/gcc/tree-pass.h
@@ -437,6 +437,7 @@ extern gimple_opt_pass *make_pass_cse_reciprocals (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_cse_sincos (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_optimize_bswap (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_store_merging (gcc::context *ctxt);
+extern gimple_opt_pass *make_pass_gimple_pass (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_optimize_widening_mul (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_warn_function_return (gcc::context *ctxt);
 extern gimple_opt_pass *make_pass_warn_function_noreturn (gcc::context *ctxt);
 ```

## 6. Set the location of our new pass

The location of a pass is defined in *`passes.def`*
We set our new pass as the last gimple pass before the *`gimple`* code is lowered into *`rtl`* code.

```patch
diff --git a/gcc/passes.def b/gcc/passes.def
index e9ed3c7bc..5ca9cc8fe 100644
--- a/gcc/passes.def
+++ b/gcc/passes.def
@@ -413,6 +413,7 @@ along with GCC; see the file COPYING3.  If not see
   NEXT_PASS (pass_gimple_isel);
   NEXT_PASS (pass_cleanup_cfg_post_optimizing);
   NEXT_PASS (pass_warn_function_noreturn);
+  NEXT_PASS (pass_gimple_pass);
 
   NEXT_PASS (pass_expand);
 ```
