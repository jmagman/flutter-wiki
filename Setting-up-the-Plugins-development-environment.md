## Things you will need

 * A working [Flutter](https://flutter.dev/docs/get-started/install) installation.
 * git (used for source version control).
 * An ssh client (used to authenticate with GitHub).

## Getting the code and configuring your environment

 * Ensure all the dependencies described in the previous section are installed.
 * Fork `https://github.com/flutter/plugins` into your own GitHub account. If
   you already have a fork, and are now installing a development environment on
   a new machine, make sure you've updated your fork so that you don't use stale
   configuration options from long ago.
 * If you haven't configured your machine with an SSH key that's known to github, then
   follow [GitHub's directions](https://help.github.com/articles/generating-ssh-keys/)
   to generate an SSH key.
 * `git clone git@github.com:<your_name_here>/plugins.git`
 * `cd plugins`
 * `git remote add upstream git@github.com:flutter/plugins.git` (So that you
   fetch from the master repository, not your clone, when running `git fetch`
   et al.)

## Setting up tools

There are scripts for many common tasks (testing, formatting, etc.) that will likely be useful in preparing a PR.
See [the tools README](https://github.com/flutter/plugins/blob/master/script/tool/README.md) for more details.

flutter/plugins uses the in-repository copy of the tool, not the published copy.
Set it up by running the following from the root of the repository:
```bash
cd ./script/tool && dart pub get && cd ../../
```

(Using the published version will also work in most cases, but may not always match what the
automated tests do.)