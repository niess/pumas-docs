# Tutorials

The following is a collection of tutorials illustrating the main functionalities
of PUMAS. A more systematic description of the API can be found
[here](api/index.html##HEAD). You might also
directly browse the
[examples](https://github.com/niess/pumas/tree/master/examples).
{: .justify}

!!! warning
    The tutorials and examples require specific [Materials Description
    Files](materials-description-files.md) and
    [energy loss tables](energy-loss-tables.md). Those can be downloaded with
    git, e.g. as:
    {: .justify}

        git clone https://gitub.com/niess/pumas-materials materials


<div markdown="1" class="shaded-box fancy">
## Initialisation and finalisation of the physics

Prior to any usage physics tables must be initialised. This can be
done in two ways:
{: .justify}

1.  From a **M**aterials **D**escription **F**ile
    ([MDF](materials-description-files.md)), using the
    [`pumas_physics_create`][API_1] function, e.g. as:

        struct pumas_physics * physics;
        pumas_physics_create(&physics, PUMAS_PARTICLE_MUON,
            "materials/mdf/examples/standard.xml", "materials/dedx/muon");

    The 3<sup>nd</sup> argument to [`pumas_physics_create`][API_1] specifies the
    path to the MDF while the 4<sup>th</sup> one specifies the folder where
    energy loss tables are located. For the later, relative paths w.r.t. the MDF
    location can be given by prepending the path with an `@`. Ordinary paths are
    assumed otherwise.
    {: .justify}

2.  From a binary dump, using the [`pumas_physics_load`][API_2] function, e.g.
    as:
    {: .justify}

        struct pumas_physics * physics;
        pumas_physics_load(&physics, "materials/dump");

When the physics is initialised from a MDF, PUMAS will pre-compute and tabulate
properties for **all** the materials defined in the file. Should there be latter
used or not. This step can last several seconds, depending on the number of
materials defined and on your machine performances. Once done, a binary dump of
the low level PUMAS data can be generated with the [`pumas_physics_dump`][API_3]
function. Initialising the physics from such a binary dump is much faster. Note
however that the dump format is OS and machine dependent. Therefore, it is safer
to use MDFs as an exchange format for the material properties.
{: .justify}

!!! note
    The file
    [examples/load.c](https://github.com/niess/pumas/blob/master/examples/load.c)
    provides an example of physics smart initialisation. The initialisation is
    done with [`pumas_physics_load`][API_2] if a binary dump is found. Otherwise
    the initialisation is done from a MDF, using
    [`pumas_physics_create`][API_1].  In the later case, a binary dump is
    generated, with [`pumas_physics_dump`][API_3] in order to speed up further
    initialisations.
    {: .justify}

When the physics ressources are no more needed they can be released using the
[`pumas_physics_destroy`][API_4] function. From there on it is possible to
re-initialise (load) the physics again.
{: .justify}

To sum up, the following code is an elementary program using the PUMAS library.
It computes the low level material tables used by PUMAS and dumps them to a
binary file.
{: .justify}

    #include "pumas.h"

    int main()
    {
        /* Initialise PUMAS physics from a MDF */
        struct pumas_physics * physics;
        pumas_physics_create(&physics, PUMAS_PARTICLE_MUON,
            "materials/mdf/standard.xml", "materials/dedx/muon");

        /* Dump the physics to a binary file */
        FILE * fid = fopen("materials/dump", "wb+");
        pumas_physics_dump(physics, fid);
        fclose(fid);

        /* Release PUMAS memory */
        pumas_physics_destroy(&physics);

        return 0;
    }

[API_1]: api/index.html##HEAD/group/physics/pumas_physics_create
[API_2]: api/index.html##HEAD/group/physics/pumas_physics_load
[API_3]: api/index.html##HEAD/group/physics/pumas_physics_dump
[API_4]: api/index.html##HEAD/group/physics/pumas_physics_destroy
</div>


<div markdown="1" class="shaded-box fancy">
## Error handling

Most PUMAS library functions return a
[`pumas_return`](api/index.html##HEAD/type/pumas_return)
code whenever an error could occur. The meaning of the return codes is detailed
in the online description of each library function.
{: .justify}

The PUMAS library also provides automatic error management by supplying an error
handler callback. This is done with the [`pumas_error_handler_set`][API_7]
function. A brief human readable description of the error is forwarded to the
handler as well. The following illustrates the corresponding mechanism with a
basic example.
{: .justify}

    #include "pumas.h"
    #include <stdlib.h>

    /* Error handler for PUMAS with a hard exit */
    static void error_handler(
        enum pumas_return rc, pumas_function_t * caller, const char * message)
    {
            /* Dump the error summary */
            fputs("pumas: library error. See details below\n", stderr);
            fprintf(stderr, "error: %s\n", message);

            /* Exit to the OS */
            exit(EXIT_FAILURE);
    }

    int main()
    {
        /* Set the error handler callback */
        pumas_error_handler_set(&error_handler);

        /* Initialise the physics from a binary dump */
        struct pumas_physics * physics;
        const char * dump_file = "materials/dump";
        FILE * fid = fopen(dump_file, "rb");
        if (fid == NULL) {
            perror(dump_file);
            exit_gracefully(EXIT_FAILURE);
        }
        pumas_physics_load(&physics, fid);
        fclose(fid);

        /* Release the physics memory */
        pumas_physics_destroy(&physics);

        exit(EXIT_SUCCESS);
    }

!!! note
    In some cases it can be useful to intercept library errors before the error
    handler is called. This can be done using the [`pumas_error_catch`][API_8]
    function. Afterwards, in order to manually raise the last caught error one
    can call the [`pumas_error_raise`][API_9] function. This mechanism is
    illustrated in the [smart loader
    example](https://github.com/niess/pumas/blob/master/examples/load.c#L48).
    {: .justify}

[API_7]: api/index.html##HEAD/group/error/pumas_error_handler_set
[API_8]: api/index.html##HEAD/group/error/pumas_error_catch
[API_9]: api/index.html##HEAD/group/error/pumas_error_raise
</div>


<div markdown="1" class="shaded-box fancy">
## Simulation context

Monte-Carlo simulations with PUMAS are performed within isolated contexts. This
allows the transport engine to handle multiple simulation streams
simultaneously. A simulation stream is created using the
[`pumas_context_create`][API_10] function. The allocated memory is released
using the [`pumas_context_destroy`][API_11] function. The
[`pumas_context_create`][API_10] function returns a handle to a semi-opaque
[`pumas_context`][PUMAS_CONTEXT] structure. **Note** that it must **not** be
re-allocated since it refers to internal (hidden) data required by the transport
engine. If extra (user) data must be attached to the simulation context, this
can be done at the context creation by reserving additional memory for them.
{: .justify}

A simulation stream can be configured directly by modifying the
[`pumas_context`][PUMAS_CONTEXT] data. In particular the following flags
allow to customise the transport:
{: .justify}

| name | type | description |
|-|-|-|
| **mode.energy\_loss** | [enum pumas\_mode][PUMAS_MODE]    | The scheme used for the computation of energy losses. Default is `PUMAS_MODE_DETAILED`. {: .justify} |
| **mode.decay**        | [enum pumas\_mode][PUMAS_MODE]    | The mode for handling decays. Default is `PUMAS_MODE_WEIGHT` for a $\mu$ or `PUMAS_MODE_DECAY` for a $\tau$. Set this to `PUMAS_MODE_STABLE` in order to disable decays at all. {: .justify} |
| **mode.direction**    | [enum pumas\_mode][PUMAS_MODE]    | Direction of the Monte Carlo flow. Default is `PUMAS_MODE_FORWARD`. Set this to `PUMAS_MODE_BACKWARD` for a reverse Monte Carlo. {: .justify} |
| **mode.scattering**   | [enum pumas\_mode][PUMAS_MODE]    | Algorithm for the simulation of the scattering. Default is `PUMAS_MODE_FULL_SPACE`. In order to neglect any transverse scattering set this to `PUMAS_MODE_LONGITUDINAL` instead. {: .justify} |
| **event**             | [`enum pumas_event`][PUMAS_EVENT] | The end conditions for the transport. Default is `PUMAS_EVENT_NONE`. {: .justify} |
| **limit.kinetic**     | `double`                          | The minimum kinetic energy for forward transport, or the maximum one for backward transport, in GeV. {: .justify} |
| **limit.distance**    | `double`                          | The maximum travelled distance, in m. {: .justify} |
| **limit.grammage**    | `double`                          | The maximum travelled grammage, in kg/m^2. {: .justify} |
| **limit.time**        | `double`                          | The maximum travelled proper time, in m/c. {: .justify} |

[PUMAS_MODE]: https://niess.github.io/pumas-docs/#HEAD/type/pumas_mode

Each simulation context also requires the user to supply a pseudo random stream.
It is the user's responsibility to ensure that the random stream is thread safe,
if multiple simulation contexts are used simultaneously. The random stream is
supplied by providing a callback generating a pseudo random number in `[0,1]`,
uniformly. Below is an example of context creation with a basic (not thread
safe) error stream. The context is configured for an hybrid energy loss scheme
without transverse transport, i.e. à la
[MUM](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.64.074015). For
the sake of clarity errors are not handled.
{: .justify}


    #include "pumas.h"
    #include <stdlib.h>

    /* A basic Pseudo Random Number Generator (PRNG) providing a uniform
     * distribution over [0, 1]
     */
    static double uniform01(struct pumas_context * context)
    {
            return rand() / (double)RAND_MAX;
    }

    int main()
    {
        /* Initialise the physics from a MDF */
        struct pumas_physics * physics;
        pumas_physics_create(&physics, PUMAS_PARTICLE_MUON,
            "materials/mdf/examples/standard.xml", "materials/dedx/muon", NULL);

        /* Create a new simulation context */
        struct pumas_context * context = NULL;
        pumas_context_create(&context, physics, 0);

        /* Configure the context */
        context->mode.energy_loss = PUMAS_MODE_HYBRID;
        context->mode.scattering = PUMAS_MODE_LONGITUDINAL;

        /* Provide a PRNG for the Monte-Carlo simulation */
        context->random = &uniform01;

        /* Release the allocated memory */
        pumas_context_destroy(&context);
        pumas_physics_destroy(&physics);

        exit(EXIT_SUCCESS);
    }

[API_10]: api/index.html##HEAD/group/context/pumas_context_create
[API_11]: api/index.html##HEAD/group/context/pumas_context_destroy
[PUMAS_CONTEXT]: api/index.html##HEAD/type/pumas_context
[PUMAS_EVENT]: api/index.html##HEAD/type/pumas_event
</div>


<div markdown="1" class="shaded-box fancy">
## Monte-Carlo state and transport

An elementary Monte-Carlo (particle) state in PUMAS is summarised by a
[`pumas_state`](api/index.html##HEAD/type/pumas_state)
structure. It contains the minimal set of data required by PUMAS for performing
a Monte-Carlo transport. **Note** that extra user data can be added by embedding
this structure in a larger one.
{: .justify}

Prior to any usage the particle state must be initialised. One has to provide an
electric charge, a kinetic energy, a Monte-Carlo weight and a starting position
and direction. For example, below is an example of static initialisation:
{: .justify}

    struct pumas_state state = {
        .charge = -1.,
        .kinetic = 1E+01,
        .weight = 1.,
        .position = { 0., 0., 10. }
        .direction = { 0., 0., -1. } };

In addition, during the simulation, the Monte-Carlo state carries information on
the traveled distance and grammage, and on the spent proper time.
{: .justify}

A Monte-Carlo state is transported (propagated) using the
[`pumas_contex_transport`][API_12] function. **Note** that doing so requires a
**simulation stream**, i.e. providing a properly configured
[`pumas_context`][PUMAS_CONTEXT]. Once everything is configured, transporting
the Monte-Carlo particle is as simple as:
{: .justify}

    enum pumas_event event;
    struct pumas_medium * medium[2];
    pumas_context_transport(context, &state, &event, medium);

The two last arguments are optionnal and can be set to `NULL`.
The [`pumas_context_transport`][API_12] call returns if:
{: .justify}

*  a user specified event occurs, e.g. a user supplied limit has been reached,
   {: .justify}

*  the particle escapes the simulation area, i.e. a `NULL` `pumas_medium` is reached (see [geometry](#specifying-a-geometry) below),
   {: .justify}

*  the particle decays (if enabled),
   {: .justify}

*  an error occurs.
   {: .justify}

!!! note
    By default the decay process is disabled for &mu;. Instead the particle's
    Monte-Carlo weight is decreased during the transport in order to account for
    it's survival probability.
    {: .justify}

Limits can be set on the particle traveled distance or grammage, on its
spent proper time or on its kinetic energy. This is done per simulation stream
by setting the `event` flag of the [`pumas_context`][PUMAS_CONTEXT] to one (or a
combination) of `PUMAS_EVENT_LIMIT_DISTANCE`, `PUMAS_EVENT_LIMIT_GRAMMAGE`,
`PUMAS_EVENT_LIMIT_KINETIC` or `PUMAS_EVENT_LIMIT_TIME`.  In addition a strictly
positive value must be provided for the corresponding `limit` field of the
simulation context. For example the code below sets a limit of 1 km on
the particle traveled distance:
{: .justify}

    /* Enable limits on the traveled distance */
    context->event |= PUMAS_EVENT_LIMIT_DISTANCE;

    /* Set a limit of 1 km on the traveled distance */
    context->limit.distance = 1E+03;

!!! note
    The context settings can be fully modified on the fly **between** two
    successive calls to [`pumas_context_transport`][API_12]. However, the
    context should **not** be modified during the execution of the
    [`pumas_context_transport`][API_12] function.
    {: .justify}

Below is an example of forward transport where the simulation mode is adapted
depending on the particle kinetic energy.
{: .justify}

    #include <float.h>
    #include "pumas.h"

    int main()
    {
        /* Initialise the physics, create a simulation stream and a
         * Monte-Carlo state
         */
        ...

        /* Enable kinetic energy limits */
        context->event |= PUMAS_EVENT_LIMIT_KINETIC;

        /* Transport the Monte-Carlo state */
        const double kinetic_min = 1E-03;
        while (state.kinetic > kinetic_min) {
            if (state.kinetic < 1E+02 + FLT_EPSILON) {
                /* Below 100 GeV do a detailed simulation
                 * à la Geant4, including transverse transport
                 */
                context->mode.energy_loss = PUMAS_MODE_DETAILED;
                context->mode.scattering = PUMAS_MODE_FULL_SPACE;
                context->limit.kinetic = kinetic_min;
            } else {
                /* Do a fast simulation à la MUM */
                context->mode.energy_loss = PUMAS_MODE_HYBRID;
                context->mode.scattering = PUMAS_MODE_LONGITUDINAL;
                context->limit.kinetic = 1E+02;
            }
            pumas_context_transport(context, &state, NULL, NULL);
        }

        /* Finalise PUMAS */
        ...
    }

[API_12]: api/index.html##HEAD/group/context/pumas_context_transport
</div>


<div markdown="1" class="shaded-box fancy">
## Specifying a Geometry

PUMAS is a pure transport engine. It does not provide geometric primitives, e.g.
boxes, orbs, tubes, etc ... like [Geant4](http://geant4.cern.ch/). Instead it
relies on a simple callback mechanism for describing the propagation media.  The
user must supply a [`pumas_medium_cb`][MEDIUM_CB] telling the transport engine
in which [`pumas_medium`][PUMAS_MEDIUM] it is currently located.  Returning a
`NULL` medium indicates that the transported particle has exit the simulation
geometry. In addition, the [`pumas_medium_cb`][MEDIUM_CB] callback provides also
a maximum geometric stepping distance, typically the distance to the next
boundary assuming a straight line propagation. Note that it is not critical to
provide the exact value. The transport engine will run a bisection search if a
change of medium is detected. However, providing the exact distance can speed up
the simulation, if it is _fast enough_ to compute this distance. Note that the
current *medium* or the *step* distance might not be needed by the transport
engine in some cases. This is indicated to the user by handling a `NULL` pointer
for the corresponding property. Therefore one must be cautious to check both
pointers for `NULL` before assigning to them, as illustrated in the example
below.
{: .justify}

A [`pumas_medium`][PUMAS_MEDIUM] is described by a material index and a set of
local properties. The material must be the same over the whole medium. Its index
can be mapped to the MDF material name using the
[`pumas_physics_material_index`][API_13] function. CPU wise, this is best done
during the initialisation and configuration stage, since the mapping implies
strings comparisons. The medium local properties are its bulk density and any
magnetic field. Those are set by a [`pumas_locals_cb`][LOCALS_CB] callback and
can vary over the medium. The [`pumas_locals_cb`][LOCALS_CB] callback can be
`NULL` in which case the material default density is used as defined in the MDF
and the magnetic field is set to zero. If a `pumas_locals_cb` is provided then
it must return a maximum stepping distance over which one can approximate the
medium as uniform. Returning a value of zero or less indicates a uniform medium
e.g. with a non standard density or with a magnetic field.  *Note* that it is an
error to return zero or less for any position of the medium if at least one area
is not uniform. Instead one should use two different media even though they have
the same material. Note also that if a null or negative density is set in the
`pumas_locals_cb` then the material's default density is used instead.
{: .justify}

!!! warning
    PUMAS does **not** correct the ionisation loss for the density effect if a
    different medium density is set than the one used to generate the energy
    loss table for the material. Neither is the energy loss corrected when a non
    uniform density is used. Therefore, it is the user responsibility to use
    this feature consistently, e.g. in order to reflect porosity variations in a
    rock filled with air whose energy loss is negligible w.r.t. the rock one.
    {: .justify}

Below is an example of a uniform medium of infinite extension in PUMAS. Although
this simple geometry might seem rather complicated to implement with PUMAS, this
callback mechanism is however flexible enough in order to deal with more
complicated geometries, e.g. a
[non uniform atmosphere](https://github.com/niess/pumas/blob/master/examples/geometry.c#L92)
as in the examples.
{: .justify}

    /* A uniform medium without magnetic field. Note that we could also set the
     * locals callback to `NULL` instead if using the material's default density.
     */
    double locals(struct pumas_medium * medium,
        struct pumas_state * state, struct pumas_locals * locals)
    {
            /* Set the medium density in kg/m^3. Set this to zero or less in
             * order to use the material's default density.
             */
            locals->density = 2.65E+03;

            /* Propose a maximum stepping distance. Returning zero or less indicates
             * a uniform medium
             */
            return 0.;
    }

    /* A simple medium of infinite extension */
    enum pumas_step medium(struct pumas_context * context,
        struct pumas_state * state, struct pumas_medium ** where, double * step)
    {
        static struct pumas_medium medium_ = { .material = 0, .locals = &locals };

        if (where != NULL) /* Beware that `where` is `NULL` if not requested */
            *where = &medium_; 

        /* Propose a maximum stepping distance. Providing zero or less indicates
         * no boundaries.
         */
        if (step != NULL) /* Beware that `step` is `NULL` if not requested */
            *step = 0;

        /* Notify PUMAS that the latter is an exact stepping distance not an
         * estimate
         */
        return PUMAS_STEP_EXACT;
    }

[MEDIUM_CB]: api/index.html##HEAD/group/callback/pumas_medium_cb
[LOCALS_CB]: api/index.html##HEAD/group/callback/pumas_locals_cb
[PUMAS_MEDIUM]: api/index.html##HEAD/type/pumas_medium
[API_13]: api/index.html##HEAD/group/physics/pumas_physics_material_index
</div>


<div markdown="1" class="shaded-box fancy">
## Practical Backward Monte-Carlo and weights

In Backward Monte-Carlo (BMC) mode PUMAS allows to compute a point estimate
of the $\mu$ ($\tau$) flux for a given final state, see e.g. _lemma 1_ of the
[reference paper][PUMAS_REF]. In order to sample a distribution of final
states instead, one has to rely on _corollary 4_, i.e. generate the final
states over a bias distribution as for classical [Importance Sampling][IS].
In practice, the procedure is as following:
{: .justify}

1.  At generation the particle weight is initialised as $\omega = \frac{1}{
    \text{PDF}_\text{gen}}$. Let say that one generates final states over a surface of
    interest and with some solid angle of aperture but at a fixed energy. Then
    $\text{PDF}_{gen}$ could be in units $\text{m}^{-2}\text{sr}^{-1}$.
    Following the weight would start with unit $\text{m}^2\text{sr}$.
    {: .justify}

2.  The backward propagation modifies the weight by a unit less Jacobian factor,
    $\text{J}_{i,f}$, due to the change in the integration variable for the
    flux, from initial state to final state. The particle’s weight is updated by
    PUMAS step by step.
    {: .justify}

3.  When reaching the primary flux surface, the weight must be multiplied by the
    corresponding flux, $\Phi_0$. The units used for this flux must be
    consistent with the one used for $\text{PDF}_\text{gen}$. So in the present
    case it could be $\text{GeV}^{-1}\text{m}^{-2}\text{s}^{-1}\text{sr}^{-1}$.
    Following, the final weight would have unit $\text{GeV}^{-1}\text{s}^{-1}$.
    But, if the final state energy is randomised as well, the weight's unit
    would be $\text{s}^{-1}$, i.e. a rate of events.
    {: .justify}

To sum up the particle weight is:

$$
\omega = \text{J}_{i,f} \frac{\Phi_0}{\text{PDF}_{gen}}.
$$

This result is similar to a classical Importance Sampling procedure except for
the extra Jacobian factor, $\text{J}_{i,f}$, due to the backward transport.
{: .justify}

For illustration, below is a simplified example of backward Monte-Carlo
integration over the kinetic energy. A 1 / E bias distribution is used, i.e.
a log uniform sampling.
{: .justify}

    #include <float.h>
    #include <math.h>
    #include "pumas.h"

    int main()
    {
        /* Initialise PUMAS, create a simulation stream and a
         * Monte-Carlo state
         */
        ...

        /* Randomise the final state kinetic energy */
        const double kinetic_min = 1E-03;
        const double kinetic_max = 1E+06;
        const double r = log(kinetic_max / kinetic_min);
        state.kinetic = kinetic_min * exp(r * context->random(context));
        state.weight = state.kinetic * r; /* 1 / PDF_{gen}(k_f) */

        /* Backward transport the Monte-Carlo state. Note that at exit the
         * Monte-Carlo weight is updated by the Jacobian BMC factor, J_{i,f}.
         */
        pumas_context_transport(context, &state, NULL, NULL);

        /* Sample the primary flux */
        state.weight *= flux(state.kinetic); /* Phi(k_i) */

        /* Finalise PUMAS */
        ...
    }

[PUMAS_REF]: https://doi.org/10.1016/j.cpc.2018.04.001
[IS]: https://en.wikipedia.org/wiki/Importance_sampling
</div>
