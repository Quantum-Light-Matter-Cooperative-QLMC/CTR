# Coherent Transition Radiation from Various Electron Bunch Distributions

This repository contains Python code for simulating coherent transition radiation (CTR) emitted by relativistic electron bunches. The code computes single-electron transition radiation spectra, bunch form factors, coherent and incoherent radiation contributions, and integrated photon counts as a function of wavelength and emission angle.

The simulation is intended to study how different electron bunch distributions, pulse durations, and beam geometries affect CTR emission.

## Background
Transition radiation is produced when a charged particle crosses the boundary between two media of distinct dielectric constants. The charged particle must "rearrange" its field because of the different media. This rearrangement produces transition radiation. For an electron bunch, the total emitted radiation depends not only on the single-electron radiation spectrum, but also on the spatial distribution of the bunch.

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

- Compute the single-electron CTR angular spectrum from tilted and finite interfaces
- Define Gaussian, hollow Gaussian, and Airy-like electron bunch distributions
- Calculate three-dimensional bunch form factors
- Compute coherent and incoherent radiation contributions
- Generate CTR spectra at specific angular slices
- Compare coherence thresholds for different pulse durations
- Study how bunch structure affects the emitted CTR spectrum

## Physical Quantities
The main physical parameters used in the simulation include:
| Symbol | Meaning |
| --- | --- |
| $\tau$ | Temporal bunch length |
| $\sigma_T$ | Transverse beam size |
| $\sigma_L$ | Longitudinal beam size |
| $\lambda$ | Emission wavelength |
| $a$ | Angular frequency |
| $\theta$ | Interface radius |
| $\psi$ | Interface tilt |
| $\phi$ | Azimuthal observation angle |
| $Q$ | Bunch charge |
| $E$ | Pkinetic beam energy |
| $\gamma$ | Lorentz factor |
| $\theta$ | Normal observation angle |
| $z_{prop}$ | Airy propagation length |
| $\alpha$ | Airy apodization |
| $W_1$ | Single-electron radiation spectrum |
| $W_N$ | Total bunch radiation spectrum | 

## Code Structure

The code is organized around the following modules:

### 1. Physical Constants and Parameters
The code begins by defining the usual constants:
```
# Physical constants
c = 299792458.0
e = 1.60217663e-19
h = 6.62607015e-34
eps0 = 8.854187812e-12
m_e_MeV = 0.51099895


# Electron-beam parameters
E_MeV = 100.0                    #kinetic energy [MeV]
bunchcharge = 5e-12              #bunch charge [C]
sigma_x = 100e-6                 #transverse rms scale [m]
sigma_y = 100e-6                 #transverse rms scale [m]
sigma_t = 1e-15                  #temporal rms scale [s]
sigma_z = c * sigma_t            #longitudinal rms scale [m]

#Hollow-Gaussian parameter
p_hollow = 3

#Finite-energy Airy parameters
airy_apodization = 0.05
z_prop = 10e6                    #Airy propagation coordinate [m]

#Interface parameters
psi = np.deg2rad(45.0)           #interface tilt
interface_radius = 1e-3          #finite radiator radius [m]

#Select which bunch to use for the CTR calculation: "gaussian", "hollow", or "airy"
beam_case = "airy"
```
Typical simulation inputs include:
```
# Observation grid
Nangle = 100
Nlam = 100

th = np.linspace(0.68, 0.88, Nangle)
phi = np.linspace(0.0, 2.0 * np.pi, Nangle, endpoint=False)
lam = np.linspace(1e-6, 1e-5, Nlam)

LAM, TH, PHI = np.meshgrid(lam, th, phi, indexing="ij")
OMEGA = 2.0*np.pi*c / LAM
```

### 2. Single-Electron CTR Spectrum
The single-electron transition radiation spectrum is computed as a function of observation anfle and wavelength. The expression used is:

$W_1 = \frac{e^2 \beta^2 \sin{\theta}^2}{4\pi\epsilon_0 c(1-\beta^2\cos{\theta}^2)^2}$.

This term describes the angular distribution of radiation emitted by a single electron crossing the aforementioned boundary

### 3. Electron Bunch Distributions
The code currently supports three different bunch distributions.
#### Gaussian Bunch
A Gaussian bunch serves as a useful baseline model:

$\rho(x,y,z)\propto \exp{\left(-\frac{x^2}{2\sigma^2_x}\right)}\exp{\left(-\frac{y^2}{2\sigma^2_y}\right)}\exp{\left(-\frac{z^2}{2\sigma^2_z}\right)}$

#### Hollow Gaussian Bunch
A hollow Gaussian bunch includes an azimuthally structured transverse profile:

$\rho(x,y,z)\propto \frac{(x^2+y^2)^{p}}{\pi^{3/2}}\exp{\left(-\frac{x^2}{2\sigma^2_x}\right)}\exp{\left(-\frac{y^2}{2\sigma^2_y}\right)}\exp{\left(-\frac{z^2}{2\sigma^2_z}\right)}.$

#### Airy Bunch
An Airy bunch serves as a unique case, as Airy distributions do not belong to the family of eigenfunctions of the angular momentum operator and therefore do not share the common structure of Gaussian and OAM beams. Therefore, the Airy bunch is defined with respect to two dimensions rather than three: a propagation distance $z$ and a transverse dimension $x$, where $s=\frac{x}{\sigma_T}$ and $\xi=\frac{z}{k\sigma_T}$:

$\rho(x,y,z)\propto\mathrm{Ai}\left(\frac{x}{\sigma_x} - \frac{z}{4k^2\sigma_x^4} + i\frac{\alpha z}{k\sigma_x^2}\right)\mathrm{Ai}\left(\frac{y}{\sigma_y} - \frac{z}{4k^2\sigma_y^4} + i\frac{\alpha z}{k\sigma_y^2}\right)\times\exp\left(\frac{\alpha x}{\sigma_x} - \frac{\alpha^2z^2}{2k^2\sigma_x^4} - i\frac{z^3}{12k^3\sigma_x^6} + i\frac{\alpha^2z}{2k\sigma_x^2} + i\frac{xz}{2k\sigma_x^2}\right)\times\exp\left(\frac{\alpha y}{\sigma_y} - \frac{\alpha^2z^2}{2k^2\sigma_y^4} - i\frac{z^3}{12k^3\sigma_y^6} + i\frac{\alpha^2z}{2k\sigma_y^2} + i\frac{yz}{2k\sigma_y^2}\right).$

### 4. Form Factor Calculation
The bunch form factor is calculated from the Fourier transform of the charge density:

$f(\bar{k}) = \int \rho(\bar{r})e^{i\bar{k} \cdot \bar{r}} d^3\bar{r}$.

The magnitude ($|F|^2$) is then used to calculate the bunch's spectrum.

## Typical Workflow
A typical simulation workflow is:

1. Imports and physical constants
2. Beam/electron/radiator parameters
3. Observation grid
4. Single electron tilted interface transition radiation
5. Beam density models and profile plots
6. 2D/3D form factors
7. Bunch CTR spectrum
8. Longitudinal coherence calculations
9. Optional finite-radiator diagnostic

## Example Use Cases
The code can be used to study:

- How pulse duration affects the onset of coherence
- How CTR spectrum changes with electron energy
- How hollow Gaussian bunch profiles compare to Gaussian profiles
- How Airy propagation affects spectrum
- How coherence depends on wavelength and observation angle

## Notes and Assumptions
Several assumptions are used in the current iteration of the code:

- The bunch is treated classically using a spatial charge-density form factor
- The radiation is calculated in the far-field approximation.
- The interface is assumed to be planar and normal to the beam axis.
- Many calculations integrate over the full azimuthal angle, reducing the problem to dependence on observation angle and wavelength
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

- Adding experimental detector acceptances
- Coupling to PIC simulations
  
