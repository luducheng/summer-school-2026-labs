---
author: Meng Sun (Lead TA), Caleb Eastlund, Ducheng Lu
---

# Lab 2 Changing Tempo

## Learning Goals
- Understand how chemical gradient can lead to the dips in g-mode period spacing
- Run MESA models from ZAMS up to TAMS 
- Use GYRE to compute g-modes at different time on the MS to see the change in dips with different chemical composition gradients

---

## Run a MESA model from ZAMS to TAMS

In this lab we will let the model evolve on the main sequence to see how the dips in the g-mode period spacing vary with different chemical gradient.

Start by copying the inlist for ZAMS to a new file:
```shell
cp inlist_zams inlist_tams
```

|📋 TASK |
|:--|
| Edit the new inlist to start with the ZAMS model we calculated in lab1 and evolve to TAMS.  |

**Question**: How to choose the termination condition?


{{< details title="ℹ️ HINT " closed="true" >}}


{{< /details >}}


{{< details title="ℹ️ SOLUTION " closed="true" >}}

```fortran
&controls
    xa_central_lower_limit_species(1) = 'h1'
    xa_central_lower_limit(1) = 0.0001
/
```
{{< /details >}}