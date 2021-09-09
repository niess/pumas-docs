# Stopping power tables

An overview of processes implemented in PUMAS is provided in section 3.1 of the
[reference paper](https://doi.org/10.1016/j.cpc.2018.04.001)
([arXiv:1705.05636](https://arxiv.org/abs/1705.05636)). For radiative processes
different [cross-section models](tutorials.md#tuning-the-physics) can be
selected.
{: .justify}

## Stopping power tables in PUMAS

Stopping power tables are generated by PUMAS as an intermediary step when
tabulating the physical properties of materials, e.g. cross-sections,
soft energy loss, CSDA range, etc. The generated tables conform to the
[PDG](https://pdg.lbl.gov/2020/AtomicNuclearProperties/index.html) format.
{: .justify}

!!! note
    By default [`pumas_context_create`][API_1] reuses already existing stopping
    power tables. This can be overwritten by providing a
    [`PUMAS_PHYSICS_SETTINGS`][PUMAS_PHYSICS_SETTINGS] argument with the
    *update* attribute set to *1*, forcing all stopping power tables to be
    generated again, i.e. updated.
    {: .justify}

A compilation of stopping power table computed with PUMAS is available from the
[pumas-materials](https://github.com/niess/pumas-materials) repository.  It is
also possible to use the original PDG stopping power tables instead of PUMAS one
by substituting (overwriting) the PUMAS generated ones. Yet, PUMAS tables are
more accurate than the PDG ones at high energy due to improvements on the
cross-sections of radiative processes (see e.g.  [Sandrock _et
al._](https://iopscience.iop.org/article/10.1088/1742-6596/1690/1/012005)).
{: .justify}


## Generating tables with PUMAS

The *dry* mode of operation of the [`pumas_context_create`][API_1] function is
meant specifically for generating stopping power tables (see
[`pumas_physics_settings`][PUMAS_PHYSICS_SETTINGS]). In this mode the physics
is only partially created resulting in lower CPU usage. The file
[examples/pumas/tabulate.c](https://github.com/niess/pumas/blob/master/examples/pumas/tabulate.c)
provides an example of this usage.
{: .justify}

[API_1]: api/index.html##HEAD/group/physics/pumas_physics_create
[PUMAS_PHYSICS_SETTINGS]: api/index.html##HEAD/type/pumas_physics_settings