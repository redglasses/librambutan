This file explains how to build the HTML documentation. Users can quickly read
the HTML for the latest release here:

    http://docs.rambutan.cc/projects/librambutan/en/master/

Install Dependencies
--------------------

First, you will need to install some dependencies (Doxygen, Sphinx,
and Breathe).

1. Much of the documentation is pulled out of the libmaple source
   code's Doxygen comments, so you need Doxygen in your PATH:

    http://www.stack.nl/~dimitri/doxygen/download.html#latestsrc

2. Documentation not taken from the libmaple sources is written in
   Sphinx's extensions to the reStructuredText (reST) markup language.
   (More about Sphinx: http://sphinx.pocoo.org/tutorial.html).

   These are your choices for how to install Sphinx:

   - From a Linux package manager, eg:
     $ sudo apt-get install python-sphinx

   - From an OS X package manager, like MacPorts or homebrew.

   - Via pip:
     $ sudo pip install Sphinx

   - Via easy_install:
     $ sudo easy_install -U Sphinx

   - From source or .egg:
     http://pypi.python.org/pypi/Sphinx#downloads

   You need Sphinx version >= 1.0.6.

3. We use a Sphinx plugin called Breathe to parse Doxygen's XML output
   into a form usable by Sphinx. (More about Breathe:
   http://michaeljones.github.com/breathe/).

   The more recent versions of Breathe seem to have all the features necessary
   to build these docs, so you can install the distribution version (breathe-doc).

   Alternatively, LeafLabs sometimes patches Breathe for the purposes of building
   docs, so clone the LeafLabs Breathe tree from GitHub (somewhere
   else on your system, NOT in the librambutan repo):

   - LeafLabs developers, clone with push permissions:
     $ git clone git@github.com:leaflabs/breathe.git

   - Everyone else, clone without them:
     $ git clone git://github.com/leaflabs/breathe.git

   After that's done, set environment variable BREATHE_HOME to point to
   Breathe.  Something like this in Bash:

   $ export BREATHE_HOME=/path/to/breathe/repo/

   (You'll want this in your shell startup script.)

Build the Docs
--------------

You are finally ready to build the documentation.  First, you'll
produce Doxygen XML output, then you can generate the final HTML docs.

1. Before the first time you run Sphinx (and any time the Doxygen
   comments in the libmaple source code are changed), you'll need to
   re-run doxygen on the top level of librambutan.

    $ cd /path/to/top/level/librambutan
    $ make doxygen

   Doxygen will yell at you a lot; it's generally safe to ignore it.

2. Finally, you can build the HTML:

    $ cd /path/to/docs
    $ make html

   On Windows, you can supposedly use the batch file make.bat instead.

   Breathe will yell at you a lot; it's often safe to ignore this,
   too. C'est la vie.

Read and Modify the Docs
------------------------

Point your web browser at the file

  build/html/index.html

For the most part, the file build/html/foo.html is built from
source/foo.rst.  (The index is an exception, because we needed to
hand-hack the HTML to get the "Contents at a Glance" section to look
nice).

All of the documentation which isn't pulled out of libmaple's Doxygen
comments lives in source/.  The directory source/_static/ is for
static content (like style sheets); source/_templates/ is meant to
contain Sphinx templates.

Read more about Sphinx and use the existing source for
examples when writing new docs.  The directory tmpl/ contains template
ReST files you should sometimes use when creating a page that follows
a pattern (like a libmaple API page), in order to keep the style
consistent.

The file source/conf.py is the Sphinx configuration file; you can go
read it for more information about our setup.
