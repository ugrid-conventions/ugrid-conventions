# UGRID Conventions

[![Build Status](https://travis-ci.org/ugrid-conventions/ugrid-conventions.svg?branch=master)](https://travis-ci.org/ugrid-conventions/ugrid-conventions)

<!-- TOC generated with "md_toc github README.md -i" -->
[](TOC)

- [UGRID Conventions](#ugrid-conventions)
    - [Overview](#overview)
    - [Contributors](#contributors)
    - [Contributing to UGRID](#contributing-to-ugrid)

[](TOC)


## Overview

This document is a standard for storing unstructured grid
(a.k.a. unstructured mesh, flexible mesh)
model data in a Unidata Network Common Data Form (NetCDF) file.

Note: It's recommended to give netCDF files adhering to the UGRID conventions the global attribute `Conventions = 'UGRID-1.0'`; if the file is also compliant with the Climate and Forecasting conventions then the global attribute can be extended to `Conventions = 'CF-1.6, UGRID-1.0'` with `1.6` replaced by the appropriate CF conventions version number. As of CF version 1.11, the CF-conventions include the UGRID 1.0 conventions by reference. In that case `Conventions = 'CF-1.11'` or later CF-version attribute is sufficient.
* [Version 1.0](http://ugrid-conventions.github.io/ugrid-conventions/)

The standard was developed over a period of several years through the
[UGRID Google Group](https://groups.google.com/forum/#!forum/ugrid-interoperability)
which had members from many different unstructured grid modeling communities
(including SELFE, ELCIRC, FVCOM, ADCIRC).
From these discussions Bert Jagers (Deltares) created the first draft of this document, and the community worked to develop version 1.0.

## Contributors

People who have contributed to this standard:

* Bert Jagers
* David Stuebe
* Tom Gross
* Chris Barker
* Brian Zelenke
* Rich Signell
* Bob Oehmke
* Alex Crosby
* Karen Schuchardt
* David Ham
* Brian Blanton
* Cristina Forbes
* Charles Seaton
* Dave Forrest
* Bill Howe
* Geoff Cowles
* Phil Elson


## Contributing to UGRID

The UGRID conventions are developed in public at <https://github.com/ugrid-conventions/ugrid-conventions>.
Anybody is welcome to contribute using the common open-source GitHub workflow ([fork and branch](https://gist.github.com/Chaser324/ce0505fbed06b947d962)).

The definitive source of the conventions can be found in the ``src`` directory in the ``master`` branch.
To generate the HTML form of the conventions, ``mkdocs`` is used.
The ``requirements.txt`` file in the UGRID repository is the recommended approach to installing the necessary tools:

```
$ pip install --file requirements.txt
```

The docs are then built using standard ``mkdocs`` commands.
For example, the following command will build the conventions pages and place them in a directory called ``_site``:

```
$ mkdocs build -f mkdocs.yml
```
