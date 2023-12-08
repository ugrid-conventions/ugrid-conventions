<p style="color:red;font-size:50px;font-style:oblique">
.. DRAFT .. DRAFT .. DRAFT ..
</p>

***Status : first draft***
<br>_Hopefully, this can evolve into an agreed final form
by the time of the next UGRID release._

# Conformance rules for UGRID Conventions v1.x

This information is intended to follow the practice established by the
[CF conformance pages][cf_conformance], as attached to the [CF conventions][cf_conventions].

In that spirit, this is likewise "intended to be a concise summary" of the
main convention, and "if there are any discrepancies ... the conventions document
is the ultimate authority."
 
Each reference statement is provided with a short code :
"Rxxx" for requirements and "Axxx" for advisory recommendations,
in the manner of the [Flake8 error and warning codes][flake8_codes], and the [PyCodeStyle ones][pycodestyle_codes].
<br>Future editions will maintain these reference numbers, but possibly add new ones.  They can be used as definitive
references by a conformance-checker utility. [^10]

**This version** is consistent with :

  * UGRID v1.0 
  * [CF version 1.11][cf_current_version]

---

[TOC]

## Preamble : terms and definitions
- A ***mesh variable*** is a variable with a `cf_role` attribute of `"mesh_topology"`. [^2]
- A ***varlist*** is a string attribute consisting of a space-separated list of variable names
    - these must be names of variables existing in the same dataset : this is implicit in all uses
- A ***coordinate attribute*** of a mesh is a varlist with one of the following names [^2] [^4]
    - `node_coordinates`
    - `edge_coordinates`
    - `face_coordinates`
- A ***connectivity attribute*** of a mesh is a varlist with one of the following names [^2] [^4]
    - `edge_node_connectivity`
    - `face_node_connectivity`
    - `face_edge_connectivity`
    - `edge_face_connectivity`
    - `face_face_connectivity`
    - `boundary_node_connectivity`
- A ***mesh coordinate*** is a variable listed in a coordinate attribute of a mesh
- A ***mesh connectivity*** is a variable listed in a connectivity attribute of a mesh [^2]
- A mesh is the ***parent mesh*** of those mesh coordinates or mesh connectivities which are included in any of 
  its coordinate or connectivity attributes :
  This _defines_ the "parent mesh" of any mesh coordinate or mesh connectivity. [^1]
- The ***parent mesh*** of a location index set is that named in its `mesh` attribute
- the ***element dimensions*** of a mesh are those indexing its elements (i.e. nodes, edges or faces),
  or mesh boundaries, within any of its mesh coordinates and mesh connectivities.
    - the **node** dimension is the single dimension of the node coordinates (should all be the same)
    - if there is an `edge_node_connectivity`, then there is an **edge** dimension
      (see [below](#edge-dimension) for how this is determined)
    - if there is a `face_node_connectivity`, then there is a **face** dimension
      (see [below](#face-dimension) for how this is determined)
    - if there is a `boundary_node_connectivity`, then there is a **boundary** dimension
      (see [below](#boundary-dimension) for how this is determined)
- the ***element dimension*** of a mesh connectivity or mesh coordinate is that one of its dimensions
  which is an element dimension of its parent mesh.
  N.B. there can only be one : see [R201](#R201) / [R304](#R304)
- A ***location index set*** is a variable with a `cf_role` attribute of `"location_index_set"` [^2]
- A ***mesh data variable*** is a CF data variable with either a `mesh` or a `location_index_set` attribute.


## Mesh variables
### Mesh variable requirements
For any mesh variable ..

- <a id="R101" href="#R101">R101</a> it must have a `cf_role` attribute,
    - <a id="R102" href="#R102">R102</a> with the value `"mesh_topology"`
- <a id="R103" href="#R103">R103</a> it must have a `topology_dimension` attribute, 
    - <a id="R104" href="#R104">R104</a> whose value is an integer between 0 and 2. [^4] [^5]
- all mesh connectivity and coordinate attributes must be varlists, that is ..
    - <a id="R105" href="#R105">R105</a> they are strings consisting of space-separated, valid netcdf variable names, and
    - <a id="R106" href="#R106">R106</a> the variables named all exist in the dataset
- <a id="R107" href="#R107">R107</a> all mesh connectivity attributes must contain a single variable name
- <a id="R108" href="#R108">R108</a> all elements of all mesh coordinate attributes must be
  [valid mesh coordinates](#mesh-coordinate-variables)
- <a id="R109" href="#R109">R109</a> all mesh connectivity attributes must be
  [valid mesh connectivities](#mesh-connectivity-variables)
- <a id="R110" href="#R110">R110</a> it must have a `node_coordinates` attribute
- an `edge_node_connectivity` attribute ..
    - <a id="R111" href="#R111">R111</a> *must not* be present, if `topology_dimension` is 0. [^5]
    - <a id="R112" href="#R112">R112</a> *must* be present, if `topology_dimension` is 1. 
- <a id="R113" href="#R113">R113</a> a `face_node_connectivity` attribute must be present
    _if and only if_ `topology_dimension` is 2  
- <a id="R114" href="#R114">R114</a> a `boundary_node_connectivity` attribute may be present,
  _only if_ `topology_dimension` is 2 [^6]
- if the mesh has an `edge_node_connectivity`, then it has an <a id="edge-dimension">**edge dimension**</a>, which
    - ***either*** is given by the `edge_dimension` attribute, _if there is one_, in which case
        - <a id="R115" href="#R115">R115</a> `edge_dimension` must be the name of a dataset dimension
    - ***or***, _if there is no `edge_dimension` attribute_, the edge dimension is the _first_ dimension of the `edge_node_connectivity` 
    - when an edge dimension exists ..
        - <a id="R116" href="#R116">R116</a> if any edge connectivity has the edge dimension as its _second_ dimension,
          (instead of its first), then the mesh _must_ have an `edge_dimension` attribute
- if the mesh has a `face_node_connectivity`, then it has a <a id="face-dimension">**face dimension**</a>, which ..
    - ***either*** is given by the `face_dimension` attribute, _if there is one_, in which case
        - <a id="R117" href="#R117">R117</a> `face_dimension` must be the name of a dataset dimension
    - ***or***, _if there is no `face_dimension` attribute_, the edge dimension is the _first_ dimension of the `face_node_connectivity` 
    - when a face dimension exists ..
        - <a id="R118" href="#R118">R118</a> if any face connectivity has the face dimension as its _second_ dimension,
          (instead of its first), then the mesh _must_ have a `face_dimension` attribute
- if the mesh has a `boundary_node_connectivity`, then it has a <a id="boundary-dimension">**boundary dimension**</a>,
  which is the first dimension of the `boundary_node_connectivity` variable [^7]
- <a id="R119" href="#R119">R119</a> a `face_face_connectivity` attribute may only exist
  if the mesh has a face dimension
- <a id="R120" href="#R120">R120</a> a `face_edge_connectivity` attribute may only exist
  if the mesh has both a face dimension and an edge dimension
- <a id="R121" href="#R121">R121</a> an `edge_face_connectivity` attribute may only exist
  if the mesh has both a face dimension and an edge dimension
- <a id="R122" href="#R122">R122</a> a `face_dimension` attribute may only exist if the mesh has a 
  [face dimension](#face-dimension)
- <a id="R123" href="#R123">R123</a> an `edge_dimension` attribute may only exist if the mesh has an 
  [edge dimension](#edge-dimension)

### Mesh variable recommendations

- <a id="A101" href="#A101">A101</a> it should have no dimensions
- <a id="A102" href="#A102">A102</a> it should not have a `standard_name` attribute
- <a id="A103" href="#A103">A103</a> it should not have a `units` attribute
- <a id="A104" href="#A104">A104</a> it should not share any of its element dimensions with other meshes [^11]
- <a id="A105" href="#A105">A105</a> if it has multiple element dimensions, they should all be different
- <a id="A106" href="#A106">A106</a> it should not have any attributes ending in "_connectivity", "_coordinates" or 
  "_dimension" which are not valid UGRID terms [^12]


## Mesh coordinate variables
For any mesh coordinate variable ..

### Mesh coordinate variable requirements
- <a id="R201" href="#R201">R201</a> it must map a single dimension
    - <a id="R202" href="#R202">R202</a> which is the appropriate element dimension of the parent mesh
- <a id="R203" href="#R203">R203</a> any `bounds` attribute must refer to a suitable CF bounds variable matching its own properties and dimensions

### Mesh coordinate variable recommendations
- <a id="A201" href="#A201">A201</a> it should have _one and only one_ parent mesh [^1]
- <a id="A202" href="#A202">A202</a> it should have a floating-point data type
- <a id="A203" href="#A203">A203</a> it should have a `standard_name` attribute which is a valid CF standard-name
  <br>(see : [CF conformance][cf_conformance] section 3.3)
- <a id="A204" href="#A204">A204</a> it should have a `units` attribute which is a valid CF unit
  <br>(see [CF conformance][cf_conformance] section 3.1)
- if it has a `bounds` attribute, then
    - <a id="A205" href="#A205">A205</a> the data values of the bounds variable should match those calculated
      from the relevant UGRID element coordinates and connectivities [^8]
- <a id="A206" href="#A206">A206</a> if its location is 'node', then it should not have a `bounds` attribute.

## Mesh connectivity variables
### Mesh connectivity variable requirements
For any mesh connectivity variable ..

- <a id="R301" href="#R301">R301</a> it must have a `cf_role` attribute, which
    - <a id="R302" href="#R302">R302</a> is a valid connectivity attribute name, and 
    - <a id="R303" href="#R303">R303</a> matches its referring connectivity attribute in the parent mesh
- <a id="R304" href="#R304">R304</a> it must exactly have two dimensions, of which
    - <a id="R305" href="#R305">R305</a> one is an element dimension of its parent mesh, and
    - <a id="R306" href="#R306">R306</a> the other is not an element dimension
- <a id="R307" href="#R307">R307</a> its element dimension must be that of the _first_ location named in its `cf_role` [^13]
- <a id="R308" href="#R308">R308</a> if it has a `cf_role` [^13] of `edge_node_connectivity` or `boundary_node_connectivity`,
  then its non-element dimension must have length 2
- <a id="R309" href="#R309">R309</a> any `start_index` attribute must have one of the values (0, 1).
- <a id="R310" href="#R310">R310</a> if it has a `cf_role` of `edge_node_connectivity` or `boundary_node_connectivity`,
  then it must ***not*** contain any 'missing' indices
- <a id="R311" href="#R311">R311</a> if it has a `cf_role` of `face_node_connectivity`, then each face index must contain
  at least 3 non-'missing' indices

### Mesh connectivity variable recommendations
- <a id="A301" href="#A301">A301</a> it should have _one and only one_ parent mesh [^1]
- <a id="A302" href="#A302">A302</a> it should have an integer type
- <a id="A303" href="#A303">A303</a> any `start_index` attribute should have an integer type
- <a id="A304" href="#A304">A304</a> if it has a `cf_role` of `edge_node_connectivity` or `boundary_node_connectivity`,
  then it should ***not*** have a `_FillValue` attribute
- <a id="A305" href="#A305">A305</a> if it contains any "missing" indices, then it should have a `_FillValue` attribute :
  <br>I.E. it does *not* rely on a netcdf "default fill-value"
- if there is a `_FillValue` attribute, then
    - <a id="A306" href="#A306">A306</a> it should have the same type as the connectivity variable itself, and
    - <a id="A307" href="#A307">A307</a> it should have a negative value
- <a id="A308" href="#A308">A308</a> all its non-missing values should be valid indices into the relevant dimension of
  the parent mesh, i.e. the _second_ location named in the `cf_role` [^3] [^8].

## Location index set variables
For any location index set variable ..

### Location index set variable requirements
- <a id="R401" href="#R401">R401</a> it must have a `cf_role` attribute with the value `"location_index_set"`
- <a id="R402" href="#R402">R402</a> it must have a `mesh` attribute,
  which is a string containing the name of a valid mesh variable in the dataset
- <a id="R403" href="#R403">R403</a> it must have a `location` attribute, which is one of `face`, `edge` or `node`, and 
    - <a id="R404" href="#R404">R404</a> the corresponding location must exist in the parent mesh
- <a id="R405" href="#R405">R405</a> it must have a single dimension
- <a id="R406" href="#R406">R406</a> any `start_index` attribute must have one of the values (0, 1).

### Location index set variable recommendations
- <a id="A401" href="#A401">A401</a> it should have an integer type
- <a id="A402" href="#A402">A402</a> it should not contain any missing points
- <a id="A403" href="#A403">A403</a> it should not have a `_FillValue` attribute
- <a id="A404" href="#A404">A404</a> the length of its single dimension should be less than or equal to the corresponding
  element dimension of its parent mesh
- <a id="A405" href="#A405">A405</a> its values should all be distinct, and 
    - <a id="A406" href="#A406">A406</a> they should all be valid indexes into the relevant element dimension
      of its parent mesh [^3] [^8]
- <a id="A407" href="#A407">A407</a> any `start_index` attribute should have an integer type


## Mesh data variables
### Mesh data variable requirements
- if there is a `mesh` attribute
    - <a id="R501" href="#R501">R501</a> there must be no `location_index_set` attribute
    - <a id="R502" href="#R502">R502</a> the `mesh` must be the name of a valid mesh variable in the dataset
    - <a id="R503" href="#R503">R503</a> there must be a `location` attribute,
        - <a id="R504" href="#R504">R504</a> which is one of `face`, `edge` or `node`, and 
        - <a id="R505" href="#R505">R505</a> the corresponding dimension must exist in the parent mesh
- if there is a `location_index_set` attribute
    - <a id="R506" href="#R506">R506</a> there must be no `mesh` attribute
    - <a id="R507" href="#R507">R507</a> there must be no `location` attribute
    - <a id="R508" href="#R508">R508</a> the `location_index_set` must name a valid location index set variable in the dataset
- its dimensions ..
    - <a id="R509" href="#R509">R509</a> must contain one and only one mesh element dimension, which
    - <a id="R510" href="#R510">R510</a> is the appropriate element dimension of its mesh and location,
      as given by it's own `mesh` and `location` properties, or by those of its `location_index_set`


## Other
### Other recommendations

- <a id="A901" href="#A901">A901</a> all dataset contents (dimension, variables and their attributes) should also be
  CF compliant -- see: [CF conventions][cf_conventions].
- <a id="A902" href="#A902">A902</a> there should be a global `Conventions` attribute,
    - <a id="A903" href="#A903">A903</a> which contains a string section of the form `UGRID-<X.Y>`,
      where "<X.Y\>" is an appropriate release version of the UGRID conventions. [^9]
- <a id="A904" href="#A904">A904</a> no netcdf variable should have a `cf_role` equal to any of the values defined here,
  unless it is one of the UGRID variable types defined here to use one
  <br>-- i.e. a mesh, location index set or connectivity variable
- <a id="A905" href="#A905">A905</a> no dataset variable should have a `cf_role` which is not _either_
  one of the valid terms described here, _or_ one of the CF-defined `cf_role` values [^14]


[^1]: From the given definitions, any mesh connectivity or -coordinate must logically _have_ a
parent mesh.  We *intend* that it should always have exactly and only one.

[^2]: for correctness testing (only), we may broaden the definitions of these items in order to detect some obvious
errors.  For example, we will attempt to check as a mesh variable any variable that _either_ has a `cf_role` of `mesh_topology`
_or_ is named in the `mesh` attribute of a data variable or location index set variable.

[^3]: "valid" element indices are values within the range of the corresponding element dimension of the parent mesh,
allowing for any `start_index` offset.

[^4]: For now at least, we _exclude_ mention of full-3d meshes, whose status is currently in question : 
cf. [this GitHub discussion][volume_status], as this would add considerable complexity here.

[^5]: For now, we _allow_ 0-d meshes (those with nodes only).  Though, this is not explicitly mentioned in the UGRID v1.0 pages.

[^6]: It is not supported to have boundaries in a 1-D mesh (I.E. one with only edges), though this is not logically
impossible.  If a specific need arises, this could change in future.

[^7]: Since there is no provision for a `boundary_dimension` attribute, the boundary dimension is always the
_first_ dimension of a `boundary_node_connectivity`, and its second dimension must always be 2
(i.e. it cannot be transposed).  If a specific need arises, this could change in future.

[^8]: Requirements on _values_ of variables may not always be practical to check,
since this requires loading all of their data.

[^9]: The form of the global `Conventions` attribute is determined by the [CF conformance pages][cf_conformance],
section 2.6.1.  However, the definition of UGRID release numbers is not yet finalised.

[^10]: Providing stable statement references allows conformance statements to evolve without invalidating earlier
versions of a checker program.  We also provide a linkable web anchor for each one.

[^11]: if a mesh does not share any element dimensions with other meshes, it follows that it also will not share any
  element coordinates or connectivities with other meshes.

[^12]: invalid 'lookalike' attributes, which are not defined by UGRID, include such as :
  "node_face_connectivity", "boundary_coordinates", "boundary_dimension" or "node_dimension".

[^13]: for checking purposes, the identity of a connectivity is actually defined by the referring attribute in the parent mesh.
   However, if the connectivity is correctly formed, then its `cf_role` value will always be the same as this  -- cf:[R303](#R303).

[^14]: in the present [latest CF version][cf_current_version] the only `cf_role` terms defined are the discrete-geometry
  types : `timeseries_id`, `profile_id` and `trajectory_id`


[volume_status]: https://github.com/ugrid-conventions/ugrid-conventions/issues/53#issuecomment-832138564
[cf_conformance]: http://cfconventions.org/cf-conventions/conformance.html "CF Conventions Conformance pages"
[cf_conventions]: https://cfconventions.org/ "CF metadata conventions"
[cf_current_version]: https://cfconventions.org/Data/cf-conventions/cf-conventions-1.9/cf-conventions.html "CF version 1.9"
[flake8_codes]: https://flake8.pycqa.org/en/4.0.1/user/error-codes.html
[pycodestyle_codes]: https://pycodestyle.pycqa.org/en/2.8.0/intro.html#error-codes

