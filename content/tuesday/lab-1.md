---
author: Meng Sun (Lead TA), Caleb Eastlund, Ducheng Lu
---

# Lab 1 

## Learning Goals

- Install GYRE
- Set up MESA to use GYRE
- Calculate period spacing using MESA and GYRE

## Acknowledgement

This tutorial is inspired by [similar labs for the 2025 MESA Summer School](https://mesa-leuven.4d-star.org/tutorials/friday/).

---

`MESA` is distributed with two codes for stellar oscillations:

- [`GYRE`](https://gyre.readthedocs.io/en/stable/), by R. H. D. Townsend, and
- [`ADIPLS`](https://ui.adsabs.harvard.edu/abs/2008Ap%26SS.316..113C/abstract), by J. Christensen-Dalsgaard.

The calculations performed by the two are essentially equivalent, with the main tradeoff being between performance and ease of use. In this tutorial, we will focus on `GYRE`, which is much easier to get started with.

> [!Tip]
> We will use the version of `GYRE` shipped with MESA for this tutorial (v8.1). However, you may wish to explore the latest release (v9.0) later, which includes significant performance improvements. See the release notes [here](https://github.com/rhdtownsend/gyre/releases/tag/v9.0).

## Building GYRE

### Download
When you download MESA, `GYRE` is automcatically included as one of the folder. For MESA version r25.12.1, the shipped GYRE version is 8.1. To be sure that you are viewing the docs for the correct version number of the GYRE version that you are using. You can check the version number that you have by:

`
ls $MESA_DIR/gyre/*.tar.gz
`

You will see something like
`
path_to_mesa/gyre/gyre-8.1.tar.gz
`
,where 8.1 tells you the version nubmer. 

On the website, you can change the version by clicking the small box on the right bottom corner.
![Screenshot of GYRE website](img/GYRE_site.png)

Optionally, if you would like to download the latest version of `GYRE`, you can download the source code from the corresponding website, put it to a directory to your taste, and do 
```shell
tar xf gyre-9.0.tar.gz
```

### Set Environment Variables
Secondly, we will set the environment variable `$GYRE_DIR` equal to the following:
```shell
export GYRE_DIR=$MESA_DIR/gyre$
```
>[!Tip]
> If you are using the `GYRE` shipped with MESA, the path should be 

Remember that this is best placed inside your shell's RC file in your home directory (usually `.bashrc` or equivalent), similarly to when you first installed MESA. **Don't forget to `source` this file to apply the changes to your terminal window!**

### Compile
Now, we can follow the GYRE installation guide from this point. Go ahead and compile:

```make -j -C $GYRE_DIR install```

### Test
Once that's complete, it's good practice to run the test suite to ensure nothing has gone wrong during the installation process:

```make -C $GYRE_DIR test```

> [!NOTE]
> If all the tests read "...succeeded" then you are good to move on to the next step. If that's not the case, ask your TA or a developer for help. 

---

## Use GYRE with MESA model

### GYRE namelist

Similar to MESA, GYRE takes an input file in the format of `namelist`.

```fortran

&model
/
 
&constants
/
 
&mode
/
 
&osc
/
 
&num
/
 
&scan
/
 
&grid
/
 
&rot
/
 
&ad_output
/
 
&nad_output
/

```
>[!Note]
> Don't forget to add an empty line at the end of the file!

The namelist is separated into different groups, a full explanation of the different groups and their members can be found [here](https://gyre.readthedocs.io/en/stable/ref-guide/input-files.html).


</div>
</details>

<details class="hx-border hx-border-blue-200 dark:hx-border-blue-200 hx-rounded-md hx-my-2">
<summary class="hx-bg-blue-100 dark:hx-bg-neutral-800 hx-text-blue-900 dark:hx-text-blue-200 hx-p-2 hx-m-0 hx-cursor-pointer">
<em><strong>Hint:</strong> Troubleshooting</em>
</summary>
<div class="hx-p-2">

Are you in the correct directory? Execute `ls` to make sure you see the normal contents of a MESA work directory, including `inlist`, `mk`, and `rn`.

Is your MESA environment set up correctly? Ensure that executing `mesasdk_version` prints out the version of the MESA SDK you have installed, and similarly ensure that `echo $MESA_DIR` prints out the path to your MESA installation.

It's also possible that the executables (like `./mk` and `./rn`) are not executable as a side effect of downloading these files from the web. You can make them executable with the following command:

```bash
chmod +x mk rn clean re
```

</div>
</details>
