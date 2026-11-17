.. _requirements:

Requirements
============

The following requirements ensure CLEO's build, compilation and execution on DKRZ's Levante HPC.
If they do not work, please :ref:`contact us <contact>` or `open a new
issue <https://github.com/yoctoyotta1024/CleoInAction/issues/new>`_ on our GitHub repository.

Of course other architectures, other compilers, versions etc. are possible, but we leave this for
you to discover.


Compilers
---------
A C++ compiler with the C++20 standard library and MPI is the absolute minimum.

On Levante you can use the latest MPI compiler wrappers for the gcc compilers.
At the time of writing this is gcc 11.2.0, e.g. for a CPU build:

.. code-block:: console

  $ module load gcc/11.2.0-gcc-11.2.0 openmpi/4.1.2-gcc-11.2.0

CMake
-----
CMake minimum version 3.18.0.

On Levante the default version is above 3.26.0 so this should be fine. In case you have trouble,
you can use spack to find (and load) a higher version yourself (``spack find cmake``).

.. code-block:: console

  $ spack load cmake@3.26.3%gcc@=11.2.0/fuvwuhz


YAC
---

YAC is one of the external libraries which Cleo requires. YAC (and its YAXT dependency) need to be
installed manually before you can build Cleo with them.
Please refer to Cleo's instructions on how to
`install YAC and YAX <https://yoctoyotta1024.github.io/CLEO/usage/installation/extern.html>`_.

YAC also requires some additional MPI, NetCDF and yaml libraries alongside the compatible gcc/intel
compiler. E.g. On Levante with gcc 11.2.0:

.. code-block:: console

  $ module load gcc/11.2.0-gcc-11.2.0 openmpi@4.1.2%gcc@11.2.0 netcdf-c/4.8.1-openmpi-4.1.2-gcc-11.2.0
  $ spack load openblas@0.3.18%gcc@=11.2.0

When you want to run Cleo, you will also need to export the fyaml library and python bindings paths,
e.g. On Levante:

.. code-block:: console

  $ export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/sw/spack-levante/libfyaml-0.7.12-fvbhgo/lib
  $ export PYTHONPATH=${PYTHONPATH}:/your/path/to/yac/python/


Python
------
We advise you to create an environment using our ``envirnoment.yaml`` file and
`uv <https://docs.astral.sh/uv/>`_ (see :ref:`getstart`). Your ``uv`` environment should then
automatically include all the additional packages you may require. If not, please
:ref:`contact us <contact>` or `open a new issue <https://github.com/yoctoyotta1024/CleoInAction/issues/new>`_
on our GitHub repository.

.. admonition:: mpi4py on HPCs

  On HPCs, running python with MPI via a conda/mamba environment can create all sorts of issues
  because you want to use the HPC's MPI installation, but conda/mamba installs it's own
  (see e.g. https://conda-forge.org/docs/user/tipsandtricks/#using-external-message-passing-interface-mpi-libraries).
  On Levante, we have found that installing conda's mpi wrapper, de- and re-installing mpi4py and then
  deleting any mpi libraries installed by mamba/conda seems to do the trick via this sequence of
  commands:

  .. code-block:: console

    $ module load python3 gcc/11.2.0-gcc-11.2.0 openmpi/4.1.2-gcc-11.2.0
    $ uv pip uninstall mpi4py
    $ export MPI4PY_BUILD_MPICC=/sw/spack-levante/openmpi-4.1.2-mnmady/bin/mpicc  # mpicc wrapper
    $ export MPI4PY_BUILD_MPILD=/sw/spack-levante/openmpi-4.1.2-mnmady/lib  # path to mpi libraries (libmpi.so)
    $ uv pip install --no-cache-dir --no-binary=mpi4py mpi4py
    $ uv run python -c 'from mpi4py import MPI; print(f"MPI version: {MPI.Get_version()}")'  # check installation

  (*hint:* if this still doesn't work: try creating a ``cleo-in-action`` environment from a clone of
  another environment that does work with MPI (e.g. ```clouds``), then install mpi4py, and afterwards
  install the other requirements (``python -m pip install -r requirements.txt``). Delete any
  ``libmpi.so`` and ``libmpi.so.40`` libraries created in the process, e.g.
  ``rm  /work/bm1183/m300950/bin/envs/cleo-in-action/lib/libmpi.so.40`` and
  ``rm /work/bm1183/m300950/bin/envs/cleo-in-action/lib/libmpi.so``.)

  See the `mpi4py documentation <https://mpi4py.readthedocs.io/en/stable/install.html#build-backends>`_
  for more information.

 .. admonition:: Using PyBind11 on ARM64 (Macbooks with M1 Processors) instead of x86_64 Architectures

  If you are using Python on an ARM64 architecture (e.g. you have a Mac with an M1 silicon chip) then
  you need to ensure the python libraries have been installed for ARM64 and *not* x86_64
  architectures. You can check this by entering either ``file [your_python_library].dylib`` or
  ``file [your_python_library].so``, which will tell you which architecture your python is installed
  for.

  If you don't know where your python libraries are installed, have a look at ``which python`` to find
  the path to your python interpreter, e.g. something like ``/path/to/python/bin/python``. Then
  look in ``/path/to/python/lib/`` for something like ``libpython[X.Y].dylib`` or ``libpython[X.Y].so``
  where X.Y is your python version (which you can get from ``python --version``).

  If your python libraries are not ARM64, then you will have problems... One solution is to create a
  conda enviroment which specifically uses conda-forge's osx-arm64 directory to find packages, e.g. via

  .. code-block:: console

    $ CONDA_SUBDIR=osx-arm64 conda create -n [my_arm64_env] numpy -c conda-forge
    $ conda activate [name_my_arm64_env]
    $ conda config --env --set subdir osx-arm64

  where ``[name_my_arm64_env]`` is the name of your new enviroment.
