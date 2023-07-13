Changelog
=========

- new MAJOR version for incompatible API changes,
- new MINOR version for added functionality in a backwards compatible manner
- new PATCH version for backwards compatible bug fixes

v1.2.5
---------
2023-07-13:
    - require minimum python 3.8
    - remove python 3.7 tests

v1.2.4
---------
2023-07-12:
    - introduce PEP517 packaging standard
    - introduce pyproject.toml build-system
    - remove mypy.ini
    - remove pytest.ini
    - remove setup.cfg
    - remove setup.py
    - remove .bettercodehub.yml
    - remove .travis.yml
    - update black config
    - clean ./tests/test_cli.py

v1.2.3.2
---------
2022-06-02: update to github actions checkout@v3 and setup-python@v3

v1.2.3.1
--------
2022-06-01: update github actions test matrix

v1.2.3
--------
2022-03-29: remedy mypy Untyped decorator makes function "cli_info" untyped

v1.2.2
--------
2022-03-25: fix github actions windows test

v1.2.1
-------
2021-11-22: Patch Release
    - fix minor readme.rst bugs
    - remove second github action yml
    - fix "setup.py test"

v1.2.0
------
2021-11-21: Minor Release
    - implement github actions
    - implement system.exit()

v1.1.8
--------
2020-10-09: service release
    - update travis build matrix for linux 3.9-dev
    - update travis build matrix (paths) for windows 3.9 / 3.10

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
