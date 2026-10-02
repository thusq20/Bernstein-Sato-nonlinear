# 0. Computing the annihilator

This stage computes the multivariate annihilator of

```text
F = (f1,f2,f3) = (y, z*y-x^2, z^2+x^2*y+z*y^2+x^3),
f = f1*f2*f3,
F^s = f1^s1*f2^s2*f3^s3.
```

Here `z` is a spatial variable and `Dz` differentiates it; the central
parameters are `s(1),s(2),s(3)`. This is not the single-parameter annihilator
of `f^s` obtained by imposing `s1=s2=s3`, and is not a Bernstein--Sato ideal.
The operators are named `B_1,...,B_13` in the paper; the standard Singular
container name `annFs` is retained for compatibility, so `annFs[i]` means `B_i`.

## Method

`0_Annfs.sing` calls the original Singular `dmodideal.lib` routine

```singular
def rawRing=annihilatorMultiFs(F,0,1,4);
```

It uses the same settings as the recorded original computation: engine `0`,
first-order syzygy input enabled (`1`), and output ordering `4`. The final
argument is the output ordering, not an engine number. No custom replacement
algorithm or Bernstein--Sato elimination is used. The routine constructs the
full multivariate annihilator by elimination. The reference input is never
loaded during the computation.

The returned ring has nine variables ordered as spatial variables,
derivatives, and parameters. A positional ring map gives them the explicit
names `x,y,z,Dx,Dy,Dz,s(1),s(2),s(3)`, without relying on library-generated
collision-safe names such as `_x` or `_Dx`. Every pairwise commutator is
checked. The checkpoint recreates the genuine Weyl algebra over `Q[s]`,
not a commutative polynomial ring:

```singular
ring base=0,(x,y,z,Dx,Dy,Dz,s(1..3)),(dp(6),dp(3));
matrix comm[9][9];comm[1,4]=1;comm[2,5]=1;comm[3,6]=1;
def annRing=nc_algebra(1,comm);setring annRing;
```

## Two entry points

Use Singular with `dmodideal.lib` and run in a **fresh private directory**
containing copies of this stage's two scripts and `inputs/`. The scripts
write to their current working directory. Do not run in the reference input
directory or in an earlier completed run: output filenames are fixed.

```bash
Singular --no-rc -q 0_Annfs.sing < /dev/null > compute.log 2>&1
Singular --no-rc -q verify_annfs.sing < /dev/null > verify.log 2>&1
```

The first entry point performs the full calculation and writes:

- `annfs.sing`: standalone, reloadable nine-variable Weyl checkpoint.
- `ann_generators.sing`: `ideal annFs=...;` for inclusion in an existing
  matching Weyl ring, retaining the ordered thirteen `B_i`.
- `B_generators.sing`: loads `annfs.sing` and defines `poly B1,...,B13` as
  convenient aliases. Load this optional entry in a fresh Singular process.

The second entry point must be a **new Singular process**. It reloads
`annfs.sing`, checks all variable names and all 36 pairwise commutators,
checks thirteen nonzero generators, and compares each `B_i` exactly with
`inputs/reference_ann_generators.sing`. It also reloads the separate
`ann_generators.sing` include and compares it with the complete checkpoint.
This is exact ordered polynomial equality, not numerical evaluation or
equality up to units, reordering, or ideal membership.

The reference contains only the canonical audit's `ideal annFs` body, renamed
to `ideal referenceAnn`, with token substitutions `a -> z` and `Da -> Dz`.
No coefficients or generator ordering are changed. Its provenance is the
original package's `certificates/audit/annfs.sing`; the computation method is
from `certificates/provenance/server-full.46nrje/compute.sing` and `run.sh`.

## Acceptance and resource reporting

Accept a run only when both processes finish, their logs contain no Singular
error (`?` error lines or `ERROR`), and their final markers are present:

```text
ANNFS_COMPUTATION_COMPLETE
ANNFS_REFERENCE_ORDERED_EQUAL
```

Useful intermediate markers include `ANNFS_COMPUTE_START`,
`ANNFS_SAVED_B1` through `ANNFS_SAVED_B13`, `ANNFS_SAVED_COUNT=13`,
`ANNFS_CPU_SECONDS=...`, `ANNFS_EXACT_MATCH_B1` through
`ANNFS_EXACT_MATCH_B13`, and `ANNFS_VERIFIED_COUNT=13`.
Do not rely on Singular's process exit status alone. Both routines use
`ERROR(...)` inside their top-level procedure so failed checks cannot reach
the routine's final success marker. Missing files or parsing errors must
also cause the external runner to reject the log.

The parent package's bounded runner is responsible for the requested local
15-minute wall-clock limit, the memory cap, and `/usr/bin/time -v` plus memory
sampling. This README records a reproducible method, not a claim that a new
full calculation has already finished; consult the runner's actual logs and
report for measured time, peak memory, exit status, and verification outcome.

Exact output ordering or normalization can differ between Singular versions.
Such a run may generate the same ideal but still fail this deliberately
strict thirteen-operator reference comparison. Investigate the discrepancy;
do not silently replace the reference or weaken the acceptance criterion.
