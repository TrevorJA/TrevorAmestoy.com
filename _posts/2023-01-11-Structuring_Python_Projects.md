---
title: 'Structuring a Python Project - Recommendations and a Template Example'
date: 2022-01-11
permalink: /posts/2022/01/PythonProjects/
tags:
  - Python
  - example
---

# Structuring a Python Project - Recommendations and a Template Example

## Motivation:

The start of a new year is a good (albeit, relatively arbitrary) time to reassess aspects of your workflow.

I, like many people, taught myself Python by jumping into different projects. The consequence of this ad-hoc learning was that I did not learn some of the fundamentals until much later in my project development.

At the end of the Fall '23 semester, I found myself spending a lot of time cleaning up repositories and modules that I had constructed in the preceding months. I ended up restructuring and reorganizing significant portions of the code base, implementing organizational practices that I had learned after it's conception.

This post is intended to save the reader from making the same mistake.  Here, I present a recommended structure for new Python projects, and discuss the main components. This is largely targeted at Python users who have not had a *formal* Python training, or who are working with their first significantly sized project.  

I provide an [example_python_project](https://github.com/TrevorJA/example_python_project), hosted on GitHub, as a demonstration and to serve as a template for beginners.

## Content:
1. Recommended structure
2. Example project repository
3. Project components (with example project)
	1. Modules, packages, \__init__.py
	2. Executable
	3. README
	4. Documentation
	5. Tests
	6. Requirements
	7. LICENSE
4. Conclusion

*******

## Recommended project structure

I'll begin by presenting a recommended project structure.  Further down, I provide some explanation and justification for this structure.

My [example_python_project](https://github.com/TrevorJA/example_python_project) follows recommendations from Kenneth Reitz, co-author of [*The Hitchhiker's Guide to Pythton*](https://docs.python-guide.org/) (1),  while also drawing from a similar demo-project, [samplemod](https://github.com/navdeep-G/samplemod), by Navdeep Gill (2).

The project folder follows this structure:

```
example_python_project/
├── sample_package/
│   ├── subpackage/
│   │   ├── __init__.py
│   │   └── subpackage_module.py
│   ├── __init__.py
│   ├── helpers.py
│   └── module.py
├── docs/
│   └── documentation.md
├── tests/
│   ├── __init__.py
│   └── basic_test.py
├── main.py
├── README.md
├── requirements.txt
└── LICENSE
```

If you are just starting a project, it may seem unnecessary complex to begin with so much modularity.  It may seem easier to open a `.py` file and start freewheeling. Here, I am trying to highlight the several reasons why it is important to take care when initially constructing a Python project. Some of these reasons include:

1.  **Maintenance:** A well-structured project makes it easier to understand the code, fix bugs, and add new features. This is especially important as the project grows in size and complexity.
2.  **Collaboration:** When working on a project with multiple developers, a clear structure makes it easier for everyone to understand how the code is organized and how different components interact with each other.
3.  **Scalability:** A well-structured project allows to scale up the codebase, adding new features and sub-components, without making the codebase hard to understand or maintain.
4.  **Testing:** A well-structured project makes it easier to write automated tests for the code. This helps to ensure that changes to the code do not break existing functionality.
5.  **Distribution:** A well-structured project makes it easier to distribute the code as a package. This allows others to easily install and use the code in their own projects.

Overall, taking the time to structure a Python project when starting can save a lot of time and heartache in the long run, by making the project easier to understand, maintain, and expand.


## Example Project Repository

The repository containing this example project is available on GitHub here: [example_python_project](https://github.com/TrevorJA/example_python_project).  

The project follows the recommended project structure above, and is designed to use modular functions from the `module`, `helpers`, and `subpackage_module`.  It is intended to be a skeleton upon which you can build-up your own project.

If you would like to experiment with your own copy of the code, you can [fork a copy of the repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo), or [Download a ZIP version](https://stackoverflow.com/questions/2751227/how-to-download-source-in-zip-format-from-github).

### Project overview

The project is a silly riddle program with no real usefulness other than forming the structure of the project.  The bulk of the work is done in the [`main_module_function()`](https://github.com/TrevorJA/example_python_project/blob/main/sample_package/module.py) which first prints a riddle on the screen, then iteratively uses the `helper_function()` and `subpackage_function()` to try and "solve" the riddle. Both of these functions simply return a random True/False, and are repeatedly called until the riddle is solved (when `status == True`).

Below is a visual representation of how the different functions are interacting. The green-box functions are contained within the main `sample_package`, while the blue-box function is stored in the `subpackage`.  

<img src="/images/Python Project Organization Best Practices-3.png" width = 400 class="center">

The program can then be executed from a command line using the [`main.py`](https://github.com/TrevorJA/example_python_project/blob/main/main.py) executable:

```
C:\<your-local-directory>\example_python_project> python main.py
```

The output will first print out the riddle, then print statements indicating which functions are being used to "solve" the riddle. This is simply a means of demonstrating how the different functions are being activated, and not necessarily a recommended "Best Practice".  

A normal output should resemble something similar to the below, although there may be more or less print statements depending upon how many times it takes the random generator to produce a "True" solution:

```
Here is a riddle, maybe `sample_package` can help solve it:

   What runs but has no feet, roars but has no mouth?

Lets see if the helper can solve the riddle.
The helper_function is helping!
The helper could not solve it.
Maybe the subpackage_module can help.
The subpackage_function is being used now.
The subpackage solved it, the answer is "A River"!
```


*******
## Project components

### Modules, packages, \__init__.py, oh my!

Before going any further, I want to take time to clarify some vocabulary which is helpful for understanding component interactions.

> **Module:**
>		A *module* is simply a file ending in `.py`, which contains functions and or variables.
>		 
 **Package:**
>		A *package* is a collection of *modules* (`.py` files) which relate to one another, and which contains an `__init__.py` file.
>		
  **`__init__.py`**
> 		Inclusion of a `__init__.py` file (pronounced "dunder in-it") within a folder will indicate to Python that the folder is a *package*.  Often, the `__init__` module is empty, however it can be used to import other modules, or functions which will then be stored in *namespace*, making it available for use later.

For example, in my `sample_package/__init__.py`, I import all contents of the `module.py` and `subpackage_module.py`:   

```python
# Import the all functions from main and sub modules
from .module import *
from .subpackage.subpackage_module import *
```

This allows all of the functions stored within `module` to be callable from the primary `sample_package` directly, rather than specifying the various sub-structures needed to access various functions.  For example, by including `from .subpackage.subpackage_module import *`, I able to run:

```python
# IF __init__ imports all content from main and sub modules then you can do this:
import sample_package
sample_package.subpackage_module_function()
```

Rather than requiring the following fully-nested call, which is necessary when the `__init__.py` is empty:

```python
# IF __init__ is EMPTY, then you need to do this:
import sample_package
sample_package.subpackage.subpackage_module.subpackage_module_function()
```

Notably, an `__init__.py` is not *necessary* to use modules and functions within a folder... however, customizing the imports present in the packages `__init__.py` will provide increased customization to your projects use. As the project increases in complexity, strategic usage of imports within the `__init__` can keep your main executable functions cleaner.  


### Executables

So, you've crafted a Python project with a sleek, modular package design. The next step is to setup a single file which will execute the package.

Inclusion of a single executable has the benefit of providing a single-entry point for other users who want to run the program without getting lost in the project.

In the [example_python_project](https://github.com/TrevorJA/example_python_project), this is done with `main.py`:

```python
# Import the main package
import sample_package

def run():
    solved = sample_package.main_module_function()
    return solved

# Run the function if this is the main file executed
if __name__ == "__main__":
    run()
```

The program then can then be executed from a command line:

```cmd
C:\<your-local-directory\example_python_project> python run_program.py
```


### README

The `README.md` file is typically someone's first encounter with your project. This is particularly true if the project is hosted on GitHub, where the `README.md` is used as the home-page of a repository.

A `README.md` file should include, at minimum, a brief description of the project, it's purpose, and clear instructions on how to use the code.

Often, `README` files are written in [Markdown](https://www.markdownguide.org/getting-started/), which includes simple text-formatting options. You can find a basic [Markdown Cheat Sheet here](https://www.markdownguide.org/cheat-sheet/). Although  [reStructuredText](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html) is often used, and even `.txt` files may be suitable.

### Documentation

Great code requires great documentation. Initializing a new project with a dedicated `docs/` folder may help hold you accountable for documenting the code along the way.  

For information on how to use [Sphinx](https://www.sphinx-doc.org/en/master/index.html) and [reStructuredText](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html) to create clean webpage-based documentation, you can see Rohini Gupta's post on [*Using Python, Sphinx, and reStructuredText to Create a Book (and Introducing our eBook: Addressing Uncertainty in Multisector Dynamics Research!)*](https://waterprogramming.wordpress.com/2022/04/07/using-python-sphinx-and-restructuredtext-to-create-a-book-and-introducing-our-ebook-addressing-uncertainty-in-multisector-dynamics-research/).


### Tests

Bugs aren't fun. They are even less fun when a code was bug-free yesterday but contains bugs today. Implementing automated tests in your project can help verify functionality throughout the development process and catch bugs when they may arise.

It is recommended to implement [Unit Tests](https://realpython.com/python-testing/) which verify individual components of the project.  These tests should assert that function output properties align with expectations. As you develop your project in a modular way, you can go in and progressively add consecutive tests, then run all of the tests before sharing or pushing the project to others.

A standard Python instillation comes with the [unittest](https://docs.python.org/3/library/unittest.html) package, which is intended to provide a framework for these tests. I provide an example test below, but deeper-dive into the `unittest` framework may require a dedicated future posts.  

In the `example_python_project`, I include the `basic_test.py` to verify that the solution generated by `main_module_function()` is True using the [unittest](https://docs.python.org/3/library/unittest.html) package:

```python
import sample_package
import unittest

# Define a test suite targeting specific functionality
class BasicTestSuite(unittest.TestCase):
    """Basic test cases."""
    def test_that_riddle_is_solved(self):
        solved = sample_package.module.main_module_function()
        self.assertTrue(solved)


if __name__ == '__main__':
    unittest.main()

```

Running the `basic_test` module from the command line produce an "OK" if everything runs smoothly, otherwise will provide information regarding which tests are failing.

```
----------------------------------------------------------------------
Ran 1 test in 0.004s
OK
```

Currently, the `example_python_project` requires the `basic_test` module to be executed manually. To learn more about automating this process, you can see Andrew Dirck's 2020 post: [*Automate unit testing with Github Actions for research codes*](https://waterprogramming.wordpress.com/2020/10/05/automate-unit-testing-with-github-actions-for-research-codes/).


### Requirements

The `requirements.txt` is a simple text file which lists the *dependencies*, or necessary packages that are required to run the code.

This can be particularly important if your code requires a specific version of a package, since the package verison can be specified in the `requirements.txt`. Specifying a particular package version (e.g., `numpy==1.24.1`) can improve the reliability of your code, since different versions of these packages may operate in different ways in the future.

Here is an example of what might be inside a `requirements.txt`, if the `numpy` and `random` packages are necessary:

```
numpy==1.24.1
random==3.11.1
```

Users can easily install all the packages listed in `requirements.txt` using the command:

```
pip install -r requirements.txt
```


### License

I'll keep this section brief, since I am far from legally qualified to comment much on Licensing.  However, general advice seems to suggest that if you are sharing code publicly, safest to include a license of some sort.

My understanding is that inclusion of an open-source license allows other users to comfortably use and modify your code for their own purposes, allowing you to contribute and benefit the broader community. At the same time, protecting the original author from future liabilities associated with its use by others.

The [GNU General Public License](https://www.gnu.org/licenses/quick-guide-gplv3.html) is the most common open-source license, however if you would like to know more about the different options, you can find some guidance here: https://choosealicense.com/


## Conclusions
If you are an experienced Python user, there may not be anything new for you here but at the least I hope it serves as a reminder to take care in your project design this year.

Additionally, this is likely to be one part in a multi-part Introduction to Python series that I will be writing for future members of our research group.  With that in mind, check back here later this spring for the subsequent parts if that interests you.

Best of luck!


## References
(1) Reitz, K., & Schlusser, T. (2016). [_The Hitchhiker's guide to Python: best practices for development_](https://docs.python-guide.org/writing/structure/). " O'Reilly Media, Inc.".
(2) Navdeep Gill. 2019. samplemod. https://github.com/navdeep-G/samplemod. (2023).
