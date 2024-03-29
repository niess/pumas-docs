# Introduction

The PUMAS library allows one to transport muon or tau leptons by forward or
backward Monte Carlo. The library is written in C99 with the standard library as
sole dependency. PUMAS is thread safe by design. PUMAS can also be used in order
to generate stopping power tables in the Particle Data Group
([PDG](https://pdg.lbl.gov/2020/AtomicNuclearProperties/index.html)) format. A
compilation of tables generated with PUMAS is available from the
[pumas-materials](https://github.com/niess/pumas-materials) repository.
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

## Issues

PUMAS physics results have been carefully validated through various tests
including comparison to other Monte Carlo engines. Updates are unit tested using
[GitHub
CI](https://docs.github.com/en/actions/guides/about-continuous-integration).
However, PUMAS is software and bugs happen. Whenever you were (un)lucky to come
accross one, please consider reporting it as a [GitHub
issue](https://github.com/niess/pumas/issues).
{: .justify}

## References

A detailed description of the PUMAS library, of its physics, its implementation
and its validation, is published in Computer Physics Communications, as
[10.1016/j.cpc.2022.108438](https://doi.org/10.1016/j.cpc.2022.108438)
([2206.01457](https://arxiv.org/abs/2206.01457)). The backward Monte Carlo
method is specifically discussed in
[10.1016/j.cpc.2018.04.001](https://doi.org/10.1016/j.cpc.2018.04.001)
([1705.05636](https://arxiv.org/abs/1705.05636)).
{: .justify}

## Logo

For more pictorial citations, the following logos could be used.

| ![Logo](img/logo-gradient.png) | ![Logo](img/logo-black.png) |
|:-:|:-:|
| ([png](img/logo-gradient.png)) ([svg](img/logo-gradient.svg)) | ([png](img/logo-black.png)) ([svg](img/logo-black.svg))  |
