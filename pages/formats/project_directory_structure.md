---
layout: default
---
## CASM project directory structure:

The CASM project directory structure standardizes the location of various files used by multiple CASM methods. In the descriptions that follow some paths include codes of the form `<variable>` to indicate that there may be multiple directories with varying names. This is used to i) store data for different configurations by supercell name (`<supercellname>`) or configuration name (`<configname>`) or ii) organize parallel sets of data in parallel directories to make it easy to compare and use different parameters. In particular, a CASM project tracks:
- `<property>`: the property being expanded
- `<bset>`: the choice of basis set
- `<calctype>`: the choice of property calculation method and parameters
- `<ref>`: the choice of reference states (for formation energy calculations)
- `<eci>`: the choice of fitting coefficients (effective cluster interactions)

When a CASM project is initialized with `casm init`, default directories are created with names such as `bset.default`, `calctype.default`, etc. Either manually or by using `casm settings`, the user may create new directories with custom names such as `bset.occupation`, `bset.chebychev`, `calctype.gga`, `calctype.lda`, etc. to store different choices of basis set, calculation type, etc. These directory names must be prefixed with `"bset."`, `"calctype."`, etc. for CASM to recognize them.


The CASM project settings (which can be inspected and modified by `casm settings`) store a list of named cluster expansions as described by one each of `<property>`, `<bset>`, `<calctype>`, `<ref>`, and `<eci>`. A default cluster expansion can be configured which indicates which data from which directories to query by default.

Additional `<variable>` descriptions:
- `<root>`: indicates the CASM project root directory
- `<title>`: a CASM project is given a `title` in the `prim.json` file used to initialize it
- `<dof_key>`: name of degrees of freedom (DoF) listed in `prim.json`


---

#### CASM project root directory:

Location: `<root>/`

Contents:

| Name | Description | Format |
|-|-|-|
| `.casm/` | Stores hidden CASM project settings and data directory | |
| `basis_sets/<bset>` | Stores basis set input, results, and generated code | |
| `cluster_expansions/` | Stores cluster expansion fitting coefficients | |
| `reports/` | Stores reports generated by `casm import` and `casm update` | |
| `symmetry/` | Stores symmetry analysis results | [goto](#symmetry-dir) |
| `training_data/` | Stores calculation settings and results | |
| `LOG` | Logs all casm API commands run for the project | |

---

#### CASM project settings and data directory:

Location: `<root>/.casm/`

Contents:

| Name | Description | Format |
|-|-|-|
| `jsonDB/` | Directory containing enumerated supercells and configurations | |
| `query/` | Directory containing query plugins code | |
| `prim.json` | Reference copy of the primitive crystal structure used to initialize the CASM project | [BasicStructure] |
| `composition_axes.json` | Stores the selected and possible composition axes | |
| `project_settings.json` | Stores CASM project settings | |

---

#### JSON database directory:

Location: `<root>/.casm/jsonDB`

Contents:

| Name | Description | Format |
|-|-|-|
| `config_list.json` | File containing enumerated configurations | |
| `scel_list.json` | File containing enumerated supercells | |
| `<calctype>/config_props.json` | File containing calculated properties for a particular calculation type | |

---

#### Query plugins directory:

Location: `<root>/.casm/query`

Contents:

| Name | Description | Format |
|-|-|-|
| `Configuration/` | Directory for placing configuration query plugin source code | |
| `Supercell/` | Directory for placing supercell query plugin source code | |

---

#### Basis sets directory: <a name="basis-sets-dir"></a>

Location: `<root>/basis_sets/<bset>`

Contents:

| Name | Description | Format |
|-|-|-|
| `basis.json` | Basis function orbit information | |
| `bspecs.json` | Basis set specifications (input file) | [ClexBasisSpecs] |
| `clust.json` | Cluster orbit information | |
| `<title>_Clexulator.cc` | Generated source code for evaluating basis functions  | |
| `<title>_Clexulator.o` | Compiled object file for evaluating basis functions  | |
| `<title>_Clexulator.so` | Compiled shared object file for evaluating basis functions  | |

---

#### Cluster expansion ECI directories: <a name="cluster-expansions-dir"></a>

Location: `<root>/cluster_expansions/<property>/<bset>/<calctype>/<ref>/<eci>/`

Contents:

| Name | Description | Format |
|-|-|-|
| `eci.json` | Basis function orbit information, with effective cluster interaction (ECI) values included | |

---

#### CASM import and update reports directory <a name="reports-dir"></a>

Location: `<root>/reports/`

Contents:

| Name | Description | Format |
|-|-|-|
| ... todo ... | ... todo ... | ... todo ... |

---

#### Symmetry analysis directory <a name="symmetry-dir"></a>

Location: `<root>/symmetry/`

Contents:

| Name | Description | Format |
|-|-|-|
| `analysis/<configname>/` | Stores DoFSpace analysis results for enumerated configurations | [goto](#dofspace-dir) |
| `lattice_point_group.json` | The point group of the prim's lattice | |
| `factor_group.json` | The prim's factor group (a finite description of the crystal space group) | |
| `crystal_point_group.json` | The group formed by excluding translations from the prim's factor group operations | |

---

#### DoFSpace analysis directories <a name="dofspace-dir"></a>

Location: `<root>/symmetry/analysis/<configname>/`

Contents:

| Name | Description | Format |
|-|-|-|
| `lattice_point_group.json` | The point group of the configuration's superlattice | |
| `factor_group.json` | The configuration's factor group (a finite description of the crystal space group) | |
| `crystal_point_group.json` | The group formed by excluding translations from the configuration's factor group operations | |
| `dof_space_<dof_key>.json` | A description of the DoF vector space, by specified DoF type  | |

---

#### Configuration directories <a name="config-dir"></a>

Location: `<root>/training_data/<configname>/`

Description:

The configuration directories are used to store data associated with a particular configuration including its definition and calculated properties.

Contents:

| Name | Description | Format |
|-|-|-|
| `POS` | VASP POSCAR file corresponding to the configuration | |
| `config.json` | JSON file containing the values degrees of freedom (DoF) defining the configuration | |
| `structure.json` | The crystal structure corresponding the configuration | |

---

#### Calculation settings directories <a name="calc-settings-dir"></a>

Location:

One of:
- For configuration specific settings: `<root>/training_data/<configname>/settings/<calctype>/`
- For supercell specific settings: `<root>/training_data/<supercellname>/settings/<calctype>/`
- For default settings: `<root>/training_data/settings/<calctype>/`

Description:

Calculation wrappers for CASM (ex. `casm.vaspwrapper` for running VASP calculations, `casm.qewrapper` for running Quantum Espresso calculations) check for input settings files in the calculation settings directories, using the most local settings for a particular configuration. In most cases, the default settings files are stored in `<root>/training_data/settings/<calctype>/` and used for all configurations. Sometimes it is necessary to provide configuration specific settings to overcome an issue that arises when calculating properties. Rarely, the superell specific settings may be used to set default settings for all configurations in a particular supercell. The implementation is controlled by the wrapper code, but by convention, settings files should be searched on a file-by-file basis, so to set supercell or configuration specific settings it is sufficient to only include the particular files necessary in the supercell or configuration level settings folder.

Contents:

| Name | Description | Format |
|-|-|-|
| `calc.json` | JSON file containing calculation settings and parameters used to control job submission management software  | |
| ... others ... | Calculation type dependent files necessary for input file setup and job execution. See casm.wrapper documentation. | |

---

#### Calculation results directories <a name="calc-results-dir"></a>

Location: `<root>/training_data/<configname>/<calctype>/`

Contents:

| Name | Description | Format |
|-|-|-|
| `properties.calc.json` | CASM SimpleStructure JSON file containing the calculated lattice, coordinates, and any properties for configuration `<configname>`. This file is read by `casm update` to update the calculated properties |  |
| ... others ... | Calculation type dependent input and output files. See casm.wrapper documentation. |  |


[[Formats reference]](../formats.md)


[ClexBasisSpecs]: casm/clex/ClexBasisSpecs.md
[BasicStructure]: casm/crystallography/BasicStructure.md