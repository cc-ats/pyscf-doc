.. _theory_rt:
**************************************************
Real-time time-dependent density functional theory
**************************************************

Introduction
============
Real-time time-dependent density functional theory (RT-TDDFT), which is also known as time-dependent Kohn-Sham (TDKS) theory, has emerged in recent years as a powerful tool in the modeling of various molecular spectra  and electron/exciton transfer.

For single molecules, RT-TDDFT has been employed to compute the UV/Vis absorption spectra,
near-edge x-ray absorption spectra, electronic responses of molecules in strong fields.

A minimal example of using the SCF module is as follows::

    from pyscf import gto, scf
    mol = gto.M(
        atom = 'H 0 0 0; F 0 0 1.1',  # in Angstrom
        basis = 'ccpvdz',
        symmetry = True,
    )
    mf = scf.HF(mol)
    mf.kernel()

This will run a HF calculation for the hydrogen fluoride molecule using the default SCF settings.

Theory
======