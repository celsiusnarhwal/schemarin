# Building Schemarin

If you're interested in building Schemarin from source, you've come to the right place. If you're a normal person,
install Schemarin with the [instructions in the README](README.md#installation).

Schemarin is written in Python, so you must have Python installed to build it. Schemarin only warrants compatibility
with CPython 3.10 and later (other Python implementations are not tested). You are advised to install Python from its
[official website](https://python.org/downloads) or with a version manager such as [asdf](https://asdf-vm.com). 

Schemarin uses [Poetry](https://python-poetry.org) for dependency management. Poetry is required
to install Schemarin's dependencies and to build its wheel and tarball; Schemarin does not provide `requirements.txt` 
or `setup.py` files.

Schemarin, naturally, requires [iTerm2](https://iterm2.com). You don't strictly have to run Schemarin *in* iTerm2, and
it mostly[^1] runs just fine in other terminal emulators. Whether Schemarin believes iTerm2 is installed is dependent
on whether it can find `~/Library/Preferences/com.googlecode.iterm2.plist`, the existence of which Schemarin checks for 
at runtime, and will exit if it cannot ascertain. 


Also, Schemarin is only compatible with macOS, because iTerm2 is only available on macOS. Schemarin performs a simple
`sys.platform` check at runtime and will exit if it returns anything other than `darwin`. There's nothing stopping
you from removing this check so Schemarin can run on other platforms, but iTerm2 is not available on other platforms,
so Schemarin will not be able to do anything useful.

## Creating a Development Environment

First and foremost, you must install Poetry.

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

The installer[^2] will either install the latest version of Poetry or honor the `POETRY_VERSION` environment variable, if it is set.
Schemarin is currently built with Poetry 1.1.2.

Once you've installed Poetry, you can clone Schemarin to your local machine:

```bash
git clone https://github.com/celsiusnarhwal/schemarin

# or with the GitHub CLI

gh repo clone celsiusnarhwal/schemarin
```

And install its dependencies:

```bash
cd schemarin  # or wherever you cloned it to
poetry install
```

Poetry will automatically create a virtual environment for you.

To run Schemarin from within your development environment, use `poetry run`:

```bash
poetry run python3 src/schemarin/schemarin.py
```

## Building Distributions

### For Python

`poetry build` will build Schemarin's wheel and tarball, placing them in the `dist` directory of the root of your
development environment.

```bash
poetry build
```

Build options are specified in [pyproject.toml](pyproject.toml) and automatically picked up by `poetry build`.

Once built, you can install either the wheel or tarball with the Python package manager of your choice.


### For Homebrew

Schemarin includes an [Invoke](https://www.pyinvoke.org) task in [tasks.py](tasks.py) that automates the process of 
building its Homebrew formula. Invoke is a development dependency of Schemarin, and should have been installed
automatically when you ran `poetry install`. If you, for some reason, ran `poetry install` with the `--without dev`
or `--no-dev` options, Invoke was not installed, and you will need to install it yourself.

Running the task is simple:

```bash
invoke build-formula Schemarin

# or using the task's alias

invoke hbf Schemarin
```

The task will use [homebrew-pypi-poet](https://github.com/mbadolato/iTerm2-Color-Schemes/blob/master/tools/preview.rb)
to generate a formula and, by default, write that formula to `houkago-tea-tap/Formula/schemarin.rb`, where `houkago-tea-tap`
is a Git submodule linked to its [namesake repository](https://github.com/celsiusnarhwal/homebrew-htt). You may
need to modify the task code to suit your environment.

You can install the generated formula directly:

```bash
brew install --formula houkago-tea-tap/Formula/schemarin.rb
```

---
Once you've built and installed Schemarin, you can run it from the command line by invoking its name:

```bash
schemarin
```

[^1]: Previewing color schemes strictly requires that Schemarin is running in iTerm2.

[^2]: It's strongly recommended that you install Poetry via the official installer, as this document directs you to,
and not via Homebrew (or, god forbid, pip). If you don't like piping to a shell, you are absolutely free to download,
examine, and manually run the installation script yourself.
