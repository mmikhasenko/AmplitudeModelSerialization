## Introduction

### Overview

The model description format delineates a structured, JSON-based specification designed to facilitate the reproducibility and validation of theoretical frameworks within the physics community. It provides a standardized approach to describing particle kinematics, lineshapes, and interaction chains, crucial for interpreting results from amplitude analyses (AmAn) and ensuring the accuracy and consistency of theoretical models. This document targets framework developers and experts engaged in high-energy physics, computational physics, and related fields.

### Objectives

- **Reproducibility and Open Science:** By standardizing model descriptions, this format aims to enhance the reproducibility of computational experiments and theoretical analyses. It supports open science initiatives by making it easier for researchers to share, validate, and build upon each other's work.
- **Inference from Amplitude Analysis Results:** The format is designed to facilitate the interpretation of AmAn results by the theory community. By providing a clear and comprehensive model description, it helps bridge the gap between experimental data and theoretical insights.

- **Correctness/Validity Check for New Frameworks:** As new computational frameworks and models are developed, this format serves as a benchmark for validating their correctness. It ensures that new tools and approaches adhere to established standards, fostering innovation while maintaining scientific rigor.

- **Integration with Monte Carlo (MC) Generators:** The format is compatible with MC generators, enabling seamless integration and simulation workflows. This compatibility is critical for testing theoretical models against experimental data and for conducting high-fidelity simulations.

- **Benchmark for New GPU/CPU Extensions:** The structured nature of the model description format makes it an ideal benchmark for new GPU and CPU extensions aimed at computational physics applications. By providing a common material for benchmarking, it aids in evaluating the performance enhancements offered by new hardware and software technologies.

This document presents the specifications of the model description format in detail, outlining its structure, components, and applications. It is intended as a comprehensive guide for developers and theorists working at the intersection of computational and theoretical physics, ensuring that the tools and models they develop are both accurate and interoperable.

## Amplitude model and observables

In modeling, the Probability Density Function (PDF) serves as a fundamental concept for predicting and analyzing the outcomes of particle interactions. The PDF is a real, normalizable function that depends on kinematic variables and parameters, providing a quantitative framework to describe the likelihood of observing a particular configuration or outcome in a particle decay or collision event.

### Observables

Observables are measurable quantities derived from the model, offering insight into the underlying physics governing particle interactions. In the context of amplitude models, observables are calculated from the transition amplitudes, which represent the probability amplitudes for the system to transition from an initial to a final state. Two primary observables are defined in this framework:

1. **Unpolarized Intensity:**
   The unpolarized intensity is an observable that represents the overall likelihood of a transition without considering the polarization states of the particles involved. It is computed as the squared magnitude of the transition amplitude, summed over all spin projections. Mathematically, the unpolarized intensity ($I_{unpolarized}$) is given by:
   
   $$
   I_{unpolarized}(\tau | \text{pars}) = \sum_{\text{helicities}} |A_{\text{helicities}}(\tau | \text{pars})|^2
   $$

   where $A_{\text{helicities}}(\tau | \text{pars})$ denotes the transition amplitude for a given set of helicities, $\tau$ represents the kinematic variables, and $\text{pars}$ symbolizes the model parameters. This observable is crucial for experiments where the polarization of the particles is not measured or considered.

2. **Polarized Intensity:**
   In contrast, the polarized intensity accounts for the polarization states of the particles involved in the interaction. It is computed by contracting the transition amplitude and its complex conjugate with the polarization matrix ($\rho$). This process involves summing over the final helicities while keeping the initial helicity states ($\lambda_0, \lambda_0'$) explicit in the calculation:
   
   $$
   I_{polarized}(\tau | \text{pars}) = \sum_{\text{final\_helicities}} A^*_{\lambda_0, \text{final\_helicities}}(\tau | \text{pars}) \times \rho_{\lambda_0,\lambda_0'} \times A_{\lambda_0', \text{final\_helicities}}(\tau | \text{pars})
   $$
   
   Here, $A^*_{\lambda_0', \text{final\_helicities}}$ represents the complex conjugate of the amplitude for initial helicity $\lambda_0'$ and a sum over final helicities. The polarization matrix $\rho_{\lambda_0,\lambda_0'}$ encapsulates the initial polarization states of the system, allowing for a detailed analysis of how polarization affects the transition probabilities.


```json
{
    "distributions" : []
    {
        "type": "unpolarized_intensity",
        "model": "my-amazing-model"
    },
    {
        "name": "my-amazing-model",
        "kinematics": {},
        "reference_topology": {},
        "chains": [
            {},  # chain 1
            {},  # chain 2
        ]
    }
}
```


## Model Structure Overview

The model description is designed to encapsulate all elements of transition models, including the characteristics of particles involved, the shapes of their interaction lines, and the overarching topology of particle interactions. The format's hierarchical nature allows for detailed specification of models while maintaining readability and ease of manipulation by software tools.

### Mandatory Top-Level Components

The model description is organized around several mandatory root-level components, each serving a distinct purpose in defining the physical model:

- **`kinematics`:** This section contains information about the particles involved in the model, including their spins, indices for identification, names, and masses. It establishes the foundational elements of the model by specifying the properties of each particle.

- **`reference_topology`:** This array defines the basic interaction structure or topology of the model which is used to define the reference quanzation axes. It outlines the decay chain for which the amplitude is written without a need for the alignment rotations. All other chains that have different decay topology must be aligned to the reference one.

- **`chains`:** The chains section lists specific interactions within the model, detailing the propagators, vertices with parametrization scheme and a complex coupling. Each chain is a cascade of decays that follows the chain topology. For every node one specifies the vertex propertied,
  and a parametrization (the lineshape) of an intermediate resonance that ends on the node.

## Kinematics Section

### Purpose of the `Kinematics` Object

The `kinematics` object within the model description format plays a crucial role in defining the physical characteristics of particles involved in a model. It serves as the foundation for constructing a decay model by specifying essential properties such as spin, and masses of all particles. All lists must have the same length, with the order that relates different particles.

### Detailed Field Descriptions

- **`names`:** Particle names in the `names` field provide a human-readable identifier for each particle. These names are not standartized and used only for verbose purpoves.

- **`indices`:** This field assigns a unique index to each particle, facilitating their identification throughout the model. Indices are used to reference particles in other sections of the model description, ensuring clarity and consistency in specifying interactions and decay processes. The overall system or the mother particle is referenced as zero. The final state particles are numbered by intices 1:N, where N is the total number of particles.

- **`masses`:** The `masses` field specifies the mass of each particle in units of GeV/c^2.

- **`spins`:** The `spins` field lists the spin quantum numbers of the particles. They are specified as strings in units of the reduced Planck constant (ħ), with common values including `1/2` for fermions (e.g., protons, electrons) and `1`, `0` for bosons (e.g., photons, pi mesons).

### Examples of the `kinematics` sections

- In a model describing the three-body decay of a Lambda baryon (Lb) into a J/psi meson, a kaon (K), and a pion (pi), the `kinematics` object might include:
  - `spins`: `["1/2", "1", "0", "0"]` for Lb, J/psi, K, and pi, respectively.
  - `indices`: `[0, 1, 2, 3]` to uniquely identify each particle.
  - `names`: `["Lb", "Jpsi", "K", "pi"]` for ease of reference.
  - `masses`: `[5.62, 3.097, 0.493, 0.140]` representing the masses of Lb, J/psi, K, and pi.

- For a four-body decay of a B meson into a psi meson, a kaon, and two pions, the `kinematics` section could detail:
  - `spins`: `["0", "1", "0", "0", "0"]` for B, psi, K, and the two pi mesons.
  - `indices`: `[0, 1, 2, 3, 4]` to differentiate each particle within the model.
  - `names`: `["B", "psi", "K", "pi", "pi"]`, noting that the pions are indistinguishable but separated by their indices.
  - `masses`: `[5.279, 3.686, 0.493, 0.140, 0.140]`, listing the masses of B, psi, K, and the two pi mesons.


## Topology and Reference Topology

The `reference_topology` array within the model description format serves a pivotal role in defining how helicity amplitude is computed. By specifying the reference topology, one sets quantization axes for particle helicities. Helicity, the projection of a particle's spin along its direction of motion, is a quantity whose precise definition is contingent upon the frame of reference in which it is evaluated. Accurate determination of helicity states is crucial for computing the correct amplitude values, however, the choice does not affect the value of the `unpolarized_intensity` upon a few exceptions.

In the context of the conventional helicity formalism, the `reference_topology` array implicitly prescribes a method for defining helicities. It comes from the specification of a default quantization frame for each stage of the decay process. The helicity values employed in the indices of Wigner rotations `D_{λ1, λ2}` and couplings `H_{λ1, λ2}` are thus indicative of this frame. When considering a particle's helicity in any other frame, it must be treated as a superposition of the states defined by the default quantization.

### An example of four-body decay

As as example, let's look into an application of the conventional helicity formalism to a four-body decay topology, specifically `[[[3,1],4],2]`. This topology outlines the decay sequence and the respective frames that define the helicities of the involved particles. Understanding the relation between the decay frames and the helicity definitions is crucial for accurately computing decay amplitudes within this formalism.

In the given topology, the decay amplitude calculation involves a series of Wigner D-functions, each corresponding to rotations and boosts that define the helicity states of the particles:

$$
A = n_{j_0} D_{\tau, \lambda_2}^{j_0}(\text{angles}_{[[3,1],4]}) 
\cdot n_{j_{[[3,1],4]}} D_{\nu, \lambda_4}^{j_{[[3,1],4]}}(\text{angles}_{[3,1]})
\cdot n_{j_{[3,1]}} D_{\lambda_3, \lambda_1}^{j_{[3,1]}}(\text{angles}_3)
$$

- $D_{\tau, \lambda_2}^{j_0}(\text{angles}_{[[3,1],4]})$: This function describes the transformation for particle 2's helicity (`λ2`) in the overall rest frame of the system (comprising particles 3, 1, 4, and 2). Here, particle 2's helicity is defined relative to the frame where all other particles are considered, emphasizing its position in the decay sequence.

- $D_{\nu, \lambda_4}^{j_{[[3,1],4]}}(\text{angles}_{[3,1]})$: For particle 4, its helicity (`λ4`) is defined within the rest frame of the (3,1,4) system. This frame is obtained from the overall rest frame by applying a rotation and boost, signifying the progression of the decay sequence and the specific frame where particle 4's helicity is defined.

- $D_{\lambda_3, \lambda_1}^{j_{[3,1]}}(\text{angles}_3)$: The helicities of particles 3 (`λ3`) and 1 (`λ1`) are defined within the (3,1) rest frame. This frame is reached through successive transformations: first to the (3,1,4) system and then to the (3,1) subsystem. This sequence of boosts and rotations precisely defines the helicity states of particles 3 and 1 in relation to their specific interaction frame.


## Chains Section

The `chains` section is a main component of the model description format, outlining the specific interaction sequences and their properties within the model. Chains are components of the model, which are added linearly to each other. The `chains` fields contains a list, `[{}, {}, ...]`, with every element being a chain. The chain contains the field `topology` that describe the cascade decay, the field `propagators` that is a list of the lineshape descriptors, and the field `vertices` that specifies parametrization of every node in the decay-topology graph.

### Topology

The `topology` field in each chain delineates the structural framework of the interactions, derived from and related to the `reference_topology`. It illustrates the hierarchical sequence of interactions and propagations, providing a visual and logical map of how particles transform and interact within the model. The topology ensures that each chain aligns with the overall model structure, maintaining consistency and coherence in the description of particle dynamics.

### Vertices

Vertices define the points where interactions occur within a chain, specifying the change in particle states. Each vertex is characterized by:

- **`node`:** Defines a node in the topology graph by specifying the particles involved in the interaction. 

- **`type`:** specify how the helicity coupling `H_{l1,l2}` is computed. Three types are defined `RecouplingLS`, `ParityRecoupling`, and `NoRecoupling`. These reflect different ways of relating various combinations of the helicity indices.


### Propagators

- **`type`:** The `type` field within each propagator specifies the mathematical or physical model used to describe the propagation of a particle between interactions. This type is directly linked to the `lineshapes` section, where the detailed characteristics of each propagator type (e.g., resonance models like Breit-Wigner or Flatte) are defined. The `type` essentially dictates how the propagator influences the chain's overall amplitude, based on its lineshape parameters.

- **`spin`:** The `spin` value of a propagator indicates the spin of the particle as it propagates. This is crucial for determining the angular momentum conservation and spin-related effects in the interaction, influencing the selection rules and possible transitions within the chain.

- **`node`:** Nodes represent the points of interaction within a chain, defining how particles are grouped and interact. The `node` structure specifies the arrangement of particles before and after an interaction, guiding the construction of the chain's topology and determining the sequence of propagations and interactions.

### Weight

The `weight` field in each chain represents the complex amplitude associated with the chain's specific sequence of interactions and propagations. This weight factors into the overall amplitude of the process being modeled, influencing the probability of the chain's occurrence. Weights are crucial for calculating cross sections, decay rates, and other observable quantities, directly impacting the model's predictive accuracy.

