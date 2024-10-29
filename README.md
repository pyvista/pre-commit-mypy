mypy pre-commit hook
====================

PyVista's `pre-commit` hook for running `mypy`.

For pre-commit, see https://github.com/pre-commit/pre-commit

For mypy, see https://github.com/python/mypy

This fork alters the default configuration of the https://github.com/pre-commit/mirrors-mypy hook.
It:
1. Removes the "mirror" part so that the release version of this hook is no longer tied to the Mypy version. 
Instead, the Mypy version is controlled via `additional_dependencies`. This allows users to manually control the 
version in their `pre-commit-config.yaml` file instead of requiring a new release of this hook to update it.
2. Removes the [default args](https://github.com/pre-commit/mirrors-mypy/blob/33f4a30be4e66513c51a857712fc732e1a9ddd78/.pre-commit-hooks.yaml#L7) `["--ignore-missing-imports", "--scripts-are-modules"]`
so that users have full control over the arguments passed to `mypy`. In particular, the use of `--ignore-missing-imports`
is [discouraged by the `mypy` developers](https://github.com/python/mypy/issues/13916).
3. Uses `pass_filenames: false`, which is [recommended by the `mypy` developers](https://github.com/python/mypy/issues/13916).


### Using mypy with pre-commit:

Add this to your `.pre-commit-config.yaml`

```yaml
- repo: https://github.com/pyvista/pre-commit-mypy
  rev: v0.1
  hooks:
    - id: mypy
      args: [] # Recommend leaving this blank. Set args via 'pyproject.toml' or 'mypy.ini' instead.
      additional_dependencies:
        # Recommend duplicating dependencies for your project (e.g. from  'pyproject.toml')
        - mypy<1.14.0  # Make sure `mypy` is included
        - matplotlib<3.9.3
        - typing-extensions<4.13.0
        - scipy<1.15.0
        - # include any other dependencies here
```
Recommended configuration:
1. Leave `args` blank and use a configuration file instead to control the arguments passed to `mypy`.
E.g. set these via `pyproject.toml` or `mypy.ini`. See https://mypy.readthedocs.io/en/stable/config_file.html for details.
2. Keep `addtional_dependencies` synchronized with all of your project's dependencies, e.g. from `pyproject.toml`.

Following these recommendations ensures that running `mypy` with the pre-commit hook performs exactly the same analysis as 
running `mypy` directly.

Note that using the `--install-types` option is problematic and should not be included in your mypy configuration. Mutating the pre-commit
environment at runtime breaks cache and will break parallel builds.
