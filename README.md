
This document is being developed as topics emerge through code reviews, collected [here](https://docs.google.com/spreadsheets/d/1F72ZMBK1bjE4q6jh_LIx908JRS5Z9L0q7YonTnXQ7-E/edit#gid=0), and as a notes from [Hitchhiker's Guide to Python](http://docs.python-guide.org/en/latest/). Additional books and resources to be added.

The intention is for this document to evolve into a OpenInvest specific Python styleguide, tool ecosystem and onboarding document. It's not there yet.


## Markdown Tools ##

* [Markdown-Viewer](https://github.com/simov/markdown-viewer) is a google chrome extension that renders markdown files without pushing to git. Follow instructions to set "Allow access to file URLs" as this is required to make rendering happen.
* [Markdown cheat sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

## Python Tools ##

### Editors ###

#### Sublime Text 3 w/ Python Linting ####

Instructions mostly sourced [here](http://cheng.logdown.com/posts/2015/06/14/sublime3-install-python-linters).

1. Install [Sublime Text 3](https://www.sublimetext.com/3).
1. PEP8 wants 4 spaces for tabs. Open a `.py` file. Go to `Sublime Text > Preferences > Settings - Syntax Specific` to open the `Python.sublime-settings` file, add the following:
```"tab_size": 4, "translate_tabs_to_spaces": true```
1. Open Package Control. `cmd+shift+p` on Mac, `ctrl+shift+p` on Windows.
1. Type `install` and select `Package Control: Install Packages`.
1. Type `SublimeLinter` and select from list.
1. Install Python libraries `pep8` and `pyflakes`: ```pip3 install pep8 pyflakes```
1. Go back to Sublime Package Control and install `SublimeLinter-pycodestyle`, `SublimeLinter-pyflakes` and `SublimeLinter-pydocstyle`.
1. Restart Sublime Text 3 and open a .py file. You should now see error highlights in the gutter.
1. Now we have style-error highlighting, lets add auto-linting. Go back to Sublime Package Control. Install `AutoPEP8` and `Python PEP8 Autoformat`. Run the autolinters using hotkeys `cmd/ctrl + Shift + 8` to format code, `cmd/ctrl + 8` to preview changes for `AutoPEP8` and `ctrl + shift + r` for `Python PEP8 Autoformat`.

Note that the linting tools we just installed can be used directly from the command line:
```
pep8 file-to-parse.py
```

This will output suggested corrections in the terminal window.

`autopep8` can be used to automatically reformat code in the PEP8 style. The `--in-place flag will overwrite the original code with the `PEP8` linted version. If this flag is omitted modified code will be output directly to the console for review (or piping to another file). The `--aggressive` flag can be added for more substantial changes and can be applied multiple times for greater effect.

```
pip3 install autopep8
autopep8 --in-place file-to-parse.py
```

### IDEs ###

#### PyCharm IDE ####

Python IDE for Professional Developers.

Summary
* "Nearly perfect" code completion
* Good support for virtual environments
* Paid version extends to web development tools

[Download](https://www.jetbrains.com/pycharm/).

[Installation instructions](https://www.jetbrains.com/help/pycharm/basic-tutorials.html).

[Basic tutorials](https://www.jetbrains.com/help/pycharm/basic-tutorials.html).

#### Spyder IDE ####

Scientific PYthon Development EnviRonment is an IDE specifically geared toward working with scientific python libraries.

Spyder comes bundled with the [Anaconda distribution](https://www.anaconda.com/download/#macos), which also includes Numpy, SciPy, IPython and Matplotlib.

### Isolation Tools ###

#### Virtual Environments ####

A virtual environment keeps dependencies required for different projects in separate places. The `virtualenv` command creates a separate folder that contains a link to the Python executable, a copy of `pip`, and a place for Python libraries. It prepends that location to the `PATH` upon activation, and then returns the `PATH` to its original state when deactivated. 

A virtual environment cannot be moved once it is created, paths are hardcoded to current absolute path to the interpreter in the virtual environment's `bin/` directory.

##### Create and Activate #####

*OSX & Linux.* Specify the version of python with `--python` argument. Use the `activate` script to set the `PATH`, entering the virtual environment:
```
cd my-project-folder
virtualenv --python python3 my-venv
source my-venv/bin/activate
```

*Windows.* Set the system execution policies to allow a locally created script to run. Open PowerShell as administrator and type:
```
PS C:\> Set=ExecutionPolicy RemoteSigned
```

Reply `Y` to the question that appears, `exit`, then in regular PowerShell you can create a virtual environment:
```
PS C:\> cd my-project-folder
PS C:\> virtualenv --python python3 my-venv
PS C:\> .\my-venv\Scripts\activate
```

##### Adding Libraries #####

Once a virtual environment is activated, the first `pip` executable in your path will be the one located in `my-venv`, and it will install libraries in the following directory:
* *OSX & Linux.* `my-venv/lib/python3.4/site-packages/`
* *Windows.* `my-venv\Lib\site-packages`

With the virtual environment activated you can bundle packages or projects by writing all of the currently installed packages (which are hopefully also project dependencies) to the file `requirements.txt`.
```
pip freeze > requirements.txt
```

Collaborators can install all of the dependencies in their own virtual environment when given a `requirements.txt` file by typing:
```
pip install -r requirements.txt
```

`pip` will install the listed dependencies, overriding dependency specifications in sub-packages if conflicts exist. Dependencies specified in `requirements.txt` are intended to set the entire python environment. To set dependencies when distributing a library it is better to use the `install_requires` keyword argument in the `setup()` function in a `setup.py` file.

*Warning:* Be careful not to use `pip install -r requirements.txt` outside of a virtual environment. If you do, and anything in `requirements.txt` is a different version than the one installed on your computer, `pip` will overwrite the other version of the library with the one specified in `requirements.txt`.

*Warning:* remember to exclude the virtual environment folder from source
control by adding it to the ignore list.

##### Deactivate #####

To return to normal system settings:
```
deactivate
```

#### Docker ####

TBD

##### To Do #####

- [ ] What is [`pipenv`](https://github.com/kennethreitz/python-guide/blob/master/docs/dev/virtualenvs.rst)?
- [ ] Read [Virtual Environments docs](https://github.com/kennethreitz/python-guide/blob/master/docs/dev/virtualenvs.rst)
- [ ] Research Python + Docker

### Style Recommendations ###

Some essential style recommentations emerging from The Zen of Python by Tim Peters.

#### Explicit is better than implicit ####

A good rule of thumb is that another developer should be able to read the first line and the last lines of your function and understand what it does.

#### Sparse as better than dense ####

Make only one statement per line. Some compound statements, such as list comprehensions, are allowed and appreciated becuase of their expressiveness, but it is a good practice to keep disjoint statements on separate lines of code. It also makes diffs more understantable.

Bad:
```
if (<complex comparison> and <other complex comparison>):
	# do something
```

Good:
```
cond1 = <complex comparison>
cond2 = <other complex comparison>
if cond1 and cond2:
	# do something
```

#### Errors should never pass silently / Unless explicitly silenced ####

##### To Do #####
- [ ] Research `try` / `except` / `pass` / `break` / `raise`

Do not let an error pass silently without handling it by name or re-raising it.

Example:
```
for tag in tags:
	try:
		identity = get_identity_by_name(tag)
		break
	except ClassNotFound:
		pass
```

If the identity cannot be discerned from the tag, then the `get_identity_by_name` function returns a `ClassNotFound` exception. As this is an expected exception, it is caught, nothing is done and the loop continues until an identity is discovered or the loop finishes naturally.

However, if any other type of exception occurs that is not handled (e.g. `KeyboardInterrupt`) it is raised to the top level and excecution stops.

We should avoid the use of overzealous error trapping. An `except` clause without any specified exception will catch everything (including `KeyboardInterrupt`!) and ignore it. A broad `except` clause can also hide bugs, leaving them to cause problems later on.

Always explicitly identify by name the exceptions you will catch, and handle only those exceptions.

It is OK to log or otherwise acknoweldge the exception and re-raise it:
```
while True:
	try:
		print("ni", end="end-")
	except:
		print("An exception happened. Raising.")
		raise
```

#### Functional arguments should be intuitive to use ####

```
def func( positional, keyword=value, *args, **kwargs ):
	pass
```

1. positional arguments are mandatory and have no default values
  * use when there are only a few function arguments, which are fully part of the function's meaning, with a natural easy to remember order. e.g. `send(message, recipient)`
1. keyword arguments are optional and have default values
  * use for two or three positional parameters, signature is more difficult to remember, and using keyword arguments with default values is helpful. e.g. `send(message, recipient, cc=None, bcc=None)`
  * *anti-pattern:*it is possible to follow the order of arguments in the function definition without explicitly naming the arguments, e.g. `send('hi', 'bob', 'frank', 'joe')`. It is also possible to name arguments in another order e.g. `send('hi', 'bob', bcc='frank', cc='joe')`. It is better to use the form closest to the function definition, e.g. `send('hi', 'bob', cc='joe', bcc='frank')`.
  * it is harder to remove an optional argument added "just in case" than to add a new optional argument with logic when needed.
1. an arbitrary argument list `*args` is optional and has no default values
  * extensible number of positional arguments.
  * in the function body `args` will be a tuple of all remaining positional arguments.
  * if a function receives a list of arguments of the same nature, it's often more clear to use a list or any sequence. If `send` has multiple recipients, it can be defined explicitly as `send(message, recipients)` and called with `send("42", ["bob","alice","fong"])`
1. an arbitrary keyword argument dictionary `**kwargs` is optional and has no default values
  * passes an undetermined series of named arguments to the function. in the function body the `kwargs` will be a dictionary of all passed named arguments that were not caught by other keyword arguments in the function signature.
  * both `*args` and `**kwargs` can and should be replaced with other names when other names make more sense.

Users will appreciate your effort when your functions are:
* easy to read, with a name and arguments that need to explanation
* easy to change, adding a new keyword argument won't break other parts of the code

##### To Do #####

- [ ] argparser + arg parsing best practices

#### Return values from one place ####

To keep a clear intent and sustain readibility, it is best to return meaningful values from as few points in the body of a function as possible. It is difficult to debug functions when you first have to identify which return statement is responsible for a result.

In cases when a function cannot perform correctly, it can be appropriate to return `None` or `False`, in which case it is better to return from the function as early as the incorrect context has been detected to flatten the structure of the function.

#### Long lines of code ####

The python interpreter will join consecutive lines if the last character is a backslash. This should usually be avoided because it is fragile, a whitespace character added to the end of the line after the backslash will break the code and may have unexpected results.

A better solution is to use parentheses around your elements. Let with an unclosed parenthesis the python interpreter will join the next line until the parentheses are closed (also true for square or curly braces).

Bad:
```
long_text =\
"this is the first part of the string and \
this is the second part of the string."
```

Good:
```
long_text = (
"this is the first part of the string and 
this is the second part of the string."
)
```

#### Other Stylish Things ####

* use `dict.get('hello', 'default_value')` to access dictionaries to prevent errors when a key is not present.
* [list comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions) are powerful. As are `map` and `filter`.
* use `enumerate()` to keep count iterating over a list, it is more readable and better optimized for iterators.
```
a = ["a", "b", "c", "d"]
for i, item in enumerate(a):
	print("{i}:{item}".format(i=i,item=item))
```
* use unpacking to assign names to elements directly. Ignore unused values with double underscore `__`. Many styleguides recommend usign single underscore, but this is commonly used as an alias for `gettext.gettext()` and is also used at the interactive prompt to hold the value of the last operation. Double underscore eliminates the risk of accidentaly overwriting the single underscore variable.
```
# filename,ext = "my_photo.orig.png".rsplit(".",1)
print(filename, "is a", ext, "file.")
# my_photo.orig is a png file
```
```
# Extended unpacking available in Python 3
a, *rest = [1,2,3]
# a = 1, rest = 2,3
```
```
a, *middle, c = [1,2,3,4]
# a = 1, middle = [2,3], c = 4
```
```
basename, __, ext = 'foobar.txt'.rpartition('.')
# basename = "foobar", ext = "txt", __ eats the "."
```
* Create a length-N list of the same immutable item using the \* operator `for_nones = [None] * 4` . Use list comprehension for mutable objects `four_lists = [[] for __ in range(4)]`# python-styleguide-and-tools
