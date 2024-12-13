Mochi Test Build Action
=======================

This action is meant to be used by the Mochi collection of packages
to test that they can be built using Spack (their latest version or
any other specified version). The following is a minimal example of
github workflow that tests every day that the latest version of the
package can be built with the develop branch of Spack.

```yaml
name: Build the latest release using Spack

on:
  workflow_dispatch: {}
  schedule:
  - cron: "0 0 * * *"

jobs:
  build:
    runs-on: ubuntu-22.04
    name: Build the latest version of the package
    steps:
    - uses: actions/checkout@v4
    - uses: mochi-hpc/mochi-test-build-action@v1
```

This workflow will infer the name of the package to install from the
name of the repository running the action (for instance if mochi-hpc/mochi-margo
runs the action, then the action will install mochi-margo).

Here is a more complete version of the workflow, specifying all the possible
inputs:

```yaml
name: Build the latest release using Spack

on:
  workflow_dispatch: {}
  schedule:
  - cron: "0 0 * * *"

jobs:
  build:
    runs-on: ubuntu-22.04
    name: Build the latest version of the package
    steps:
    - uses: actions/checkout@v4
    - uses: mochi-hpc/mochi-test-build-action@v1
      with:
        # The package name will default to the name of the repository
        package-name: mochi-margo
        # The version will default to whatever is the preferred version for the package
        # Note that the "@" is required before the version
        package-version: '@1.2.3'
        # If specified, build-cache-token should be set to ${{ secrets.GITHUB_TOKEN }},
        # this will make the workflow push any dependencies built to the mochi-spack-buildcache
        # after a successful installation of the package
        build-cache-token: ${{ secrets.GITHUB_TOKEN }}
        # By default, only the dependencies of the root package will be pushed into the cache.
        # To also push the root package, set push-only-dependencies to false.
        push-only-dependencies: true
        # Version of spack to use (default is the develop branch)
        spack-version: v0.22.1
        # Reference (main or a commit hash) to use for mochi-spack-packages (default is main)
        mochi-spack-packages-version: main
```

