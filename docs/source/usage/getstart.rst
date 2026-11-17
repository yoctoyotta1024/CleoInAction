.. _getstart:

Getting Started
===============

Clone the GitHub repository:

.. code-block:: console

  $ git clone https://github.com/yoctoyotta1024/CleoInAction.git

We provide a ``pyproject.toml`` file for creating the python version and all the pip-installable
packages you need to use this repository (e.g to run the scripts, to build the documentation,
or to run pre-commit etc.). We provide a ``environment.yaml`` file for all non-pip-installable
packages available from conda-forge.

Everything will be much easier for you if you install the python version and package depenencies as
per our ``pyproject.toml`` file. You can acheive this quickly and simply using
`uv <https://docs.astral.sh/uv/>`_. *hint*: optionally, you can install ``uv`` within a
Conda/Micromamba environment, which also installs our other non-pip-installable packages (e.g. doxygen)
using our ``environment.yaml`` file, e.g.

.. code-block:: console

  $ mamba create --file=environment.yaml
  $ mamba activate cleo-in-action

Using ``uv`` to setup python, simply do:

.. code-block:: console

  $ uv sync

Sometimes it is useful to know where uv has installed your python, in which case you can run:

.. code-block:: console

  $ uv python find

Finally, install our pre-commit hooks:

.. code-block:: console

  $ uv run pre-commit install

That's it, you're done!

We kindly ask that you also :ref:`contact us <contact>` or
`open a new issue <https://github.com/yoctoyotta1024/CleoInAction/issues/new>`_
on our GitHub repository if you discover this environment is missing any packages.
