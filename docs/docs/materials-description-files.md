# Materials Description Files

In PUMAS, material properties  are encoded in XML within a **M**aterials
**D**escription **F**ile (**MDF**). A compilation of MDFs is available from the
[pumas-materials](https://github.com/niess/pumas-materials) repository. Below is
a description of the XML tags recognised by PUMAS.
{: .justify}


<div markdown="1" class="shaded-box fancy">
## The `<pumas>` tag

The `<pumas>` tag is the root tag of the MDF. Any tag occurring outsides is
ignored by PUMAS. See e.g.
[domite.xml#L4](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L4).
{: .justify}
</div>


<div markdown="1" class="shaded-box fancy">
## The `<element>` tag

The `<element>` tag specifies a new atomic element. It has the following
attributes:
{: .justify}

| Attribute | Type   | Unit  | Description             |
|:---------:|:------:|:-----:|:-----------------------:|
| **name**  | String |       | Element name (symbol).  |
| **Z**     | Float  |       | Atomic number.          |
| **A**     | Float  | g/mol | Atomic mass.            |
| **I**     | Float  | eV    | Mean excitation energy. |

See e.g. [domite.xml#L6](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L6).
</div>


<div markdown="1" class="shaded-box fancy">
## The `<material>` tag

The `<material>` tag allows one to build a new material as a collection of
atomic `<element>`'s. It has the following attributes:
{: .justify}

| Attribute   | Type   | Unit             | Description             |
|:-----------:|:------:|:----------------:|:-----------------------:|
| **name**    | String |                  | Material name.          |
| **density** | Float  | g/cm<sup>3</sup> | Material density.       |
| **file**    | String |                  | Stopping power filename.|
| **I**       | Float  | eV               | Mean excitation energy. |

See e.g. [domite.xml#16](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L16).

!!! note
    Providing filenames for stopping power tables is optionnal. If missing then
    the filename is set according to the material name. If the filename point to
    an existing stopping power table then it **must** conform to the
    [PDG](https://pdg.lbl.gov/2020/AtomicNuclearProperties/index.html) format.
    {: .justify}

!!! warning
    The search path for stopping power tables depends on the 4<sup>th</sup>
    argument provided to the [`pumas_physics_create`][API_1] function, as
    described in the
    [tutorials](tutorials.md#initialisation-and-finalisation-of-the-physics).
    {: .justify}

!!! note
    Specifying the material Mean excitation energy, $I$, is optionnal. If not
    provided then $I$ is estimated from atomic components assuming Bragg
    additivity.
    {: .justify}

</div>


<div markdown="1" class="shaded-box fancy">
## The `<material><component>` tag

Constituent atomic elements are defined as children of a `<material>` tag using
`<component>` tags, one tag per atomic element. A material component tag has
the following attributes:
{: .justify}

| Attribute    | Type   | Description    |
|:------------:|:------:|:--------------:|
| **name**     | String | Element name.  |
| **fraction** | Float  | Mass fraction. |

See e.g. [domite.xml#17](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L17).

[API_1]: api/index.html##HEAD/group/physics/pumas_physics_create
</div>


<div markdown="1" class="shaded-box fancy">
## The `<composite>` tag

In PUMAS, a `<composite>` is a weighted average of several `<material>`'s, It
can be used in order to represent a rock containing various minerals at the
microscopic scale. A `<composite>` has the following attribute:
{: .justify}

| Attribute |  Type  |
|:---------:|:------:|
| **name**  | String |

See e.g. [domite.xml#58](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L58)
</div>


<div markdown="1" class="shaded-box fancy">
## The `<composite><component>` tag

Constituent materials are defined as children of a `<composite>` tag using
`<component>` tags, one tag per material. A composite component tag has
the following attributes:
{: .justify}

| Attribute    | Type   | Description    |
|:------------:|:------:|:--------------:|
| **name**     | String | Material name. |
| **fraction** | Float  | Mass fraction. |

See e.g. [domite.xml#L59](https://github.com/niess/pumas-materials/blob/master/mdf/examples/domite.xml#L59).

!!! note
    The component mass fraction can be changed at runtime using the
    [`pumas_composite_update`][API_2] function. For example, setting a mass
    fraction to zero or less disables the corresponding material from the
    composite. However, on the contrary it is **not** possible to add a
    component material to a composite at runtime if not already defined in the
    MDF.
    {: .justify}

[API_2]: api/index.html##HEAD/group/physics/pumas_physics_composite_update
</div>
