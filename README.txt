========================
installing this buildout
========================

Just run the bootstrap.py and create every needed directory and file
to run buildout:

 $ python bootstrap.py

Finally just insert:

 $ bin/buildout

This will install all plone products and other dependencies, too.

You can start your Zope instance by running:

 $ bin/instance start

or, to run in foreground mode:

 $ bin/instance fg

To run unit tests, you can use:

 $ bin/instance test -s my.package

Working with buildout.cfg
-------------------------

You can change any option in buildout.cfg and re-run bin/buildout to reflect
the changes. This may delete things inside the 'parts' directory, but should
keep your Data.fs and source files intact.

To save time, you can run buildout in "offline" (-o) and non-updating (-N)
mode, which will prevent it from downloading things and checking for new
versions online:

 $ bin/buildout -Nov

Creating new eggs
-----------------

New packages you are working on (but which are not yet released as eggs and
uploaded to the Python Package Index, aka PYPI) should be placed in src. You can do:

 $ cd src/
 $ paster create -t plone my.package

Use "paster create --list-templates" to see all available templates. Answer
the questions and you will get a new egg. Then tell buildout about your egg
by editing buildout.cfg and adding your source directory to 'develop':

 [buildout]
 ...
 develop =
    src/my.package

You can list multiple packages here, separated by whitespace or indented
newlines.

You probably also want the Zope instance to know about the package. Add its
package name to the list of eggs in the "[instance]" section, or under the
main "[buildout]" section:

 [instance]
 ...
 eggs =
    ${buildout:eggs}
    ${plone:eggs}
    my.package

Leave the ${buildout:eggs} part in place - it tells the instance to use the
eggs that buildout will have downloaded from the Python Package Index
previously.

If you also require a ZCML slug for your package, buildout can create one
automatically. Just add the package to the 'zcml' option:

 [instance]
 ...
 zcml =
    my.package

When you are finished, re-run buildout. Offline, non-updating mode should
suffice:

 $ bin/buildout -Nov

=============
Using Windows
=============

To use buildout on Windows, you will need to install a few dependencies which
other platforms manage on their own.

Here are the steps you need to follow (thanks to Hanno Schlichting for these):

Python (http://python.org)
--------------------------

  - Download and install Python 2.4.4 using the Windows installer from
    http://www.python.org/ftp/python/2.4.4/python-2.4.4.msi
    Select 'Install for all users' and it will put Python into the
    "C:\Python24" folder by default.

  - You also want the pywin32 extensions available from
    http://downloads.sourceforge.net/pywin32/pywin32-210.win32-py2.4.exe?modtime=1159009237&big_mirror=0

  - And as a last step you want to download the Python imaging library available
    from http://effbot.org/downloads/PIL-1.1.6.win32-py2.4.exe

  - If you develop Zope based applications you will usually only need Python 2.4
    at the moment, so it's easiest to put the Python binary on the systems PATH,
    so you don't need to specify its location manually each time you call it.

    Thus, put "C:\Python24" and "C:\Python24\Scripts" onto the PATH. You can
    find the PATH definition in the control panel under system preferences on
    the advanced tab at the bottom. The button is called environment variables.
    You want to add it at the end of the already existing PATH in the system
    section. Paths are separated by a semicolons.

  - You can test if this was successful by opening a new shell (cmd) and type
    in 'python -V'. It should report version 2.4.4 (or whichever version you
    installed).

    Opening a new shell can be done quickly by using the key combination
    'Windows-r' or if you are using Parallels on a Mac 'Apple-r'. Type in 'cmd'
    into the popup box that opens up and hit enter.


Subversion (http://subversion.tigris.org)
-----------------------------------------

  - Download the nice installer from
    http://subversion.tigris.org/files/documents/15/35379/svn-1.4.2-setup.exe

  - Run the installer. It defaults to installing into
    "C:\Program Files\Subversion".

  - Now put the install locations bin subfolder (for example
    "C:\Program Files\Subversion\bin") on your system PATH in the same way you
    put Python on it.

  - Open a new shell again and type in: 'svn --version' it should report
    version 1.4.2 or newer.


MinGW (http://www.mingw.org/)
-----------------------------

  This is a native port of the gcc compiler and its dependencies for Windows.
  There are other approaches enabling you to compile Python C extensions on
  Windows including Cygwin and using the official Microsoft C compiler, but this
  is a lightweight approach that uses only freely available tools. As
  it's used by a lot of people chances are high it will work for you and there's
  plenty of documentation out there to help you in troubleshooting problems.

  - Download the MinGW installer from
    http://downloads.sourceforge.net/mingw/MinGW-5.1.3.exe?modtime=1168794334&big_mirror=1

  - The installer will ask you which options you would like to install. Choose
    base and make here. It will install into "C:\MinGW" by default. The install
    might take some time as it's getting files from sourceforge.net and you
    might need to hit 'retry' a couple of times.

  - Now put the install location's bin subfolder (for example "C:\MinGW\bin") on
    your system PATH in the same way you put Python on it.

  - Test this again by typing in: 'gcc --version' on a newly opened shell and
    it should report version 3.4.2 or newer.


Configure Distutils to use MinGW
--------------------------------

  Some general information are available from
  http://www.mingw.org/MinGWiki/index.php/Python%20extensions for example but
  you don't need to read them all.

  - Create a file called 'distutils.cfg' in "C:\Python24\Lib\distutils". Open it
    with a text editor ('notepad distutils.cfg') and fill in the following lines:

    [build]
    compiler=mingw32

    This will tell distutils to use MinGW as the default compiler, so you don't
    need to specify it manually using "--compiler=mingw32" while calling a
    package's setup.py with a command that involves building C extensions. This
    is extremely useful if the build command is written down in a buildout
    recipe where you cannot change the options without hacking the recipe
    itself. The z2c.recipe.zope2install used in ploneout is one such example.
