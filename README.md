UGRID Conventions
=================
* [Stable Versioned Releases (0.9,..)](https://github.com/ugrid-conventions/ugrid-conventions/releases)
* [Development Version](https://github.com/ugrid-conventions/ugrid-conventions/blob/master/ugrid-conventions.md) 

This project contains a proposed standard for storing unstructured (or flexible mesh) model data in a network common data form (NetCDF) file. Our focus is on data for environmental applications and hence we start from the Climate & Forecast (CF) metadata conventions which has been the standard in climate research for many years, and is increasingly adopted by others as the metadata standard (e.g. NASA,Open Geospatial Consortium). The CF conventions allow you to unambiguously provide the geospatial and temporal coordinates for scientific data, but assumes that the horizontal topology may be inferred from the i,j indices of structured grids.  This proposal adds conventions for specifying the topology for unstructured (e.g. triangular) grids.  

