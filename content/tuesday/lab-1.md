---
author: Meng Sun (Lead TA), Caleb Eastlund, Ducheng Lu
---

# Lab 1 

## Learning Goals

- Install GYRE
- Set up MESA to use GYRE
- Calculate period spacing using MESA and GYRE

> Acknowledgement
This tutorial is inspired by [similar labs for the 2025 MESA Summer School](https://mesa-leuven.4d-star.org/tutorials/friday/).

---

## Building GYRE

### Your star’s personal sound system

`MESA` is distributed with two codes for stellar oscillations:

- [`GYRE`](https://gyre.readthedocs.io/en/stable/), by R. H. D. Townsend, and
- [`ADIPLS`](https://ui.adsabs.harvard.edu/abs/2008Ap%26SS.316..113C/abstract), by J. Christensen-Dalsgaard.

The calculations performed by the two are essentially equivalent, with the main tradeoff being between performance and ease of use. In this tutorial, we will focus on `GYRE`, which is much easier to get started with.

> [!Tip]
> We will use the version of `GYRE` shipped with MESA for this tutorial (v8.1). However, you may wish to explore the latest release (v9.0) later, which includes significant performance improvements. See the release notes [here](https://github.com/rhdtownsend/gyre/releases/tag/v9.0).

<!-- ### Download GYRE -->
### Almost free lunch: download GYRE
When you download MESA, `GYRE` is automcatically included as one of the folder. For MESA version r26.4.1, the shipped GYRE version is 8.1. To be sure that you are viewing the docs for the correct version number of the GYRE version that you are using. You can check the version number that you have by:

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

<!-- ### Set Environment Variables -->
### Bonding session: set environment variables
Secondly, we will set the environment variable `$GYRE_DIR` to the path to source directory of gyre.

```shell
export GYRE_DIR=path/to/gyre
```

>[!Tip]
> If you are using the `GYRE` shipped with MESA, the path should be 
> ```shell
> export GYRE_DIR=$MESA_DIR/gyre/gyre
> ```

Remember that this is best placed inside your shell's RC file in your home directory (usually `.bashrc` or equivalent), similarly to when you first installed MESA. **Don't forget to `source` this file to apply the changes to your terminal window!**

<!-- ### Compile -->
### Now we cook: compile
Now, we can follow the GYRE installation guide from this point. Go ahead and compile:

```make -j -C $GYRE_DIR install```

<!-- ### Test -->
### The moment of truth: test
Once that's complete, it's good practice to run the test suite to ensure nothing has gone wrong during the installation process:

```make -C $GYRE_DIR test```

> [!NOTE]
> If all the tests read "...succeeded" then you are good to move on to the next step. If that's not the case, ask your TA or a developer for help. 

## Use GYRE with MESA model

### A small kick: get the MESA model
In this tutorial, we will focus on the usage of GYRE, so we have prepared a simple standard 5 solar mass model to get you started. 

{{% callout type="error" %}}
TODO: include the download links
{{% /callout %}}

### Know your GYRgon: GYRE namelist

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

The namelist is separated into different groups, a full explanation of the different groups and their members can be found [here](https://gyre.readthedocs.io/en/stable/ref-guide/input-files.html). In the following we will discuss the sections that we will use for our use.

### Model

Here's where we tell GYRE what type of model to read in and calculate frequencies for. [The docs](https://gyre.readthedocs.io/en/stable/ref-guide/input-files/model-group.html) have a full explanation of all the options.

We will add the following lines here:

```fortran
&model
    model_type = 'EVOL'
    file = './LOGS/profile1.data.FGONG'
    file_format = 'FGONG'
/
```
- `model_type` 
As described by the docs, this will tell GYRE that we are using an external evolutionary model, and that file is called `profile##.data.GYRE` and it's using MESA's default GYRE file format. 


### Mode
This namegroup defines which modes you want to calculate. You can state the angular degree ($\ell$) and the azimuthal order ($m$). For each type of mode we will need one extra `&mode` namegroup. For now we will leave the azimuthal order at its default value ($m=0$); it will come up later when we include rotation. We will also assign the modes with a tag to differentiate between them in other namegroups. For this lab using `'radial'` for the  $\ell=0$ modes and `'non-radial'` for all others is enough. 

|📋 TASK |
|:--|
| Add the instructions to calculate the `l=0` oscillation modes into your `gyre.in` file and give them the corresponding tag.|

{{< details title="ℹ️ SOLUTION " closed="true" >}}

```fortran
&mode
    l = 0
    m = 0
    n_pg_min = -150
    n_pg_max = -10
/
```
{{< /details >}}

{{% callout type="error" %}}
TODO: explain `n_pg_min` and `n_pg_max` choices.
{{% /callout %}}


{{% callout type="error" %}}
TODO: check all the website links
{{% /callout %}}

{{% callout type="info" %}} Info box {{% /callout %}}
{{% callout type="warning" %}} Warning box {{% /callout %}}
{{% callout type="error" %}} Red box (error style) {{% /callout %}}
{{% callout type="success" %}} Success box {{% /callout %}}

### Oscillation parameters

In this namegroup we can set various options on how to calculate the oscillations themselves. Which physics and assumptions to use, different boundary conditions, how to scale various parameters and so on. All of the options and their default values can be found [here](https://gyre.readthedocs.io/en/stable/ref-guide/input-files/osc-params.html).

|📋 TASK |
|:--|
| As per default, GYRE assumes that at the outer boundary of the model, the stellar surface, the density vanishes. Instead we want to follow a more accurate description from Christensen-Dalsgaard (2008). Find and set the corresponding variable. To be consistent, you should also adjust `variables_set`.  |

{{< details title="ℹ️ HINT " closed="true" >}}

Search for the `outer_bound` variable.

{{< /details >}}

{{< details title="ℹ️ SOLUTION " closed="true" >}}

```fortran
&osc
    outer_bound = 'VACUUM'
/
```
{{< /details >}}

#### Numerical Parameters
Here goes everything to do with numerical parameters. Again, we leave nearly everything at default (see [here](https://gyre.readthedocs.io/en/stable/ref-guide/input-files/num-params.html)) and only increase the difference scheme from a second-order to a fourth-order Gauss-Legendre collocation.

```fortran
&num
    diff_scheme = 'COLLOC_GL4'
/
```

#### Frequency Scan Parameters

This section tells GYRE in which frequency range it will scan for the oscillation eigenfunctions. The modes that are of interest to us are those closest to $\nu_{\rm max}$. We suggest to set `freq_min` and `freq_max` to $\nu_{\rm max} \pm 3 \Delta\nu$. Look into your `history.data` file to find the values of $\nu_{\rm max}$ and $\Delta\nu$ for your model with the profile you choose in the "Model" section. (You wrote this down in [our google sheet](https://docs.google.com/spreadsheets/d/1pAcvlfqOga0JNZo3cjJeZaErVd4youLYHyAmAu3NmSE/edit?usp=sharing) from Lab1, if you need a refresher). We also set the units of our chosen range to μHz (`freq_units = 'UHZ'`) 

Next, we define the resolution, the number of points of our scan.  Generally, we need the grid spacing to be smaller than the eigenfrequency separation of adjacent modes, across the full range of the grid. There is no absolute rule as how to determine this value beforehand, but a too low value could miss some modes and a high value means a longer runtime. For our radial p-modes `n_freq = 200` is enough but because we calculate the oscillations also for red giants we do not only have p-modes but also mixed-modes (for the non-radial modes). These will need a higher value. To be more efficient we therefore define two different `&scan` namegroups. To define which modes correspond to which scan we set `tag_list = 'radial'` (the tag we defined in `&mode`):

```fortran
&scan
  tag_list = 'radial'
  grid_type = 'LINEAR'  ! Scan grid uniform in frequency
  freq_min =  ###       ! Minimum frequency to scan from
  freq_max =  ###       ! Maximum frequency to scan to  
  n_freq = 200          ! Number of frequency points in scan
  freq_units = 'UHZ'
/
```

For the non-radial modes g-modes become relevant. These are not equally spaced in frequency like the p-modes but in period. The higher the star evolves up the RGB ($\nu_{\rm max}$ decreases) the more tightly spaced they get. Therefore it is recommended to scan the grid not uniform in frequency (`grid_type = 'LINEAR'`) but uniform in period (`grid_type = 'INVERSE'`).

```fortran
&scan
  tag_list = 'non-radial'
  grid_type = 'INVERSE' ! Scan grid uniform in inverse frequency
  freq_min = ###        ! Minimum frequency to scan from
  freq_max = ###        ! Maximum frequency to scan to
  n_freq = 3000         ! Number of frequency points in scan
  freq_units = 'UHZ'
/
```

|📋 TASK |
|:--|
| Copy- Paste the two `&scan` namelist groups and set `freq_min` and `freq_max` to $\nu_{\rm max} \pm 3*\Delta\nu$. You can find the values for $\nu_{\rm max}$ and $\Delta\nu$ in your `history.data` file. Choose the values with the same `model_number` as your profile selected in the "Model" section|
 
#### Grid Parameters
In this group you can modify the spatial resolution of the grid generated by Gyre to calculate the eigenmodes. If it is too low the eigenmodes are not resolved and the accuracy of the results suffers. The grid spacing should be smaller than the scale of the smallest significant variation of the eigenfunction. [Here](https://gyre.readthedocs.io/en/stable/user-guide/understanding-grids/spatial-grids.html#spatial-grids) you can see how the following weighting parameters are used to define and refine the grid. The defaults are set to 0 but for a appropriate resolution you should choose higher values.

|📋 TASK |
|:--|
| Search in the Gyre documentation for appropriate values for `w_osc`, `w_exp` and `w_ctr` and set them in the corresponding namelist group |

{{< details title="ℹ️ SOLUTION" closed="true" >}}

You can find them for example at the bottom of [this page](https://gyre.readthedocs.io/en/stable/user-guide/understanding-grids/spatial-grids.html#recommended-values).

```fortran
&grid
  w_osc = 10 ! Oscillatory region weight parameter
  w_exp = 2  ! Exponential region weight parameter
  w_ctr = 10 ! Central region weight parameter
/
```
{{< /details >}}

#### Output

Now it's time to tell gyre what parameters it should save. Similar to profiles and history files we also have two different output types in GYRE. The summary file is as the name implies a summary of all oscillation modes GYRE found. You can include all parameters that describe the mode with a single value e.g. $l$, $m$, $n$, frequency, inertia, ... . The name of the file is given by the `summary_file` and the parameters it should include are given with `summary_item_list`. You can rename this if you want to run more than one model (e.g. `summary_numax500.txt`)(Or put it in the same folder as the details as described in the next paragraph). All the options can be found [here](https://gyre.readthedocs.io/en/stable/ref-guide/output-files.html). For this lab we will also change the file format to a textfile so it becomes human readable instead of the default HDF5 file.

```fortran
&ad_output
  summary_file = 'summary.txt'                         
  summary_item_list = 'l,m,n_pg,n_p,n_g,freq,E_norm,E_p,E_g'
  summary_file_format = 'TXT'  
...
```

The detail file on the other hand gives you a detailed description of one mode. We have several files for one run and therefore we give a template of the name instead to avoid overwriting the file for each mode we calculate. E.g. `'%l'` will be replaced with the angular degree of the corresponding mode and `'%n'` with its radial order. All options can be found [here](https://gyre.readthedocs.io/en/stable/ref-guide/input-files/output-params.html). A good idea is to make a new folder for all your detail files if you want to run several models and provide the path to it (for example: `detail_template = 'details_numax500/detail.l%l.n%n.h5`). Be aware that GYRE does not generate any folders for you and you will get an error if it can't find the folder. Keep an eye out on your storage, one file may not be large but it sums up quite quickly when you have a lot of modes (That's why we will stick to the HDF5 format). Additionally we will define the frequency units again with `freq_units`.

```fortran
...
  detail_template = 'detail.l%l.n%n.h5'               
  detail_item_list = 'l,n_pg,omega,x,xi_r,xi_h,c_1,As,
                      V_2,Gamma_1,rho,P,R_star,M_star' 
  !detail_file_format = 'TXT'  

  freq_units = 'UHZ'                      
/

&nad_output
/
```

We put everything in the namegroup `&ad_output`. It tells gyre that we will assume adiabatic conditions. If we want to instead calculate it non-adiabatically we would put it in `&nad_output` instead. 

|📋 TASK |
|:--|
| Put the above lines to define your output into the `&ad_output` of your `gyre.in` file. Adjust the location of your output file as you see fit. |

{{<details title="The final look of our gyre.in" closed="true">}}

```fortran
&constants
/

&model
    model_type = 'EVOL'
    file = './LOGS/profile1.data.FGONG'
    file_format = 'FGONG'
/

&mode
    l = 1
    m = 0
    n_pg_min = -150
    n_pg_max = -10
/

&osc
    outer_bound = 'VACUUM'
/

&scan
    grid_type = 'INVERSE'
    freq_min = 0.2
    freq_max = 2.5
    n_freq = 5000
    freq_units = 'CYC_PER_DAY'
    freq_min_units = 'CYC_PER_DAY'
    freq_max_units = 'CYC_PER_DAY'
/

&rot
/

&grid
/

&num
    diff_scheme = 'COLLOC_GL2'
/

&ad_output
    summary_file = 'summary_zams.h5'
    summary_item_list = 'l,n_pg,m,freq,period'
    summary_file_format = 'HDF'
    freq_units = 'CYC_PER_DAY'

    detail_template = 'detail_central_h1_zams/detail.l%l.n%n.h5'               
    detail_item_list = 'l,n_pg,omega,x,xi_r,xi_h,c_1,As,V_2,Delta_g,Gamma_1'
/

&nad_output
/
```
{{< /details>}}

>[!Tip]
> Sometimes it can be useful to check out the [troubleshooting](https://gyre.readthedocs.io/en/stable/user-guide/troubleshooting.html) section of the website.