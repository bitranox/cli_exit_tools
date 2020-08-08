cli_exit_tools
==============


Version v1.1.7 as of 2020-08-08 see `Changelog`_

|travis_build| |license| |jupyter| |pypi|

|codecov| |better_code| |cc_maintain| |cc_issues| |cc_coverage| |snyk|


.. |travis_build| image:: https://img.shields.io/travis/bitranox/cli_exit_tools/master.svg
   :target: https://travis-ci.org/bitranox/cli_exit_tools

.. |license| image:: https://img.shields.io/github/license/webcomics/pywine.svg
   :target: http://en.wikipedia.org/wiki/MIT_License

.. |jupyter| image:: https://mybinder.org/badge_logo.svg
 :target: https://mybinder.org/v2/gh/bitranox/cli_exit_tools/master?filepath=cli_exit_tools.ipynb

.. for the pypi status link note the dashes, not the underscore !
.. |pypi| image:: https://img.shields.io/pypi/status/cli-exit-tools?label=PyPI%20Package
   :target: https://badge.fury.io/py/cli_exit_tools

.. |codecov| image:: https://img.shields.io/codecov/c/github/bitranox/cli_exit_tools
   :target: https://codecov.io/gh/bitranox/cli_exit_tools

.. |better_code| image:: https://bettercodehub.com/edge/badge/bitranox/cli_exit_tools?branch=master
   :target: https://bettercodehub.com/results/bitranox/cli_exit_tools

.. |cc_maintain| image:: https://img.shields.io/codeclimate/maintainability-percentage/bitranox/cli_exit_tools?label=CC%20maintainability
   :target: https://codeclimate.com/github/bitranox/cli_exit_tools/maintainability
   :alt: Maintainability

.. |cc_issues| image:: https://img.shields.io/codeclimate/issues/bitranox/cli_exit_tools?label=CC%20issues
   :target: https://codeclimate.com/github/bitranox/cli_exit_tools/maintainability
   :alt: Maintainability

.. |cc_coverage| image:: https://img.shields.io/codeclimate/coverage/bitranox/cli_exit_tools?label=CC%20coverage
   :target: https://codeclimate.com/github/bitranox/cli_exit_tools/test_coverage
   :alt: Code Coverage

.. |snyk| image:: https://img.shields.io/snyk/vulnerabilities/github/bitranox/cli_exit_tools
   :target: https://snyk.io/test/github/bitranox/cli_exit_tools

small toolset to properly exit a cli application:

- print the traceback information (can be set with commandline option)
- get a proper exit code from the Exception
- flush the streams, to make sure output is written in proper order
- demo how to integrate into Your cli module (see usage)

----

automated tests, Travis Matrix, Documentation, Badges, etc. are managed with `PizzaCutter <https://github
.com/bitranox/PizzaCutter>`_ (cookiecutter on steroids)

Python version required: 3.6.0 or newer

tested on linux "bionic" with python 3.6, 3.7, 3.8, 3.8-dev, pypy3 - architectures: amd64, ppc64le, s390x, arm64

`100% code coverage <https://codecov.io/gh/bitranox/cli_exit_tools>`_, flake8 style checking ,mypy static type checking ,tested under `Linux, macOS, Windows <https://travis-ci.org/bitranox/cli_exit_tools>`_, automatic daily builds and monitoring

----

- `Try it Online`_
- `Usage`_
- `Usage from Commandline`_
- `Installation and Upgrade`_
- `Requirements`_
- `Acknowledgements`_
- `Contribute`_
- `Report Issues <https://github.com/bitranox/cli_exit_tools/blob/master/ISSUE_TEMPLATE.md>`_
- `Pull Request <https://github.com/bitranox/cli_exit_tools/blob/master/PULL_REQUEST_TEMPLATE.md>`_
- `Code of Conduct <https://github.com/bitranox/cli_exit_tools/blob/master/CODE_OF_CONDUCT.md>`_
- `License`_
- `Changelog`_

----

Try it Online
-------------

You might try it right away in Jupyter Notebook by using the "launch binder" badge, or click `here <https://mybinder.org/v2/gh/{{rst_include.
repository_slug}}/master?filepath=cli_exit_tools.ipynb>`_

Usage
-----------

- example for the main_cli

.. code-block:: python

    # STDLIB
    import sys
    from typing import Optional

    # EXT
    import click

    # CONSTANTS
    CLICK_CONTEXT_SETTINGS = dict(help_option_names=['-h', '--help'])

    try:
        from . import __init__conf__
        from . import cli_exit_tools
    except (ImportError, ModuleNotFoundError):  # pragma: no cover
        # imports for doctest
        import __init__conf__                   # type: ignore  # pragma: no cover
        import cli_exit_tools                   # type: ignore  # pragma: no cover


    def info() -> None:
        """
        >>> info()
        Info for ...

        """
        __init__conf__.print_info()


    @click.group(help=__init__conf__.title, context_settings=CLICK_CONTEXT_SETTINGS)
    @click.version_option(version=__init__conf__.version,
                          prog_name=__init__conf__.shell_command,
                          message='{} version %(version)s'.format(__init__conf__.shell_command))
    @click.option('--traceback/--no-traceback', is_flag=True, type=bool, default=None, help='return traceback information on cli')
    def cli_main(traceback: Optional[bool] = None) -> None:
        if traceback is not None:
            cli_exit_tools.config.traceback = traceback


    @cli_main.command('info', context_settings=CLICK_CONTEXT_SETTINGS)
    def cli_info() -> None:
        """ get program informations """
        info()


    # entry point if main
    if __name__ == '__main__':
        try:
            cli_main()
        except Exception as exc:
            cli_exit_tools.print_exception_message()
            sys.exit(cli_exit_tools.get_system_exit_code(exc))
        finally:
            cli_exit_tools.flush_streams()

- get the system exit code

.. code-block:: python

    def get_system_exit_code(exc: BaseException) -> int:
        """
        Return the exit code for linux or windows os, based on the exception.
        If, on windows, the winerror code is passed with the Exception, we return that winerror code.


        Parameter
        ---------
        exc
            the exception to analyze


        Result
        ------
        exit_code
            as integer


        Examples
        --------


        >>> try:
        ...     raise RuntimeError()
        ... except RuntimeError as exc:
        ...     assert get_system_exit_code(exc) == 1
        ...     setattr(exc, 'winerror', 42)
        ...     assert get_system_exit_code(exc) == 42
        ...     setattr(exc, 'winerror', None)
        ...     assert get_system_exit_code(exc) == 1

        """

- print the exception message

.. code-block:: python

    def print_exception_message(trace_back: bool = config.traceback, stream: Optional[TextIO] = None) -> None:
        """
        Prints the Exception Message to stderr
        if trace_back is True, it also prints the traceback information

        if the exception has stdout, stderr attributes (like the subprocess.CalledProcessError)
        those will be also printed to stderr


        Parameter
        ---------
        trace_back
            if traceback information should be printed. This is usually set early
            in the CLI application to the config object via a commandline option.
        stream
            optional, to which stream to print, default = stderr


        Examples
        --------


        >>> # test with exc_info = None
        >>> print_exception_message()

        >>> # test with exc_info
        >>> try:
        ...     raise FileNotFoundError('test')
        ... except Exception:       # noqa
        ...     print_exception_message(False)
        ...     print_exception_message(True)

        >>> # test with subprocess to get stdout, stderr
        >>> import subprocess
        >>> try:
        ...     discard=subprocess.run('unknown_command', shell=True, check=True)
        ... except subprocess.CalledProcessError:
        ...     print_exception_message(False)
        ...     print_exception_message(True)
        ...     print_exception_message(True, stream=sys.stderr)

        """

- flush the streams

.. code-block:: python

    def flush_streams() -> None:
        """
        flush the streams - make sure the output is written early,
        otherwise the output might be printed even after another CLI
        command is launched


        Examples
        --------


        >>> flush_streams()

        """

Usage from Commandline
------------------------

.. code-block:: bash

   Usage: cli_exit_tools [OPTIONS] COMMAND [ARGS]...

     functions to exit an cli application properly

   Options:
     --version                     Show the version and exit.
     --traceback / --no-traceback  return traceback information on cli
     -h, --help                    Show this message and exit.

   Commands:
     info  get program informations

Installation and Upgrade
------------------------

- Before You start, its highly recommended to update pip and setup tools:


.. code-block:: bash

    python -m pip --upgrade pip
    python -m pip --upgrade setuptools

- to install the latest release from PyPi via pip (recommended):

.. code-block:: bash

    python -m pip install --upgrade cli_exit_tools

- to install the latest version from github via pip:


.. code-block:: bash

    python -m pip install --upgrade git+https://github.com/bitranox/cli_exit_tools.git


- include it into Your requirements.txt:

.. code-block:: bash

    # Insert following line in Your requirements.txt:
    # for the latest Release on pypi:
    cli_exit_tools

    # for the latest development version :
    cli_exit_tools @ git+https://github.com/bitranox/cli_exit_tools.git

    # to install and upgrade all modules mentioned in requirements.txt:
    python -m pip install --upgrade -r /<path>/requirements.txt


- to install the latest development version from source code:

.. code-block:: bash

    # cd ~
    $ git clone https://github.com/bitranox/cli_exit_tools.git
    $ cd cli_exit_tools
    python setup.py install

- via makefile:
  makefiles are a very convenient way to install. Here we can do much more,
  like installing virtual environments, clean caches and so on.

.. code-block:: shell

    # from Your shell's homedirectory:
    $ git clone https://github.com/bitranox/cli_exit_tools.git
    $ cd cli_exit_tools

    # to run the tests:
    $ make test

    # to install the package
    $ make install

    # to clean the package
    $ make clean

    # uninstall the package
    $ make uninstall

Requirements
------------
following modules will be automatically installed :

.. code-block:: bash

    ## Project Requirements
    click

Acknowledgements
----------------

- special thanks to "uncle bob" Robert C. Martin, especially for his books on "clean code" and "clean architecture"

Contribute
----------

I would love for you to fork and send me pull request for this project.
- `please Contribute <https://github.com/bitranox/cli_exit_tools/blob/master/CONTRIBUTING.md>`_

License
-------

This software is licensed under the `MIT license <http://en.wikipedia.org/wiki/MIT_License>`_

---

Changelog
=========

- new MAJOR version for incompatible API changes,
- new MINOR version for added functionality in a backwards compatible manner
- new PATCH version for backwards compatible bug fixes


v1.1.7
--------
2020-08-08: service release
    - fix documentation
    - fix travis
    - deprecate pycodestyle
    - implement flake8

v1.1.6
--------
2020-08-07: fix wheels

v1.1.5
--------
2020-07-31: fix wheels

v1.1.3
--------
2020-07-31: initial release

