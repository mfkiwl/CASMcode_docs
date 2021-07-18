---
title: "Chemical Reference"
permalink: /formats/casm/clex/ChemicalReference/
---

### Description

The chemical reference determines the value of the formation energy and chemical potentials calculated by CASM.                          

Chemical references states are set by specifying a hyperplane in energy - composition space. Energies are specified normalized per atom, and compositions are specified in terms of atomic fractions.

The chemical reference may be defined by specifying the hyperplane explicitly, or by specifying several reference states via enough points in energy - composition space to span the composition space of the allowed occupants specified in the [prim].

For consistency with other CASM projects, additional reference states extending to other compositional dimensions may be included also. The pure Va reference is always 0.

Additionally, it is possible to customize the reference states used on a per supercell or per configuration basis.

#### Project files

This format is used for the [`chemical_reference.json`] file, which is generated by the `casm ref` method.


### JSON Attributes List

Chemical Reference attributes:

| Name | Description | Format |
|-|-|-|
| [`config`](#config) | Configuration-specific references | dict |
| [`global`](#global) | Global reference | array of [Chemical Reference State](#chemical-reference-states) |
| [`species_order`](#species-order) | Species order in composition vectors | array of string |
| [`supercell`](#supercell) | Supercell-specific references | dict |

---

Chemical Reference State attributes:

| Name | Description | Format |
|-|-|-|
| [`<occupant>`](#occupant) | Number of a particular occupant in the reference state | number |
| [`energy_per_species`](#energy-per-species) | Energy per species of the reference state | number |


### JSON Attributes Description

#### Chemical Reference JSON Object

- {: #config } `config`: dict (optional, default=`{}`)

  Allows the chemical reference to be customized per configuration. The keys are configuration names and the values are arrays of [Chemical Reference State], as specified for [`global`](#global).

- {: #global } `global`: array of [Chemical Reference State](#chemical-reference-states) (required)

  An array of [Chemical Reference State](#chemical-reference-states) that define the hyperplane in energy - composition space. The hyperplane must span the composition space of the [prim], and may extend to additional compositional dimensions. The pure vacancy (`"Va"`) reference is always 0 and does not need to be specified.

- {: #species-order } `species_order`: array of string

  Gives the order of species in composition vectors used by CASM internally. This is deterministically generated from the order of sites and occupants in the [prim], with any additional occupants included to extend the composition space added to the end.

- {: #supercell } `supercell`: dict (optional, default=`{}`)

  Allows the chemical reference to be customized per supercell. The keys are supercell names and the values are arrays of [Chemical Reference State], as specified for [`global`](#global).

#### Chemical Reference State JSON Object

- {: #occupant } `<occupant>`: number

  A name-value pair, where the key, `<occupant>`, is of one of the allowed [occupants]({{ "/formats/casm/crystallography/BasicStructure/#site-occupants" | relative_url }}) of the [prim], and the value is the number of that species in the reference state.

  The composition of the reference state, as an atomic fraction, is determined from all `<occupant>` attributes in the reference state.

- {: #energy-per-species } `energy_per_species`: number

  The energy per species of the reference state.

<div>
**Note:** The `<occupant>` values only affect the composition of the reference state, not the energy. Scaling the `<occupant>` values of a reference state by a constant value does not change the reference. The following references define the same chemical reference:

    // reference 1
    [
      {"A": 1.0, "energy_per_species": 1.},
      {"B": 1.0, "energy_per_species": 1.}
    ]

    // reference 2
    [
      {"A": 2.0, "energy_per_species": 1.},
      {"B": 2.0, "energy_per_species": 1.}
    ]

    // reference 3
    [
      {"A": 1.0, "energy_per_species": 1.},
      {"B": 2.0, "energy_per_species": 1.}
    ]

</div>
{: .notice--info }


### Examples

#### Example 1) Chemical reference for HCP Zr, with octahedral interstitial O disorder

    {
      "chemical_reference" : {
        "global" : [
          {
            "O" : 0.000000000000,
            "Zr" : 1.000000000000,
            "energy_per_species" : -8.546979385000
          },
          {
            "O" : 0.500000000000,
            "Zr" : 0.500000000000,
            "energy_per_species" : -9.090697705000
          }
        ],
        "species_order" : [ "Zr", "Va", "O" ]
      }
    }



{% include file_formats_and_locations.md %}