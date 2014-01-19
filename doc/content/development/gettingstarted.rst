.. Copyright (C) 2005 - 2013  Eric Van Dewoestine

   This program is free software: you can redistribute it and/or modify
   it under the terms of the GNU General Public License as published by
   the Free Software Foundation, either version 3 of the License, or
   (at your option) any later version.

   This program is distributed in the hope that it will be useful,
   but WITHOUT ANY WARRANTY; without even the implied warranty of
   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
   GNU General Public License for more details.

   You should have received a copy of the GNU General Public License
   along with this program.  If not, see <http://www.gnu.org/licenses/>.

Developers Guide
================

This guide is intended for those who wish to contribute to eclim by
fixing bugs or adding new functionality.

Checking out the code and building it.
--------------------------------------

.. begin-build

1. Check out the code:
^^^^^^^^^^^^^^^^^^^^^^

::

  $ git clone git://github.com/ervandew/eclim.git

.. note::

  If you are still using Eclipse 3.7 (Indigo) you will need to checkout the
  eclim indigo branch before attempting to build eclim:

  ::

    $ cd eclim
    $ git checkout indigo

2. Build eclim:
^^^^^^^^^^^^^^^

::

  $ cd eclim
  $ ant -Declipse.home=/your/eclipse/home/dir

.. note::

  If your eclipse home path contains a space, be sure to quote it:

  ::

    > ant "-Declipse.home=C:/Program Files/eclipse"

This will build and deploy eclim to your eclipse and vim directories.

.. note::

  If your vimfiles directory is not located at the default location for your
  OS, then you can specify the location using the "vim.files" property:

  ::

    $ ant -Dvim.files=<your vimfiles dir>

When the build starts, it will first examine your eclipse installation to
find what eclipse plugins are available. It will then use that list to determine
which eclim features/plugins should be built and will output a list like the one
below showing what will be built vs what will be skipped:

::

  [echo] ${eclipse}: /opt/eclipse
  [echo] # Skipping org.eclim.adt, missing com.android.ide.eclipse.adt
  [echo] # Skipping org.eclim.dltk, missing org.eclipse.dltk.core
  [echo] # Skipping org.eclim.dltkruby, missing org.eclipse.dltk.ruby
  [echo] # Skipping org.eclim.pdt, missing org.eclipse.php
  [echo] Plugins:
  [echo]   org.eclim.cdt
  [echo]   org.eclim.jdt
  [echo]   org.eclim.pydev
  [echo]   org.eclim.sdt
  [echo]   org.eclim.wst

In this case we can see that four eclim plugins will be skipped along with the
eclipse feature that would be required to build those plugins. If you see an
eclipse feature in that list that you know you have, it may be the case that you
installed it as a regular user, so that feature was installed in your user local
eclipse directory. In that case you will need to notify the build of that
directory so it can examine it as well (just replace the ``<version>`` portion
below with the actual version found in your ~/.eclipse directory):

::

  $ ant \
      -Declipse.home=/opt/eclipse \
      -Declipse.local=$HOME/.eclipse/org.eclipse.platform_<version>

If you don't want to supply the eclipse home directory, or any other
properties, on the command line every time you build eclim, you can create a
``user.properties`` file at the eclim source root and put all your properties
in there:

::

  $ vim user.properties
  eclipse.home=/opt/eclipse
  vim.files=${user.home}/.vim/bundle/eclim

.. end-build

.. _coding-style:

Coding Style
------------

When contributing code please try to adhere to the coding style of similar code
so that eclim's source can retain consistency throughout. For java code, eclim
includes a checkstyle configuration which can be run against the whole project:

::

  $ ant checkstyle

or against the current java file from within vim:

::

  :Checkstyle

.. _development-patches:

Developing / Submitting Patches
-------------------------------

The preferred means of developing and submitting patches is to use a github
fork. Github provides a nice `guide to forking`_ which should get you started.

Although using a github fork is preferred, you can of course still submit
patches via email using git's format-patch command:

::

  $ git format-patch -M origin/master

Running the above command will generate a series of patch files which can be
submitted to the `eclim development group`_.

Building the eclim installer
----------------------------

It should be rare that someone should need to build the eclim installer, but
should the need arise here are the instructions for doing so.

To build the installer you first need a couple external tools installed:

* sphinx_: Sphinx is used to build the eclim documentation which is included in
  the installer.

  Eclim also uses a custom sphinx theme which is included in eclim as a git
  submodule. So before you can build the installer you will need to initialize
  the submodule:

  ::

    $ git submodule init
    $ git submodule update

* graphviz_:  The docs include a few uml diagrams which are generated using
  plantuml_ (included in the eclim source tree) which in turn requires
  graphviz_.

* formic_: The eclim installer has been developed using the formic framework,
  and requires it to build the installer distributables.  Formic doesn't
  currently have an official release, so you'll need to check out the source
  code:

  ::

    $ git clone git://github.com/ervandew/formic.git

  After checking out the code, you'll need to build the formic distribution:

  ::

    $ cd formic
    $ ant dist

  Then extract the formic tar to the location of your choice

  ::

    $ tar -zxvf build/dist/formic-0.2.0.tar.gz -C /location/of/your/choice

Once you have installed the above dependencies, you can then build the eclim
installer with the following command.

::

  $ ant -Dformic.home=/your/formic/install/dir dist

In lieu of supplying the formic home on the command line, you can instead put
it in a ``user.properties`` file at the eclim source root:

::

  $ vim user.properties
  formic.home=/your/formic/install/dir

What's Next
------------

Now that you're familiar with the basics of building and patching eclim, the
next step is to familiarize yourself with the eclim architecture and to review
the detailed docs on how new features are added.

All of that and more can be found in the
:doc:`eclim development docs </development/index>`.


.. _git: http://git-scm.com/
.. _eclim development group: http://groups.google.com/group/eclim-dev
.. _guide to forking: http://help.github.com/forking/
.. _git-format-patch: http://www.kernel.org/pub/software/scm/git/docs/git-format-patch.html
.. _sphinx: http://sphinx.pocoo.org
.. _plantuml: http://plantuml.sourceforge.net/
.. _graphviz: http://www.graphviz.org/
.. _formic: http://github.com/ervandew/formic
