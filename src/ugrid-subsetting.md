UGRID Subsetting
=================


## Introduction

This project, and accompanying document, are all about a NetCDF standard for describing unstructured grids and the data/model results stored on them. Having such a standard is key to interoperability between different modeling systems and post-processing tools, etc. Once NetCDF supports this access protocols like Open-source Project for a Network Data Access Protocol (OPeNDAP) can be used.

However, there is an additional challenge. Modern unstructured grid models can use very large grids and thus produce extremely large results sets. However, a particular client application may only need a spatial subset of that data. Ideally, there would be a way for a client to download only the portion of the model domain required.

With structured grids, you can make good use of OPeNDAP's ability to slice arrays to get a subset of the data that is useful. However, with UGRIDs, data in a contiguous region in the spatial domain is generally not contiguous in the data arrays. So to do subsetting, the system needs to understand the topology of the grid, select the appropriate discontiguous subset of the data and grid, and put all that together into a still-correct UGRID dataset.

This can be done on the client side, figuring out the grid geometry and requesting the required data via OPeNDAP. However, OPeNDAP does not support requesting discontiguous data, so it is necessary to make many small requests to the server and put it back together on the client. This not only means a lot of complex client-side code, but it's also painfully slow to do many small requests, rather than one large one. In fact, with some testing, for anything but tiny subsets, it's faster to grab the entire grid than make many small requests for a subset.

## OPeNDAP subsetting protocol

To address this issue, OPeNDAP, Inc., under a grant from NOAA, has developed an extension to the Hyrax server that supports server-side subsetting of UGRIDs via OPeNDAP server-side functions. As the implementation uses the existing OPeNDAP server-side function protocol, it should be usable with standard OPeNDAP clients, and it should be possible to implement the functionally with the same application programming interface (API), in any OPeNDAP server.

Documentation of the server-side function API is here:

[UGRID subsetting docs](http://docs.opendap.org/index.php/OPULS:_UGrid_Subsetting)

## Hyrax implementation

Recent development builds of Hyrax include the new functionality, so it should be available in the next Hyrax release.

The source code for the Hyrax implementation is here:

[Hyrax subsetting code](https://scm.opendap.org/svn/trunk/ugrid_functions/)


## GridFields

The subsetting code in the Hyrax server is implemented by the [GridFields](http://www.stccmop.org/gridfields) library. The source code for the version of GridFields can be found here:

[GridFields source](https://scm.opendap.org/svn/trunk/hyrax-dependencies/downloads/gridfields-1.0.3.tar.gz)


The GridFields library has a much broader scope of capabilities than is utilized with this simple subsetting function, and could be used for a number of other UGRID-manipulating and evaluating tasks.

## Other OPeNDAP servers.

As of this writing, I am unaware of any other implementations of server-side subsetting in an OPeNDAP server. However it is hoped that other OPeNDAP servers will add such functionality eventually, and hopefully using the same API, as documented by the document linked to above.

Gridfields is written in C++, and thus could be used with any other server that can call C++. It also has Python bindings, and thus could be used with a Python implementation, such as [PyDAP](http://www.pydap.org/).

Alternatively, the same functionality could be implemented with a different library for the subsetting code itself. Perhaps a Java library for use with the [THREDDS](http://www.unidata.ucar.edu/software/thredds/current/tds/TDS.html) server.
