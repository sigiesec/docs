.. _virtual_environment_generator:


Virtual Environments
====================

Conan offer three special conan generators to create virtual environments:

- ``virtualenv``:  Declares the :ref:`self.env_info<environment_information>` variables of the requirements.
- ``virtualbuildenv``: Special build environment variables for autotools/visual studio.
- ``virtualrunenv``: Special environment variables to locate executables and shared libraries in the requirements.

These virtual environment generators create two executable script files (.sh or .bat depending on the current operating system), one
for ``activate`` the virtual environment (set the environment variables) and one for ``deactivate`` it.

You can aggregate two or more virtual environments, that means that you can activate a ``virtualenv`` and then activate a ``virtualrunenv`` so you will
have available the environment variables declared in the ``env_info`` object of the requirements plus the special enviroment variables to locate executables
and shared libraries.


Virtualenv generator
--------------------

Conan provides a **virtualenv** generator, able to read from each dependency the :ref:`self.env_info<environment_information>` 
variables declared in the ``package_info()`` method and generate two scripts "activate" and "deactivate". These scripts set/unset all env variables in the current shell.

**Example**:

The recipe of ``cmake-installer/0.1@lasote/testing`` appends to the PATH variable the package folder/bin.
In the **bin** folder there is a **cmake** executable:


.. code-block:: python

  def package_info(self):
        self.env_info.path.append(os.path.join(self.package_folder, "bin"))



Let's prepare a virtual environment to have available out cmake in the path, open ``conanfile.txt`` and change (or add) **virtualenv** generator:


.. code-block:: text

   [requires]
   cmake-installer/0.1@lasote/testing

   [generators]
   virtualenv


Run ``conan install``:

.. code-block:: bash

   $ conan install

And activate the virtual environment:


.. code-block:: bash

   $ source activate.sh # Windows: activate.bat without the source
   


In windows are available ``activate.bat``/``deactivate.bat`` and ``activate.ps1``/``deactivate.ps1`` if you are using powershell.
   
Now you can run ``cmake --version`` and check that you have the installed CMake in path.


Deactivate the virtual environment (or close the console) to restore the environment variables:


.. code-block:: bash

   $ source deactivate.sh # Windows: deactivate.bat without the source
   

.. seealso:: Read the Howto :ref:`Create installer packages<create_installer_packages>` to know more about virtual environment feature.
             Check the section :ref:`Reference/virtualenv<virtualenv_generator>` to see the reference of the generator.



Virtualbuildenv environment
---------------------------

Use the generator ``virtualbuildenv`` to activate an environment that will set the environment variables for
Autotools and Visual Studio.

This will generate ``activate_build`` and ``deactivate_build`` files.

.. seealso:: Read More about the building environment variables defined in the sections :ref:`Building with autotools <building_with_autotools>` and :ref:`Building with Visual Studio <building_with_visual_studio>`.

             Check the section :ref:`Reference/virtualbuildenv<virtualbuildenv_generator>` to see the reference of the generator.


Virtualrunenv generator
---------------------------

Use the generator ``virtualrunenv`` to activate an environment that will:

- Append to ``PATH`` environment variable every ``bin`` folder of your requirements.
- Append to ``LD_LIBRARY_PATH`` and ``DYLIB_LIBRARY_PATH`` environment variables each ``lib`` folder of  your requirements.

This generator is specially useful:

- If you are requiring packages with shared libraries and you are running some executable that needs those libraries.
- If you have a requirement with some tool (executable) and you need it in the path.

In the previous example of the ``cmake_installer`` recipe, even if the cmake_installer package doesn't declare the ``self.env_info.path`` variable,
using the virtualrunenv generator, the ``bin`` folder of the package will be available in the PATH. So after activating the virtual environment we could just run ``cmake`` and
we will be executing the cmake of the package.


.. seealso:: Check the section :ref:`Reference/virtualrunenv<virtualrunenv_generator>` to see the reference of the generator.