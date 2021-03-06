# NAME

Object::HashBase - Build hash based classes.

# SYNOPSIS

A class:

    package My::Class;
    use strict;
    use warnings;

    # Generate 3 accessors
    use Object::HashBase qw/foo -bar ^baz/;

    # Chance to initialize defaults
    sub init {
        my $self = shift;    # No other args
        $self->{+FOO} ||= "foo";
        $self->{+BAR} ||= "bar";
        $self->{+BAZ} ||= "baz";
    }

    sub print {
        print join ", " => map { $self->{$_} } FOO, BAR, BAZ;
    }

Subclass it

    package My::Subclass;
    use strict;
    use warnings;

    # Note, you should subclass before loading HashBase.
    use base 'My::Class';
    use Object::HashBase qw/bat/;

    sub init {
        my $self = shift;

        # We get the constants from the base class for free.
        $self->{+FOO} ||= 'SubFoo';
        $self->{+BAT} ||= 'bat';

        $self->SUPER::init();
    }

use it:

    package main;
    use strict;
    use warnings;
    use My::Class;

    my $one = My::Class->new(foo => 'MyFoo', bar => 'MyBar');

    # Accessors!
    my $foo = $one->foo;    # 'MyFoo'
    my $bar = $one->bar;    # 'MyBar'
    my $baz = $one->baz;    # Defaulted to: 'baz'

    # Setters!
    $one->set_foo('A Foo');

    #'-bar' means read-only, so the setter will throw an exception (but is defined).
    $one->set_bar('A bar');

    # '^baz' means deprecated setter, this will warn about the setter being
    # deprecated.
    $one->set_baz('A Baz');

    $one->{+FOO} = 'xxx';

# DESCRIPTION

This package is used to generate classes based on hashrefs. Using this class
will give you a `new()` method, as well as generating accessors you request.
Generated accessors will be getters, `set_ACCESSOR` setters will also be
generated for you. You also get constants for each accessor (all caps) which
return the key into the hash for that accessor. Single inheritance is also
supported.

# INCLUDING IN YOUR DIST

If you want to use HashBase, but do not want to depend on it, you can include
it in your distribution.

    $ hashbase_inc.pl Prefix::For::Module

This will create 2 files:

    lib/Prefix/For/Module/HashBase.pm
    t/HashBase.t

You can then use the includes `Prefix::For::Module::HashBase` instead of
`Object::HashBase`.

You can re-run this script to regenerate the files, or upgrade them to newer
versions.

If the script was not installed, it can be found int he `scripts/` directory.

# METHODS

## PROVIDED BY HASH BASE

- $it = $class->new(@VALUES)

    Create a new instance using key/value pairs.

    HashBase will not export `new()` if there is already a `new()` method in your
    packages inheritance chain.

    **If you do not want this method you can define your own** you just have to
    declare it before loading [Object::HashBase](https://metacpan.org/pod/Object::HashBase).

        package My::Package;

        # predeclare new() so that HashBase does not give us one.
        sub new;

        use Object::HashBase qw/foo bar baz/;

        # Now we define our own new method.
        sub new { ... }

    This makes it so that HashBase sees that you have your own `new()` method.
    Alternatively you can define the method before loading HashBase instead of just
    declaring it, but that scatters your use statements.

## HOOKS

- $self->init()

    This gives you the chance to set some default values to your fields. The only
    argument is `$self` with its indexes already set from the constructor.

# ACCESSORS

To generate accessors you list them when using the module:

    use Object::HashBase qw/foo/;

This will generate the following subs in your namespace:

- foo()

    Getter, used to get the value of the `foo` field.

- set\_foo()

    Setter, used to set the value of the `foo` field.

- FOO()

    Constant, returns the field `foo`'s key into the class hashref. Subclasses will
    also get this function as a constant, not simply a method, that means it is
    copied into the subclass namespace.

    The main reason for using these constants is to help avoid spelling mistakes
    and similar typos. It will not help you if you forget to prefix the '+' though.

# SUBCLASSING

You can subclass an existing HashBase class.

    use base 'Another::HashBase::Class';
    use Object::HashBase qw/foo bar baz/;

The base class is added to `@ISA` for you, and all constants from base classes
are added to subclasses automatically.

# SOURCE

The source code repository for HashBase can be found at
`http://github.com/Test-More/HashBase/`.

# MAINTAINERS

- Chad Granum <exodist@cpan.org>

# AUTHORS

- Chad Granum <exodist@cpan.org>

# COPYRIGHT

Copyright 2016 Chad Granum <exodist@cpan.org>.

This program is free software; you can redistribute it and/or
modify it under the same terms as Perl itself.

See `http://dev.perl.org/licenses/`
