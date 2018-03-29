# PHP Code Standards
The words "MUST", "SHOULD", "MAY", "OPTIONAL" and "REQUIRED", as well as phrases
"MUST NOT", "SHALL NOT" and "SHOULD NOT", that are used in this document
are to be interpreted as described in [RFC 2119].

Packages complying with the Dhii standard for PHP code must follow the below
rules. 

1.  Complete compatibility with the [PSR-1] and [PSR-2] standards MUST be maintained.

    This MAY be done partly by relying on the [dhii/php-cs-fixer-config] package.
    However, because the term [used][psr-2-properties] in relation to the
    underscore prefix for private/protected methods is "SHOULD NOT", that prefix
    is OPTIONAL. This is encouraged, and allows avoiding any potential conflicts
    in method names, which is especially important given the layered
    architecture.
    
2.  Classes, interfaces, and traits MUST obey the [PSR naming conventions][psr-naming-conventions].

    This allows the above, collectively called "classes" due to existing
    in the same symbol space and sharing many characteristics, to be
    predictably named, and vice-versa - to have some of their
    characteristics be obvious from the name of the class and consequently
    the filename.

3.  Other existing standards SHOULD be used, when possible.

    Standards are scarce, and creating more is one of the main goals of this
    organization. However, because interoperability is one of our main
    priorities, if a suitable standard is made available - either by [PHP-FIG]
    or other parties - effort will be made to use it. Often this is done by
    extending or otherwise adapting the standard.

4.  Generic interfaces MUST be published separately from any implementation.

    Often developers who mean well would create interfaces for their
    implementations. Unfortunately, publishing these interfaces together
    with the implementations defeats the point of having interfaces in the
    first place, as it makes it impossible to provide an alternative without
    relying on the implementing package itself. Therefore, interfaces with
    non-application-specific methods have to be published in separate packages.

5.  Releases MUST follow the [SemVer] scheme.

    Having a transparent and predictable pattern for release versions is
    extremely important for all software. For standards, the value of
    transparency and predictability cannot be overrated. Therefore, all
    packages complying with this specification must follow the semantic
    versioning scheme. In addition, packages must also comply with the rules
    of the [caret operator][caret-operator].

6.  A changelog MUST be kept.

    It is important that consumers can at a glance understand what changed
    between versions. For this reason, a changelog must be maintained.
    At present, the only thing resembling a standard is at [keepachangelog.com].
    Therefore, following these guidelines is required. Also, see (7) below.

7.  Documentation MUST be included.

    All previously undocumented characteristics of a method must be documented
    with every declaration. All declarations of all class-level entities,
    be it methods, properties, constants etc, must have at least the type
    and `@since` version documented. Including the description is also
    encouraged, although not strictly required. In all instances, be it
    code or otherwise, the string "\[*next-version*\]" represents the unknown
    version of the next release. This gets replaced by package maintainers
    at the time of the release with an actual version number. Also, the use of
    [Markdown] in docblocks is allowed and encouraged.

8.  Adequate tools SHOULD be used.

    Tools such as Composer, Git, PHP CS Fixer, PHPUnit have become an industry
    standard, and their use is highly encouraged. Use of any other tools
    is also welcome, provided that they are useful for the case, and are
    a development dependency.

    To aid development and encourage creation of
    new packages, standard configuration is provided for some of the tools:
    [.codeclimate.yml], [.gitattributes], [.gitignore], [.php_cs], [.travis.yml],
    [CHANGELOG.md], [LICENSE], [README.md], [composer.json], [phpmd.xml], [phpunit.xml].
    Replace "YYYY" with the current year, "package-name" with the package slug,
    "PackageName" with the package namespace, and finally "Package" with the
    package title.

    Also, to speed things up, [dhii/bootstrap] is available for a very fast
    and standards-compliant way to create new Dhii projects.

9.  Logic layers MUST be separated into packages.

    A package does one thing. There can be packages, which exist solely for the purpose
    of grouping other packages. There can be packages that only add a very small
    modification to existing packages. There can be packages, which add or create a lot
    of functionality. This is all fine, and encouraged. What's not fine is packages which
    do many things at once, pretending or attempting to provide some "comprehensive"
    solution or set of solutions. A package should do one thing, no matter how small or
    big it is. The aim is to, as much as possible, allow developers to use only the code
    that they need - by splitting each solution into layers, and providing those layers
    in a separate and granular fashion. Even in a large mechanism, the smallest of
    screws is invaluable, if it has been designed for usability, created for
    functionality, and tested for durability.

    For this reason, it is very important that solutions are broken down into the
    following layers, each published separately. Additionally, if an interface
    from a package is used in the code (with the exception of typechecking -
    see below), that package MUST be explicitly declared as a dependency -
    even if it is already a dependency of one of the immediate dependencies.

    #### Interface Layer

    Functionality SHALL NOT go into this layer. Interface packages
    MAY depend on other interface packages. Depending on packages containing
    functionality is NOT RECOMMENDED. Tests MUST verify that classes
    implementing the interfaces can be created, and that they implement all
    other required interfaces - which may be a consequence of an interface
    hierarchy. Such package names MUST be suffixed with `-interface` -
    regardless of the amount of interfaces declared.

    #### Abstract Layer

    Concrete implementations SHALL NOT go into this layer. Only abstract classes
    are allowed. Methods MUST NOT be declared as `public`. Including static
    methods is NOT RECOMMENDED to preserve the IoC principle. Methods which
    are meant to create instances MUST be `abstract`. A very high standard
    of granularity should be maintained. Only generic functionality can be
    implemented at this layer. Abstract classes MUST NOT implement interfaces,
    and MUST NOT declare a public constructor. If descendants are anticipated to
    implement specific interfaces, its methods or constants MUST NOT be
    referred to as if belonging to the abstract class, i.e. by using the
    `static` keyword, or in any other way. Instead, the protected
    methods of the abstract class itself SHOULD be invoked. Interface constants
    MUST be referred to by using their fully qualified name, i.e.
    `MyInterface::CONSTANT`. Implementations MAY use the `use` keyword to alias
    other classes and interfaces to shorter symbols. Other functionality,
    except for that in the package itself and its dependencies, SHOULD NOT be
    assumed, with the exception of typechecking, such as in cases
    where additional processing is needed for specific types,
    or when certain exceptions need to be handled. In these and only these cases,
    implementations MAY refer to the types being tested against without
    declaring their packages to be dependencies of the implementing package.
    This is because checking for types, explicitly or for exception handling,
    does not cause fatal errors if the checked type is unavailable, i.e.
    the existence of the type itself is not required. This layer is meant
    to be remain completely agnostic of any particular use-case, environment,
    or application. Such package names MUST be suffixed with `-abstract`.

    #### Base Layer (optional)

    This level allows creation of concrete functionality. Implementations
    MAY declare public methods and concreate classes. However, this is done only
    for the purpose of saving time when coding concrete implementations
    that frequently use the most common functionality, such as interface
    (public) methods, or exceptions. Classes SHOULD be declared as
    `abstract` even if they have enough data or functionality to be
    instantiated. Such classes MUST be prefixed with the word "Base",
    in addition to the PSR naming conventions mentioned earlier in this
    document. This means that for a class which extends
    `AbstractTranslator` and is designed as a base for a `Translator`
    class implementing `TranslatorInterface`, the suitable name for a
    base abstract class is `AbstractBaseTranslator`. One exception to this rule
    is for exception classes, i.e. those extending `Exception, which will be
    instantiated by factory methods`. Such
    classes MUST be declared as concrete, and the name MUST have the
    concrete form, such as `class ValidationException extends
    AbstractValidationException implements ValidationExceptionInterface`.
    This level is the right place for factory methods which create exceptions,
    implementing `abstract` methods declared in the Abstract layer. This
    layer is meant to remain agnostic of any use-case, environment, or
    application, consuming only the code declared either directly in the package,
    or in one of its dependencies. All concrete implementations SHOULD assume
    instance immutability. For cases where mutability is desired, an immutable
    implementation MUST be provided alongside. Such package names MUST be
    suffixed with `-base`.

    #### Concrete Layer

    This level is intended be completely or partly aware of its environment,
    use-case, or application. It MAY consume code that is not immediately
    a part of the package or one of its dependencies. This is particularly
    useful when creating extensions for frameworks and engines, and is
    allowed, provided that the documentation clearly states its intended
    application. Concrete implementations MUST contain all logic needed
    for instances to function properly. Abstract classes MAY be included in order
    to maintain SoC, but MUST NOT use their own public API, even for methods
    declared directly or inherited from base classes. Explicit use of DI
    container instances is OPTIONAL on this level, and on no other level.
    This is because all types and the concrete use-case is known here.
    Implementations MUST NOT expect non-public methods to "chain",
    unless their specific purpose is to return the instance.
    There is no restriction with regard to repository/package names on this
    level, as long as they are descriptive and readable. However, these names
    SHOULD reflect the use-case of the package.

    #### Also

    Both the aim and a direct consequence of the described layer system is
    a well defined, flexible, and very predictable class hierarchy tree.
    This tree follows these principles:

    -   Implementations invoking a public method of a dependency MUST be
        consuming an interface.

        This means that you may only call a public method which is not
        defined by any interface explicitly implemented by the instance's
        class if you are guaranteed to operate on the specific concrete
        type of the instance.

    -   Implementations SHOULD NOT extend concrete classes.
    
        When declaring a concrete class, the use-case is known. Such a
        class is known as "leaf", and its API or internal behaviour is
        subject to high churn. Depending on it may result in the
        invalidation of the whole class' descendant hierarchy.

    -   Implementations SHOULD extend an abstract class.
    
        Abstract functionality is aimed to be as generic as possible,
        and is subject to much less churn. Such abstract classes are
        known as "branch" classes. They gradually specialize functionality
        from most generic to more specific changes.

    -   An implementation of a "leaf" class MUST extend at least 1 "branch"
        class and MUST implement at least 1 interface.

        This ensures that other implementations can be created without
        having to implement a public API, without explicitly closing
        access to functionality, and without code duplication by
        extending an abstract class containing the necessary code.
        Moreover, this ensures that consumers can fulfill the requirement
        of having to consume an interface when invoking public methods.

10.  Transparent naming.

    Interfaces SHOULD be named to reflect the essence of the functionality
    which they aim to expose. Abstract class names SHOULD correlate with the
    names of the interfaces, which their descendants aim to implement.
    Names of concrete classes SHOULD be descriptive and readable, but
    otherwise are not restricted. Example:

    -   `interface TranslationCapableInterface` declares `public function translate()`.
    -   `interface TranslatorInterface extends TranslationCapableInterface`.
    -   `abstract class AbstractTranslator` declares `protected function _translate()`.
    -   `class Translator extends AbstractTranslator implements TranslatorInterface`
        declares `public function translate()`, which uses `_translate()`.

11. Adequate testing.

    Implementation SHOULD aim for complete test coverage, and SHOULD only test
    code of the repository containing the tests. Tests SHOULD NOT be
    declared as "unit tests" unless they test units in complete
    isolation. Test classes SHOULD be declared in a base namespace of the form
    `<Vendor>\<Package>\FuncTest` and `<Vendor>\<Package>\UnitTest`, where
    `<Vendor>` represents the name of the package's vendor or author, and
    `<Package>` represents the name of the package. Packages SHOULD avoid
    declaring stub classes. if declared, tuch classes MUST use a base 
    namespace of the form `<Vendor>\<Package>\TestStub`. Protected methods
    SHOULD be tested as well as public ones, which is true of any functionality
    Tests for getters and setters SHOULD NOT test protected properties;
    instead, such tests SHOULD test that the values returned
    by the getters are predicted by those set by the setters. Setters and
    getters are not guaranteed to read or write to/from any specific location,
    as long as value integrity is preserved. Remember: protected methods
    are an API too, but for descendants instead of external consumers.



[SemVer]:                   http://semver.org/
[PHP-FIG]:                  http://www.php-fig.org
[PSR-1]:                    http://www.php-fig.org/psr/psr-1/
[PSR-2]:                    http://www.php-fig.org/psr/psr-2/
[psr-2-properties]:         http://www.php-fig.org/psr/psr-2/#properties
[psr-naming-conventions]:   http://www.php-fig.org/bylaws/psr-naming-conventions/
[caret-operator]:           https://getcomposer.org/doc/articles/versions.md#caret
[Markdown]:                 https://daringfireball.net/projects/markdown/syntax
[keepachangelog.com]:       http://keepachangelog.com
[RFC 2119]:                 https://www.ietf.org/rfc/rfc2119.txt

[dhii/php-cs-fixer-config]: https://packagist.org/packages/dhii/php-cs-fixer-config
[dhii/bootstrap]:           https://packagist.org/packages/dhii/bootstrap

[.codeclimate.yml]:         ../configs/.codeclimate.yml
[.gitattributes]:           ../configs/.gitattributes
[.gitignore]:               ../configs/.gitignore
[.php_cs]:                  ../configs/.php_cs
[.travis.yml]:              ../configs/.travis.yml
[CHANGELOG.md]:             ../configs/CHANGELOG.md
[LICENSE]:                  ../configs/LICENSE
[README.md]:                ../configs/README.md
[composer.json]:            ../configs/composer.json
[phpmd.xml]:                ../configs/phpmd.xml
[phpunit.xml]:              ../configs/phpunit.xml
