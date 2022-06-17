Contributing to Nucleus One
=======================

First, join [Discord].  Please remember to be respectful.  Go to the `role-assignment` channel and
click the appropriate reaction to be assigned a developer role.  This will give you access to
additional channels.

Next, follow the instructions below to setup your dev environment.

## Welcome

Everyone is welcome to participate.  Contribute code via pull requests on GitHub, along with filing
issues.  Engage with other devs on Discord to get and provide support.

We communicate primarily over GitHub and [Discord].

Before you get too far along, please read the following document, so that you know what's expected
of the community.

- Our [Code of Conduct](CODE_OF_CONDUCT.md), which outlines expectations of community members and
how conflicts are expected to be handled.


## Developing for Nucleus One

To develop for Nucleus One, you will first need to get your dev environment set up.  In-house, we use
[Visual Studio Code](https://code.visualstudio.com), which runs on most OSes.  You are welcome to use
another IDE, such as Android Studio, but much of our setup is configured to run using VS Code.

Getting started is straightforward, just clone the repositories you want to work on.

Please make sure to read the below guide, to make sure you have an understanding of what is expected
of any code you write.
- [Testing Guide](TESTING.md)


## Style guide

Because Nucleus One's repositories span multiple languages, we cannot have a one-size-fits-all style
guide.

Generally speaking, the following styling principals should be observed.
- Favor readability over convention
- Documentation should be thorough, but not verbose
- A standard line should *generally* not exceed 100 characters, unless doing so improves readability
- Error messages should be clear, not vague or cryptic
- All documentation, even private, should use the language's built-in code-commenting system
  (i.e. `///`)


## Documentation

Generally speaking, our apps and SDKs are self documented.  That is, where a comment-based
documentation exists for a given platform, such as Dart (i.e. [dartdoc](https://pub.dev/packages/dartdoc)),
documentation of classes, parameters, etc., usage and even sample code should make use of such system.
This way, documentation can be automatically generated and updated in other formats (e.g. HTML).


[Discord]: https://nucleus.one/#discord "Discord"
