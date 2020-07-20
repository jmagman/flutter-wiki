# Engine Clang Tidy Linter

## Description

In May 2020, [`clang-tidy`](https://clang.llvm.org/extra/clang-tidy/) was added as a CI step to the Flutter Engine.  Previously the only lint checks that were happening in the engine were formatting, there were no semantic checks.  Now there are, but that means there is work to be done migrating all the code to conform to all the semantic checks.  As part of working on the engine we've decided progressively fix the issues as they arise, thus sharing the work.

## FAQs

### Hey, why am I getting this lint error in code I didn't write?

We decided that cleaning up the code should be a responsibility shared across all the developers.  If you see a lint error in a file you've edited please address the problem in your PR or in a separate PR.

### I don't understand this lint error, where do I get help?

You can ask on the `hackers-engine` discord channel.  Ping @gaaclarke or @zanderso if you don't get the response you want.

### Hey, why are/aren't you checking for X?

The checks that are enabled are negotiable.  If you think we are missing something, please discuss it on `hacker-engine`.

### Can I just use `NOLINT` to turn off the error?

You can, but please get explicit approval to do from someone on the team.