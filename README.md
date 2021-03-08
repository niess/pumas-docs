# Documentation for the PUMAS library
( **S**emi **A**nalytical **MU**ons -or taus- **P**ropagation, *backwards* )

## Description

This repo contains the [source](docs/) of the [PUMAS][PUMAS] documentation as
well as as well as a static HTML description of the C library API. The
documentation is generated with [MkDocs][MKDOCS] and the C
library API with a [Docurium](https://github.com/niess/docurium) fork.

## Usage

The generated documention(s) are available from
[ReadTheDocs](https://pumas.readthedocs.io/en/latest/). Alternativelly you can
also browse the documentation locally with
[MkDocs][MKDOCS] e.g. as:
```bash
git clone https://github.com/niess/pumas-docs.git docs
cd docs/docs
mkdocs serve
```
Then direct your web browser to localhost:8000.

## License

The PUMAS library is under the **GNU LGPLv3** license. See the provided
[LICENSE](LICENSE) and [COPYING.LESSER](COPYING.LESSER) files.

[PUMAS]: http://niess.github.io/pumas
[MKDOCS]: https://www.mkdocs.org
