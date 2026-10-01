.. _nexstim_doc:

Nexstim
-------

The :code:`simnibs.nexstim` reader imports a neutral JSON pose interchange into
a :code:`TMSLIST`. It does not read proprietary NBS, NBE, NBX, or coil DAT
files. A separate extraction tool must transform positions into the NIfTI RAS
coordinate system used by the subject's SimNIBS T1.

The reader deliberately rejects ambiguous inputs. The JSON must declare:

* interchange schema version :code:`1.0.0`;
* coordinates in :code:`NIfTI_RAS_mm`;
* a validated Nexstim-to-SimNIBS coil-axis mapping;
* a SimNIBS coil model, either in the file or as :code:`coil_file`; and
* either calibrated dI/dt in A/s or explicit normalized mode.

Normalized positions use :code:`didt=1 A/s`. Their fields are suitable for
relative spatial comparisons only and must not be reported as absolute V/m.
Exploratory files may instead carry explicitly assumed axes or dI/dt scaling.
They are rejected unless :code:`allow_assumed=True` is passed, and their fields
must still be labeled assumed rather than calibrated.

.. code-block:: python

   import simnibs

   tms_list = simnibs.nexstim().read(
       "nexstim_poses.json",
       coil_file="path/to/validated_coil.tcd",
   )

   exploratory = simnibs.nexstim().read(
       "nexstim_assumed_poses.json",
       coil_file="path/to/generic_70mm_figure8.ccd",
       allow_assumed=True,
   )

   # Add tms_list to a SESSION after generating the subject head mesh.

The public interchange contains no patient name or clinical identifier. Source
provenance and any restricted vendor parsing remain the responsibility of the
extracting system.
