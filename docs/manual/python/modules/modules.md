module

d.py - is module
Module caching

import works only one time, but you can reload if needed
u can import global variables with from the

from d import GLOABL_VARIABLE
control * import

define all=["func","SomeClass"]
circula import

# moda.py

import modb

def func_a():
    modb.func_b()

class Base:
    pass

# ----------------------------
# modb.py

import moda

def func_b():
    print('B')

class Child(moda.Base):
    pass

There is a strange import order dependency in this code. Using import modb first works fine, but if you put import moda first, it blows up with an error about moda.Base being undefined.

To understand what is happening, you have to follow the control flow. import moda starts executing the file moda.py. The first statement it encounters is import modb. Thus, control switches over to modb.py. The first statement in that file is import moda. Instead of entering a recursive cycle, that import is satisfied by the module cache and control continues on to the next statement in modb.py. This is good—circular imports don’t cause Python to deadlock or enter a new spacetime dimension. However, at this point in execution, module moda has only been partially evaluated. When control reaches the class Child(moda.Base) statement, it blows up. The required Base class hasn’t been defined yet.

One way to fix this problem is to move the import modb statement someplace else. For example, you could move the import into func_a() where the definition is actually needed:
module reloading

importlib.realod(requests) no c/ c++ extenstions
compilation pychache

When modules are first imported, they are compiled into an interpreter bytecode. This code is written to a .pyc file within a special pycache directory. This directory is usually found in the same directory as the original .py file. When the same import occurs again on a different run of the program, the compiled bytecode is loaded instead. This significantly speeds up the import process.

The caching of bytecode is an automatic process that you almost never need to worry about. Files are automatically regenerated if the original source code changes. It just works.

That said, there are still reasons to know about this caching and compilation process. First, sometimes Python files get installed (often accidentally) in an environment where users don’t have operating system permissions to create the required pycache directory. Python will still work, but every import now loads the original source code and compiles it to bytecode. Program loading will be a lot slower than it needs to be. Similarly, in deploying or packaging a Python application, it may be advantageous to include the compiled bytecode, as that may significantly speed up program startup.

The other good reason to know about module caching is that some programming techniques interfere with it. Advanced metaprogramming techniques involving dynamic code generation and the exec() function defeat the benefits of bytecode caching. A notable example is the use of dataclasses:

Click here to view code image

from dataclasses import dataclass

@dataclass class Point: x: float y: float Dataclasses work by generating method functions as text fragments and executing them using exec(). None of this generated code is cached by the import system. For a single class definition, you won’t notice. However, if you have a module consisting of 100 dataclasses, you might find that it imports nearly 20 times slower than a comparable module where you just wrote out the classes in the normal, if less compact, way.
module search

env PYTHONPATH=/some/path python3 script.py

import sys
sys.path.append('mymodules.zip')
import foo, bar

python execute directory

myapp/ foo.py bar.py main.py You can run Python on it by typing python3 myapp. Execution will start in the main.py file. This also works if you turn the myapp directory into a ZIP archive. Typing python3 myapp.zip will look for a top-level main.py file and execute it if found.
package

graphics/ init.py primitive/ init.py lines.py fill.py text.py ... graph2d/ init.py plot2d.py ... graph3d/ init.py plot3d.py ... formats/ init.py gif.py png.py tiff.py jpeg.py

Whenever any part of a package is first imported, code in the init.py file executes first (if it exists). As noted, this file may be empty, but it can also contain code to perform package-specific initializations. If importing a deeply nested submodule, all init.py files encountered in traversal of the directory structure are executed. Thus, the statement import graphics.primitive.fill would first execute the init.py file in the graphics/ directory followed by the init.py file in the primitive/ directory.

Astute Python users might observe that a package still seems to work if init.py files are omitted. This is true—you can use a directory of Python code as a package even if it contains no init.py. However, what’s not obvious is that a directory with a missing init.py file actually defines a different kind of package known as namespace package. This is an advanced feature sometimes used by very large libraries and frameworks to implement broken plugin systems. In the opinion of the author, this is rarely what you want—you should always create proper init.py files when creating a package.
runninc packgage submodule as script

from ..primitive import lines, text

class Plot2D:
    ...

if __name__ == '__main__':
    print('Testing Plot2D')
    p = Plot2D()
    ...
If you try to run it directly, you get a crash complaining about relative import statements:

Click here to view code image

bash $ python3 graphics/graph2d/plot2d.py
Traceback (most recent call last):
  File 'graphics/graph2d/plot2d.py', line 1, in <module>
    from ..primitive import line, text
ValueError: attempted relative import beyond top-level package
bash $
You can’t move into the package directory and run it there either:

Click here to view code image

bash $ cd graphics/graph2d/
bash $ python3 plot2d.py
Traceback (most recent call last):
  File 'plot2d.py', line 1, in <module>
    from ..primitive import line, text
ValueError: attempted relative import beyond top-level package
bash $

bash $ python3 -m graphics.graph2d.plot2d Testing Plot2D bash $ -m specifies a module or package as the main program. Python will run the module with the proper environment to make sure that imports work. Many of Python’s built-in packages have “secret” features that can be used via -m. One of the most well-known is using python3 -m http.server to run a web server from the current directory.

You can provide similar functionality with your own packages. If the name supplied to python -m name corresponds to a package directory, Python looks for the presence of a main.py in that directory and runs that as the scrip
control package namespace

The primary purpose of a package is to serve as a top-level container for code. Sometimes users will import the top-level name and nothing else. For example:

import graphics This import doesn’t specify any particular submodule. Nor does it make any other part of the package accessible. For example, you’ll find that code like this fails:

Click here to view code image

import graphics graphics.primitive.fill.floodfill(img,x,y,color) # Fails! When only a top-level package import is given, the only file that imports is the associated init.py file. In this example, it’s the file graphics/init.py file.

The primary purpose of an init.py file is to build and/or manage the contents of the top-level package namespace. Often, this involves importing selected functions, classes, and other objects from lower-level submodules. For example, if the graphics package in this example consists of hundreds of low-level functions but most of those details are encapsulated into a handful of high-level classes, then the init.py file might choose to expose just those classes:

Click here to view code image
graphics/init.py

from .graph2d.plot2d import Plot2D from .graph3d.plot3d import Plot3D With this init.py file, the names Plot2D and Plot3D would appear at the top level of the package. A user could then use those names as if graphics were a simple module:

Click here to view code image

from graphics import Plot2D plt = Plot2D(100, 100) plt.clear() ... This is often much more convenient for the user because they don’t have to know how you’ve actually organized your code. In some sense, you’re putting a higher layer of abstraction on top of your code structure. Many of the modules in the Python standard library are constructed in this manner. For example, the popular collections module is actually a package. The collections/init.py file consolidates definitions from a few different places and presents them to the user as a single consolidated namespace.
package exports

One issue concerns the interaction between an __init__.py file and low-level submodules. For example, the user of a package might only want to concern themselves with objects and functions that live in the top-level package namespace. However, the implementor of a package might be concerned with the problem of organizing code into maintainable submodules.

To better manage this organizational complexity, package submodules often declare an explicit list of exports by defining an __all__ variable. This is a list of names that should be pushed up one level in the package namespace. For example:

Click here to view code image

# graphics/graph2d/plot2d.py

__all__ = ['Plot2D']

class Plot2D:
    ...
The associated __init__.py file then imports its submodules using an * import like this:

Click here to view code image

# graphics/graph2d/__init__.py

# Only loads names explicitly listed in __all__ variables
from .plot2d import *

# Propagate the __all__ up to next level (if desired)
__all__ = plot2d.__all__
This lifting process then continues all the way to the top-level package __init__.py. for example:

Click here to view code image

# graphics/__init__.py
from .graph2d import *
from .graph3d import *

# Consolidate exports
__all__ = [
    *graph2d.__all__,
    *graph3d.__all__
]

The gist is that every component of a package explicitly states its exports using the all variable. The init.py files then propagate the exports upwards. In practice, it can get complicated, but this approach avoids the problem of hard-wiring specific export names into the init.py file. Instead, if a submodule wants to export something, its name gets listed in just one place—the all variable. Then, by magic, it propagates up to its proper place in the package namespace.

It is worth noting that although using * imports in user code is frowned upon, it is widespread practice in package init.py files. The reason it works in packages is that it is usually much more controlled and contained—being driven by the contents of the all variables and not a free-wheeling attitude of “let’s just import everything.”
module objects

__name__

Full module name

__doc__

Documentation string

__dict__

Module dictionary

__file__

Filename where defined

__package__

Name of enclosing package (if any)

__path__

List of subdirectories to search for submodules of a package.

__annotations__

Module-level type hints

8.16 Deploying Python Packages The final frontier of modules and packages is the problem of giving your code to others. This is a large topic that has been the focus of active ongoing development over many years. I won’t try to document a process that’s bound to be out-of-date by the time you read this. Instead, direct your attention to the documentation at https://packaging.python.org/tutorials/packaging-projects.

For the purposes of day-to-day development, the most important thing is to keep your code isolated as a self-contained project. All of your code should live in a proper package. Try to give your package a unique name so that it doesn’t conflict with other possible dependencies. Consult the Python package index at https://pypi.org to pick a name. In structuring your code, try to keep things simple. As you’ve seen, there are many highly sophisticated things that can be done with the module and package system. There is a time and place for that, but it should not be your starting point.

With absolute simplicity in mind, the most minimalistic way to distribute pure Python code is to use the setuptools module or the built-in distutils module. Suppose you have written some code and it’s in a project that looks like this:

Click here to view code image

spam-project/ README.txt Documentation.txt spam/ # A package of code init.py foo.py bar.py runspam.py # A script to run as: python runspam.py To create a distribution, create a file setup.py in the topmost directory (spam-project/ in this example). In this file, put the following code:

Click here to view code image
setup.py

from setuptools import setup

setup(name = 'spam', version = '0.0' packages = ['spam'], scripts = ['runspam.py'], ) In the setup() call, packages is a list of all package directories, and scripts is a list of script files. Any of these arguments may be omitted if your software does not have them (for example, if there are no scripts). name is the name of your package, and version is the version number as a string. The call to setup() supports a variety of other parameters that supply various metadata about your package. See the full list at https://docs.python.org/3/distutils/apiref.html.

Creating a setup.py file is enough to create a source distribution of your software. Type the following shell command to make a source distribution:

Click here to view code image

bash $ python setup.py sdist ... bash $ This creates an archive file, such as spam-1.0.tar.gz or spam-1.0.zip, in the directory spam/dist. This is the file you would give to others to install your software. To install, a user can use a command such as pip. For example:

Click here to view code image

shell $ python3 -m pip install spam-1.0.tar.gz This installs the software into the local Python distribution and makes it available for general use. The code will normally be installed into a directory called site-packages in the Python library. To find the exact location of this directory, inspect the value of sys.path. Scripts are normally installed into the same directory as the Python interpreter itself.

If the first line of a script starts with #! and contains the text python, the installer will rewrite the line to point to the local installation of Python. Thus, if your scripts have been hardcoded to a specific Python location, such as /usr/local/bin/python, they should still work when installed on other systems where Python is in a different location.

It must be stressed that the use of setuptools as described here is absolutely minimal. Larger projects may involve C/C++ extensions, complicated package structures, examples, and more. Covering all of the tools and possible ways to deploy such code is beyond the scope of this book. You should consult various resources on https://python.org and https://pypi.org for the most up-to-date advice.

8.17 The Penultimate Word: Start with a Package When first starting a new program, it is easy to start with a simple single Python file. For example, you might write a script called program.py and start with that. Although this will work fine for throwaway programs and short tasks, your “script” may start growing and adding features. Eventually, you might want to split it into multiple files. It’s at that point that problems often arise.

In light of this, it makes sense to get in the habit of starting all programs as a package from the onset. For example, instead of making a file called program.py, you should make a program package directory called program:

program/ init.py main.py Put your starting code in main.py and run your program using a command such as python -m program. As you need more code, add new files to your package and use package-relative imports. An advantage of using a package is that all of your code remains isolated. You can name the files whatever you want and not worry about collisions with other packages, standard library modules, or code written by your coworkers. Although setting up a package requires a bit more work at the start, it will likely save you a lot of headaches later.

8.18 The Final Word: Keep It Simple There is a lot of more advanced wizardry associated with the module and package system than what has been shown here. Consult the tutorial “Modules and Packages: Live and Let Die!” at https://dabeaz.com/modulepackage/index.html to get an idea of what’s possible.

All things considered, however, you’re probably better off not doing any advanced module hacking. Managing modules, packages, and software distribution has always been a source of pain in the Python community. Much of the pain is a direct consequence of people applying hacks to the module system. Don’t do that. Keep it simple and find the power to just say “no” when your coworkers propose to modify import to work with the blockchain.

Next: [Builtins](builtins.md)
