---
title: "ClexBasis (`basis.json`, `eci.json`)"
permalink: /formats/casm/clex/ClexBasis/
---

### Description

A description of a cluster expansion basis set and coefficients. In a CASM project, it is written to a `basis.json` file. It is the output format of the ClexBasis class.

It is also used as the format for input of effective cluster interations (ECI) (expansion coefficients) when the `basis.json` file is copied to an `eci.json` file and coefficient values are added using the [`eci`](#eci) attribute. Internally, CASM constructs the ECIContainer class from the `eci.json` file.

In a CASM project, the `basis.json` file is written to the [basis set directories]({{ "/formats/project_directory_structure/#basis-sets-dir" | relative_url }}): `<root>/basis_sets/<bset>/basis.json`.

The `eci.json` file is read from the [cluster expansion ECI directories]({{ "/formats/project_directory_structure/#cluster-expansions-dir" | relative_url }}): `<root>/cluster_expansions/<property>/<bset>/<calctype>/<ref>/<eci>/eci.json`.


### JSON Attributes List

ClexBasis (`basis.json`, `eci.json`) attributes:

| Name | Description | Format |
|-|-|-|
| [`bspecs`](#bspecs) | Basis set specifications | [ClexBasisSpecs] |
| [`orbits`](#orbits) | Cluster orbits and basis set functions | array of [Cluster Basis Set Orbit](#cluster-basis-set-orbit-json-object) |
| [`prim`](#prim) | Primitive crystal structure and DoF | [BasicStructure] |
| [`site_functions`](#site-functions) | Description of site functions at each sublattice | array of [Site Functions](#site-functions-json-object) |

---

Cluster Basis Set Orbit attributes:

| [`cluster_functions`](#cluster-functions) | Basis functions of the prototype cluster DoF values | array of [Cluster Function](#cluster-function-json-object) |
| [`linear_orbit_index`](#linear-orbit-index) | Linear cluster orbit index | int |
| [`mult`](#mult) | Number of equivalent clusters in the orbit | int |
| [`prototype`](#prototype) | Representation of the prototype cluster | [Prototype Cluster](#prototype-cluster-json-object) |

---

Cluster Function attributes:

| [`\\Phi_{<linear_function_index>}`](#function-formula) | Basis function formula, formatted for Latex | string |
| [`linear_function_index`](#linear-function-index) | Linear basis function index | int |
| [`eci`](#eci) | Effective cluster interaction value | number |

---

Prototype Cluster attributes:

| [`invariant_group`](#invariant-group) | Indices of prim factor group operations that leave the prototype cluster invariant | array of int |
| [`invariant_group_descriptions`](#invariant-group-descriptions) | Prototype cluster invariant group operation descriptions | array of string |
| [`max_length`](#max-length) | Maximum distance between sites in the cluster. | number |
| [`min_length`](#min-length) | Minimum distance between sites in the cluster. | number |
| [`sites`](#sites) | Cluster site coordinates | number |

---

Site Functions attributes:

| [`asym_unit`](#asym-unit) | Asymmetric unit index for the sublattice | int |
| [`occ`](#occ) | Occupation site basis functions | dict  |
| [`<site_dof_key>`](#site-dof) | Continuous DoF site basis functions | dict |
| [`sublat`](#sublat) | Sublattice index | int |


### JSON Attributes Description

#### ClexBasis JSON object

- {: #bspecs } `bspecs`: [BasisFunctionSpecs]

  A copy of the basis set specifications (`bspecs.json`) used to generate the basis set.

- {: #orbits } `orbits`:  array of [Cluster Basis Set Orbit](#cluster-basis-set-orbit-json-object).

  An array of cluster basis set orbit objects, one for each orbit generated, including the null cluster orbit.

- {: #prim } `prim`: [BasicStructure]

  A copy of the prim used to generate the basis set.

- {: #site-functions } `site_functions`: array of [Site Functions](#site-functions-json-object), `shape=(n_sublattice,)`

  The `site_functions` array contains one site functions object for each sublattice in the prim.


#### Cluster Basis Set Orbit JSON object

- {: #cluster-functions} `cluster_functions`: array of [Cluster Function](#cluster-function-json-object)

  An array describing the cluster functions associated with the prototype cluster.

- {: #linear-orbit-index} `linear_orbit_index`: int

  Linear cluster orbit index

- {: #mult} `mult`: int

  Cluster multiplicity, the number of symmetrically equivalent clusters in the orbit.

- {: #prototype} `prototype`: [Prototype Cluster](#prototype-cluster-json-object)

  Information about one cluster (the "prototype") in the orbit of symmetrically equivalent clusters.


#### Cluster function JSON object

- {: #function-formula} `\\Phi_{<linear_function_index>}`: string

  Basis function formula, formatted for Latex. This is the formula for the basis function evaluated from site DoF values on the prototype cluster sites and global DoF values. The complete basis set also includes symmetrically the equivalent basis functions for each cluster in the orbit.

  Example:

      "\\Phi_{24}" : "\\sqrt{1/2}(e_1\\phi_{0,0}(s_{0})\\phi_{0,0}(s_{1})+e_3\\phi_{0,0}(s_{0})\\phi_{0,0}(s_{1}))"

  In this example:
  - ``\\phi_{b,i}`` are site basis functions, where `b` is a sublattice index and `i` is a function index at that site
  - `s_{0}`, `s_{1}` are the occupation values at sites in the clusters, where `<site_index>` is an index into the sites in the cluster.
  - `e_1`, `e_3` are strain DoF component values
  - In general, other site and global DoF component values may also be included in the formulas using the standard basis names or user-specified `axis_names` as defined in the prim.

- {: #linear-function-index} `linear_function_index`: int

  Linear basis function index.

- {: #eci} `eci`: number (optional)

  Basis function coefficient value. Not printed in `basis.json`. Optional value in the `eci.json` file. Not including the `eci` attribute indicates the coefficient value is zero and the corresponding basis functions may not need to be evaluated.


#### Prototype cluster JSON object

- {: #invariant-group} `invariant_group`: array of int

  Indices (begins with 0) of symmetry operations in the prim factor group which leave the prototype cluster invariant (up to lattice vector translations).

- {: #invariant-group-descriptions} `invariant_group_descriptions`: array of string

  Descriptions of the invariant group operations, following the conventions of the International Tables for Crystallography, and using the fractional representation for coordinates and vectors.

  Example:

      "invariant_group_descriptions" : [
        "1",
        "2 x, 0.5, -x",
        "2 0, y, 0",
        "2 x, 0.5-x, x",
        "m x, y, x",
        "m 2*x, 0.5-x+y, -2*y",
        "m x, y, -x",
        "-1 0.0000000 0.5000000 0.0000000"
      ]

- {: #max-length} `max_length`: number

  Maximum distance between sites in the cluster.

- {: #min-length} `min_length`: number

  Minimum distance between sites in the cluster.

- {: #sites } `sites`: array of integral site coordinates

  Sites are represented using integral site coordinates `[b, i, j, k]`, where b=sublattice index, and i,j,k are lattice vector indices.

  Example:

      "sites" : [
        [ 0, 0, 0, 0 ],
        [ 0, 0, 1, 0 ]
      ]

#### Site functions JSON object

- {: #asym-unit } `asym_unit`: int

  Asymmetric unit index. Sublattices with the same `asym_unit` value are equivalent under prim factor group operations.

- {: #occ } `occ`: dict

  Specifies the value of occupation site basis functions as a function of which occupant is at the site. The discrete site basis functions are named ``\\phi_{b,i}``, where `b` is the sublattice index and `i` is an index over site basis functions on this sublattice.

  Example: Discrete site basis functions, for a ternary system, using the `"occupation"` basis

      "occ" : {
        "basis" : {
          "\\phi_{0,0}" : {
            "A" : 0.000000000000,
            "B" : 1.000000000000,
            "C" : 0.000000000000
          },
          "\\phi_{0,1}" : {
            "A" : 0.000000000000,
            "B" : 0.000000000000,
            "C" : 1.000000000000
          }
        }
      }

  Example: Discrete site basis functions, for a ternary system, using the `"chebychev"` basis

      "occ" : {
        "basis" : {
          "\\phi_{0,0}" : {
            "A" : -1.224744871392,
            "B" : 0.000000000000,
            "C" : 1.224744871392
          },
          "\\phi_{0,1}" : {
            "A" : -0.707106781187,
            "B" : 1.414213562373,
            "C" : -0.707106781187
          }
        }
      }

- {: #site-dof } `<site_dof_key>`: dict

  Specifies the value of continuous site DoF basis functions as a function of the vector components, using the standard or user-specified axis names. The `<site_dof_key>` is the site DoF type as specified in the prim `dofs` dict for this sublattice. The continuous site basis functions are named ``var_{b}_{i}``, where `b` is the sublattice index and `i` is an index over continuous site basis functions of this type on this sublattice.

  Example: Atomic displacement site basis functions, using standard basis

      "disp" : {
        "basis" : {
          "var_{0}_{0}" : "dx_{0}",
          "var_{0}_{1}" : "dy_{0}",
          "var_{0}_{2}" : "dz_{0}"
        }
      }

  Example: Atomic displacement site basis functions, using user-specified basis in a 2d subspace

      "disp" : {
        "basis" : {
          "var_{0}_{0}" : "d1_{0}",
          "var_{0}_{1}" : "d2_{0}"
        }
      }

- {: #sublat } `sublat`: int

Sublattice index.


[BasicStructure]: {{ "/formats/casm/crystallography/BasicStructure" |  relative_url }}
[ClexBasisSpecs]: {{ "/formats/casm/clex/ClexBasisSpecs" |  relative_url }}