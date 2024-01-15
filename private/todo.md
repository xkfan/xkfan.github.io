# TODO

## LMUL of RISCV V

Check if the compiler automatically determines the value of lmul based on the v registers used.

If the auto-vec in compiler only generates code with lmul = 1,
then,
we have something to do.

## multi length slp for VLA SIMD Architecture

Targeting CGO'24 / ICPP'25 / PACT'25

Implement in LLVM

Both with loop vectorization enabled & disabled.

SVE (Gem5 simulation)

RISCV V (sg2042), plus [rvv-rollback](https://github.com/RISCVtestbed/rvv-rollback)

## partial slp in sve

## loop split flatten

## libm

sic/cos/exp/log etc.

## SIMD of lbm on VLA architecture

## strcmp\_sve on VLA architecture

## stencil on riscv-v

ICPP'24

evaluation on sg2042.

Refer to the paper of stencil on SVE.

Zhiwei Zhang.
