.. _theory_rt:

Real-time time-dependent density functional theory
**************************************************

Introduction
============
Real-time time-dependent density functional theory (RT-TDDFT), which is also known as time-dependent Kohn-Sham (TDKS) theory, has emerged in recent years as a powerful tool in the modeling of various molecular spectra  and electron/exciton transfer.

For single molecules, RT-TDDFT has been employed to compute the UV/Vis absorption spectra,
near-edge x-ray absorption spectra, electronic responses of molecules in strong fields.

A minimal example of using the SCF module is as follows::

    from pyscf import gto, scf
    from pyscf import rt

    h2o =   gto.Mole( atom='''
    O     0.00000000    -0.00001441    -0.34824012
    H    -0.00000000     0.76001092    -0.93285191
    H     0.00000000    -0.75999650    -0.93290797
    '''
    , basis='sto-3g', symmetry=False).build() # water

    h2o_rhf    = scf.RHF(h2o)
    h2o_rhf.verbose = 0
    h2o_rhf.conv_tol = 1e-12
    h2o_rhf.kernel()

    rttd = rt.TDHF(h2o_rhf, field=None)
    rttd.total_step     = 10
    rttd.step_size      = 0.02
    rttd.kernel()

This will run a RT-TDHF calculation for the water molecule.

Theory
======
The theoretical foundation of DFT is formed by Hohenberg, Kohn and Sham during 1964-1965. We have the non-relativistic time-dependent Schrödinger equation,

.. math::

   H \Psi\left(t, \mathbf{x}_{1}, \mathbf{x}_{2}, \mathbf{x}_{3}, \ldots, \mathbf{x}_{N-1}, \mathbf{x}_{N}\right)=i\frac{\partial \,}{\partial t} \Psi\left(t, \mathbf{x}_{1}, \mathbf{x}_{2}, \mathbf{x}_{3}, \ldots, \mathbf{x}_{N-1}, \mathbf{x}_{N}\right)

For an :math:`N` -electron system, the Hamiltonian is,

.. math::

   H=-\frac{1}{2} \sum_{i} \nabla_{i}^{2}+\frac{1}{2} \sum_{j \neq i} \sum_{i} \frac{1}{\left|\mathbf{r}_{i}-\mathbf{r}_{j}\right|}+\sum_{i} v_\mathrm{e x t}\left(\mathbf{r}_{i}\right)

The unit used here is atomic units (a.u.), i.e. 

.. math::

   \hbar=e=4 \pi \epsilon_{0}=m_{e}=1

in such units,

.. csv-table::
   :header: "", "atomic unit", "SI unit"
   :widths: 20, 20, 20

   "Time", "1 a.u", "0.02419 fs"
   "Length", "1 a.u", "52.92 pm"
   "Energy", "1 a.u", "27.2114 eV"
   "Dipole moment", "1 a.u", "2.542 D"

The traditional way in quantum mechanics to solve the time evolution of electron systems is by solving the time independent wavefunctions and finding the ground state and excited states of the system. Then the time evolution state would be given by superposition of the eigenstates, then observables, such as density distribution :math:`\rho(t,\mathbf{r})`, can be therefore calculated.

Density Functional Theory (DFT) provides an alternative way. In DFT, the ground state electronic structure is solved by minimizing energy subject to the possible electron density distribution :math:`\rho(\mathbf{r})`, without calculating the wave function. The validity of this approach is proved by the first Hohenberg-Kohn theorem (the existence theorem). And the method to obtain such density distribution :math:`\rho(\mathbf{r})` is stated in the second Hohenberg- Kohn theorem.

In order to do calculation beyond the ground state, Runge, Gross and Sham generalized the two Hohenberg-Kohn theorems onto the time-dependent domain, which set the theoretical foundation of TDDFT. The generalization of second Hohenberg-Kohn theorem to time-dependent case is implemented by a variation principle on the action,

.. math::

   A=\int_{t_{0}}^{t_{1}}\left\langle\Psi(t)\left|i \frac{\partial}{\partial t}-H(t)\right| \Psi(t)\right\rangle d t

The actual electron density distribution :math:`\rho(t,\mathbf{r})` is the one that makes the action stationary,

.. math::

   \frac{\delta A}{\delta \rho(t,\mathbf{r})}=0

By doing the variation, the time-dependent Kohn-Sham equation is obtained as,

.. math::

   i \frac{ \partial \psi (t,\mathbf{r}) } { \partial t} = \hat{F}  \psi (t,\mathbf{r}) 

:math:`\hat{F}` is the time-dependent Fock operator.

In the canonical molecular orbital representation, it is 

.. math::
   i \frac { \partial \mathbf{C}_\text{o} (t) } {\partial t}  = \mathbf{F} (t) \mathbf{C}_\text{o} (t)   \label{eq:TDKS-ortho} 

and we obtain the time evolution of the one-particle density matrices,

.. math::

   i  \frac { \partial \mathbf{P} (t) } {\partial t} =  \mathbf{F} (t)  \mathbf{P} (t)    -   \mathbf{P} (t)  \mathbf{F}(t)  = \left[  \mathbf{F} (t), \mathbf{P} (t)  \right] 

