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
