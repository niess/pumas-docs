# Introduction

PUMAS performs the transport of relativistic $\mu$ or $\tau$ in both forward and
backward Monte Carlo. The library is written in C99 with the Standard Library as
sole dependency. PUMAS is thread safe by design. The library also allows to
generate muons or taus energy loss tables in the Particle Data Group
([PDG](https://pdg.lbl.gov/2020/AtomicNuclearProperties/index.html)) format. A
compilation of pre-computed material tables for PUMAS is available from the
[pumas-materials](https://github.com/niess/pumas-materials) project.  For muons
one can also use the whole set of tabulations provided online by the PDG.
Note however that those are less accurate than PUMAS ones above 100 TeV due
to improvements on the photonuclear cross-section (see e.g.
[Sokalski _et al._](https://arxiv.org/abs/hep-ph/0201122)).
{: .justify}

## Source and license

The PUMAS source is hosted on [GitHub](https://github.com/niess/pumas). It is
available under the terms of the GNU LGPLv3 license. See the provided
[LICENSE](https://github.com/niess/pumas/blob/master/LICENSE) and
[COPYING.LESSER](https://github.com/niess/pumas/blob/master/COPYING.LESSER)
files. The [examples](https://github.com/niess/pumas/tree/master/examples)
however have a separate public domain license allowing them to be copied without
any restriction.
{: .justify}

## Reference

If you consider citing PUMAS please use the following reference:
[doi:10.1016/j.cpc.2018.04.001](https://doi.org/10.1016/j.cpc.2018.04.001).
Note that the corresponding paper is also available from arXiv as
[preprint](https://arxiv.org/abs/1705.05636).
{: .justify}

## Logo

For more pictorial citations, the following logos could be used:

| ![Logo](img/logo-gradient.png) | ![Logo](img/logo-black.png) |
|:-:|:-:|
| ([png](img/logo-gradient.png)) ([svg](img/logo-gradient.svg)) | ([png](img/logo-black.png)) ([svg](img/logo-black.svg))  |
