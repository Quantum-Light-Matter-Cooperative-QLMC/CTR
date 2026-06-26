# Coherent Transisition Radiation from Various Electron Bunch Distributions

This repository contains Python code for simulating coherent transition radiation (CTR) emitted by relativistic electron bunches. The code computes single-electron transition radiation spectra, bunch form factors, coherent and incoherent radiation contributions, and integrated photon counts as a function of wavelength and emission angle.

The simulation is intended to study how different electron bunch distributions, pulse durations, and beam geometries affect CTR emission.

## Background
Transition radiation is produced when a charged particle crosses the boundary between two media of distinct dielectric constants. The charged particle must "rearrange" its field as a result of the different media. It is this rearrangement that produces transition radiation. FOr an electron bunch, the total emitted radiation depends not only on the single-electron radiation spectrum, but also on the spatial distribution of the bunch.

The total bunch spectrum is modeled as:

$W_N=W_1[N_e + N_e(N_e-1)|F|^2]$

where:

- $W_1$ is the single-electron transition radiation spectrum
- $N_e$ is the number of electrons in the bunch
- $|F|^2$ is th ebunch form factor
- $N_e W_1$ is the incoherent contribution
- $N_e(N_e-1)W_1|F|^2$ is the coherent contribution

## Main Features
This code can be used to do the following:

- Compute the single-electron CTR anfular spectrum
- Define Gaussian, orbital angular momentum (OAM), and Airy-like electron bunch distributions
- Calculate three-dimensional bunch form factors
- Compute coherent and incoherent radiation contributions
- Generate CTR specta over angle to estimate total photon count
- Compare coherence thresholds for different pulse durations (only applicable for Gaussian and OAM distributions)
- Study how bunch structure affects the emitted CTR spectrum

## Physical Quantities
The main physical parameters used in the simulation include:
| Symbol | Meaning |
| --- | --- |
| $N_e$ | Number of electrons in the bunch |
| $\beta$ | Electron velocity normalized to the speed of light |
| $\gamma$ | Relativistic Lorentz factor |
| $\lambda$ | Radiation wavelength |
| $\omega$ | Angular frequency |
| $\theta$ | Observation angle |
| $\sigma_t$ | Pulse duration |
| $\sigma_T$ | Transverse bunch size |
| $\sigma_L$ | Longitudinal bunch size |
| $z$ | Propagation distance |
| $a$ | Apodization level |
| $\ell$ | Topological order |
| $F$ | Bunch form factor |
| $W_1$ | Single-electron radiation spectrum |
| $W_N$ | Total bunch radiation spectrum | 

## Code Structure

The code is organized around the following modules:

### 1. Physical Constants and Parameters
The code begins by defining the usual constants:
```
c = 3e8
eps0 = 8.854e-12
e = 1.602e-19
```
Typical simulation inputs include:
```
bunchcharge = 5e-12
lam = np.linspace(1e-6, 1e-5, Nlam)
th = np.linspace(-0.2, 0.2, Nangle)
```

### 2. Single-Electron CTR Spectrum
The single-electron transition radiation spectrum is computed as a function of observation anfle and wavelength. The expression used is:

$W_1 = \frac{e^2 \beta^2 \sin{\theta}^2}{4\pi\epsilon_0 c(1-\beta^2\cos{\theta}^2)^2}$.

This term describes the angular distribution of radiation emitted by a single electron crossing the aforementioned boundary

### 3. Electron Bunch Distributions
The code currently supports three different bunch distributions.
#### Gaussian Bunch
A Gaussian bunch serves as a useful baseline model:

$\rho(\bar{r})\propto \exp{\left(-\frac{x^2+y^2}{2\sigma^2_T}\right)} \exp{\left(-\frac{z^2}{2\sigma^2_L}\right)}$.


#### OAM Bunch
An OAM bunch includes an azimuthhally structured transverse profile:

$\rho(\bar{r})\propto \frac{(x^2+y^2)^{\ell}}{\pi^{3/2}}\exp{\left(-\frac{x^2+y^2}{2\sigma^2_T}\right)}\exp{\left(-\frac{z^2}{2\sigma^2_L}\right)}$.

#### Airy Bunch
An Airy bunch serves as a unique case as Airy distributions do not belong to the family of eigenfunctions of the angular momentum operator and therefore do not share the common structure of Gaussian and OAM beams. Therefore, the Airy bunch is defined with respect to two dimensions rather than three: a propagation distance $z$ and a transverse simension $x$, where $s=\frac{x}{\sigma_T}$ and $\xi=\frac{z}{k\sigma_T}$:

$\rho(\xi,s)\propto\text{Ai}\left(s-\frac{\xi^2}{4}+ia s\right)\exp{\left(a\left[s-\frac{\xi^2}{2}\right]+i\left[\frac{s\xi}{2}-\frac{\xi^3}{12}+\frac{a^2\xi}{2}\right]\right)}$.

### 4. Form Factor Calculation
The bunch form factor is calculated from the FOurier transform of the charge density:

$f(\bar{k}) = \int \rho(\bar{r})e^{i\bar{k} \cdot \bar{r}} d^3\bar{r}$.

For a longitudinal-only model, the relevant wavevector component is expressed as:

$k_z=\frac{\omega}{c}(1-\beta\cos{\theta})$.

The magnitude ($|F|^2$) is then used to calculate the bunch's spectrum.

## Typical Workflow
A typical simulation workflow is:

1. Define physical constants and simulation parameters.
2. Define wavelength and angular grids.
3. Compute the elctron energy, $\gamma$, and $\beta$.
4. Compute the single-electron spectrum ($W_1$).
5. Define the electron bunch density.
6. Compute the bunch form factor (|F|^2).
7. Compute the total bunch spectrum (W_N).
8. Plot total bunch spectrum results.
10. Integrate spectrum.
11. Plot total photon count results.

## Example Use Cases
The code can be used to study:

- How pulse duration affects the onset of coherence
- How CTR spectrum changes with electron energy
- How OAM bunch profiles compare to Gaussian profiles
- How Airy propagation affects emitted spectrum
- How coherence depends on wavelength and observation angle

## Notes and Assumptions
Several assumptions are used in the current iteration of the code:

- The bunch is treated classically using a spatial charge-density form factor
- The radiation is calculated in the far-field approximation.
- The interface is assumed to be planar and normal to the beam axis.
- Many calculation integrate over the full azimuthal angle, reducing the problem to dependence on observation angle and wavelength
- The longitudinal bunch size strongly controls the coherence threshold

## Requirements
The code requires the following Python packages:
```
numpy
scipy
matplotlib
```
## Future Improvements
Future versions will include:

- Non approximated form factors
- Azimuthal angle dependence
- The ability to model tilted interfaces
- Adding experimental detector acceptances
