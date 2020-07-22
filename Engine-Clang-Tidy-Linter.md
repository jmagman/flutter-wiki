# Engine Clang Tidy Linter

## Description

In May 2020, [`clang-tidy`](https://clang.llvm.org/extra/clang-tidy/) was added as a CI step to the Flutter Engine.  Previously the only lint checks that were happening in the engine were formatting, there were no semantic checks.  Now there are, but that means there is work to be done migrating all the code to conform to all the lint checks.

If a file has `// FLUTTER_NOLINT` at the top, it has issues with the lint that haven't been addressed and the linter will ignore it.  As the issues are fixed the comments should be removed.

You can run the linter locally by running `flutter/ci/lint.sh`.

## FAQs

### I don't understand this lint error, where do I get help?

You can ask on the `hackers-engine` discord channel.  Ping @gaaclarke or @zanderso if you don't get the response you want.

### Hey, why are/aren't you checking for X?

The checks that are enabled are negotiable.  If you think we are missing something, please discuss it on `hacker-engine`.

### Can I just use `NOLINT` to turn off the error?

You can, but please get explicit approval to do from someone on the team.