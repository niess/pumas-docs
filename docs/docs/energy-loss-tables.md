# Energy loss tables

A detailed description of energy loss process, as implemented in PUMAS, can
be found in section 3.1 of the
[reference paper](https://doi.org/10.1016/j.cpc.2018.04.001)
([arXiv:1705.05636](https://arxiv.org/abs/1705.05636)).
{: .justify}

## Energy loss tables in PUMAS

In order to allow for different energy loss models, the $\mu$ ($\tau$) average
energy loss computed by PUMAS is normalized to user supplied values, read from
materials specific tables. These tables must conform to the
[PDG](https://pdg.lbl.gov/2020/AtomicNuclearProperties/index.html) text format.
A large compilation of material data is available online from the PDG.  In
addition, those materials data are also provided by the
[pumas-materials](https://github.com/niess/pumas-materials) project. The latter
have been self-generated with PUMAS. For $\mu$, PUMAS data tables are more
accurate than the PDG ones above 100 TeV energies due to improvements on the
photonuclear cross-section (see e.g.
[Sokalski _et al._](https://arxiv.org/abs/hep-ph/0201122)).
{: .justify}

## Generating tables with PUMAS

Energy loss tabulation can be generated with PUMAS using its own physics models.
This is done by creating a specific `pumas_physics` object with the
`pumas_physics_create_tabulation`. Then the `pumas_physics_tabulate` function
allows to generate the tabulations.
{: .justify}
