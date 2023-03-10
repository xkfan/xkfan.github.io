# English Writing

## Standard/Canonical Usages

For more details we
**refer the reader to** [xx].

In other (**e.g.,** C) programs, ...

A serial loop that computes the remaining N%VF iterations is also
added for the case that N dost not **evenly divide by** VF.

Finally, the loop bound is transformed to reflect the new number of iterations,
**and if necessary**,
an epilog scalar loop is created to handle cases of loop bounds which
**do not divide by** the vectorization factor.

Using a standard loop-based vectorization scheme for SIMD,
multiple occurences of the same operation across consecutive iterations can be
grouped into single SIMD instructions, as shown in Figure 2,
where VF is the vectorization factor - the number of elements
that **fit in** a vector register.
(**We assume for clarity that** len **is divisible by**
VF).

An
**equation of state (hereinafter EOS)**
which relates the pressure and density fields must be imposed.

**For the sake of clarity** a cut-off limit of 2h
**will be considered from now on**.

Autovectorizaiton is a **mature research area**;
automatic detection of vector loops in serial code
**has been discussed in literature for more than a decade**.

Use semantics and default values that **comply with** GCC conventions.

Use terminology that does not **conflict with** existing terms used by
SIMD extensions or existing RTL operations-codes.

Before diving into the specifics of our implementation,
we introduce some basic terminology and notation.

**Last, but not least**, we always **strive to** generate statements that
will **eventually** translate into the most efficient instructions available
on a target.

Object bounds overflow
errors **are a** common source
of security vulnerabilities.
..., low-fat pointers **are a** recent scheme for ....

The contributions of the paper are **threefold**:
- **Comprehensive survey of alternative** outer-loop vectorization
techniques.
- **Experimental evaluation exhibiting the impact of** optimized
outer-loop vectorization **relative to** scalar and inner-loop vectorized
versions, using kernels of loop nests, on two platforms.
- ...
