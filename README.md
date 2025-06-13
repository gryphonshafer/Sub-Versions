# NAME

Sub::Versions - Subroutine versioning syntactic sugar

# VERSION

version 1.06

[![test](https://github.com/gryphonshafer/Sub-Versions/workflows/test/badge.svg)](https://github.com/gryphonshafer/Sub-Versions/actions?query=workflow%3Atest)
[![codecov](https://codecov.io/gh/gryphonshafer/Sub-Versions/graph/badge.svg)](https://codecov.io/gh/gryphonshafer/Sub-Versions)

# SYNOPSIS

    # ...in a class somewhere...

    package MyExampleClass;
    use strict;
    use warnings;
    use Sub::Versions;

    sub new {
        return bless( {}, shift );
    }

    sub simple_method : v1 {
        return 'version 1';
    }

    sub simple_method : v2 {
        return 'version 2';
    }

    # ...and just for this inline example:
    BEGIN { $INC{'MyExampleClass.pm'} = 1 }

    # ...meanwhile, elsewhere...

    use MyExampleClass;
    my $object = MyExampleClass->new;

    $object->simple_method;     # returns "version 2"
    $object->v1->simple_method; # returns "version 1"

    # select "simple_method" version 42 or higher if available
    $object->subver( '>= 42', 'simple_method' )->();

    # ...or with Moose...

    package MyOtherExampleClass;
    use Moose;
    use Sub::Versions;

    sub simple_method : v1 {
        return 'version 1';
    }

    sub simple_method : v2 {
        return 'version 2';
    }

# DESCRIPTION

This module provides automatic syntactic sugar for simple subroutine versioning.
By specifying a version in the form "v#" as a subroutine attributes, this
module will perform a series of compile time symbol table surgeries so you
can call subroutines by explicit version or the latest version implicitly.

    use MyExampleClass;
    my $object = MyExampleClass->new;

    $object->simple_method;     # calls the latest version of the method
    $object->v1->simple_method; # calls version 1 of the method

Versions must be specified in the form `/v\d+/`. The exact version number you
use is irrelevant. The only importance is the relative value of the version
numbers to each other. The largest version number is considered the most
current version of the subroutine.

    package MyExampleClass;
    use Sub::Versions;

    sub simple_method : v1 {
        return 'version 1';
    }

    sub simple_method : v2 {
        return 'version 2';
    }

## Explicit Versioned Methods

The compile time symbol table surgeries will result in a subroutine being
injected into your class a name that is the combination of the original
subroutine name and the version. So for example, if you have "simple\_method"
and you specify it as the "v2" version, then "simple\_method\_v2" gets injected
into your class. You can access the method directly with this name if you
prefer. But the key thing to keep in mind is that if you write a
"simple\_method\_v2" method and have a "simple\_method" method tied to the "v2"
version, then you'll get a subroutine redefined warning.

## Motivation and Purpose

In the process of building an REST/JSON API web service, I found I needed a way
to very simply version calls to parts of the model. I needed to support legacy
versions in parallel with the most recent version and allow consumers to
explicitly call a particular version.

## Sub Version Selection Method

If you don't know the version you want to access exactly, call the method
`subver()` and provide it with a version vector and method name.

    # select "simple_method" version 42 or higher if available
    $object->subver( '>= 42', 'simple_method' )->();

You can: >, <, >=, <=, or =, with or without a space between that and the
version number. Only specifying a version number implies a = vector.

# SEE ALSO

[Sub::Util](https://metacpan.org/pod/Sub%3A%3AUtil), [Devel::Hook](https://metacpan.org/pod/Devel%3A%3AHook).

You can also look for additional information at:

- [GitHub](https://github.com/gryphonshafer/Sub-Versions)
- [MetaCPAN](https://metacpan.org/pod/Sub::Versions)
- [GitHub Actions](https://github.com/gryphonshafer/Sub-Versions/actions)
- [Codecov](https://codecov.io/gh/gryphonshafer/Sub-Versions)
- [CPANTS](http://cpants.cpanauthors.org/dist/Sub-Versions)
- [CPAN Testers](http://www.cpantesters.org/distro/D/Sub-Versions.html)

# AUTHOR

Gryphon Shafer <gryphon@cpan.org>

# COPYRIGHT AND LICENSE

This software is Copyright (c) 2016-2050 by Gryphon Shafer.

This is free software, licensed under:

    The Artistic License 2.0 (GPL Compatible)
