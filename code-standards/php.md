# PHP Code Standards
Packages complying with the Dhii standard for PHP code must follow the below
rules:

1.  Maintain complete compatibility with the [PSR-1] and [PSR-2] standards.

    This is done partly by relying on the [dhii/php-cs-fixer-config] package.
    However, because the term [used][psr-2-properties] in relation to the
    underscore prefix for private/protected methods is "SHOULD NOT", this prefix
    MAY be used. This is encouraged, and lets us avoid any potential conflicts
    in method names, which is especially important given our layered
    architecture. Classes, interfaces, and traits MUST obey the [PSR naming
    conventions][psr-naming-conventions].

2.  Use other existing standards wherever possible.

    Standards are scarce, and creating more is one of the main goals of this
    organization. However, because interoperability is one of our main
    priorities, if a suitable standard is made available - either by [PHP-FIG]
    or other parties - effort will be made to use it. Often this is done by
    extending or otherwise adapting the standard.

3.  Publish generic interfaces separately.

    Often developers who mean well would create interfaces for their
    implementations. Unfortunately, publishing these interfaces _together_
    with the implementations defeats the point of having interfaces in the
    first place, as it makes it impossible to provide an alternative without
    relying on the implementing package itself. Therefore, interfaces with
    non-application-specific methods must be published in separate packages.

4.  Follow the [SemVer] scheme.

    Having a transparent and predictable pattern for release versions is
    extremely important for all software. For standards, the value of
    transparency and predictability cannot be overrated. Therefore, all
    packages complying with this specification must follow the semantic
    versioning scheme. In addition, packages must also comply with the rules
    of the [caret operator][caret-operator].

5.  Keep a changelog.

    It is important that consumers can at a glance understand what changed
    between versions. For this reason, a changelog must be maintained.
    At present, the only thing resembling a standard is at [keepachangelog.com].
    Therefore, following these guidelines is required. Also, see (7) below.

6.  Use proper documentation.

    All previously undocumented characteristics of a method must be documented
    with every declaration. All declarations of all class-level entities,
    be it methods, properties, constants etc, must have at least the type
    and `@since` version documented. Including the description is also
    encouraged, although not strictly required. In all instances, be it
    code or otherwise, the string "\[*next-version*\]" represents the unknown
    version of the next release. This gets replaced by package maintainers
    at the time of the release with an actual version number. Also, the use of
    [Markdown] in docblocks is allowed and encouraged.

7.  Use the right tools.

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

8.  Compartmentalization, encapsulation, separation.

    A package does one thing. There can be packages, which exist solely for the purpose
    of grouping other packages. There can be packages that only add a very small
    modification to existing packages. There can be packages, which add or create a lot
    of functionality. This is all fine, and encouraged. What's not fine is packages which
    do many things at once, pretending or attempting to provide some "comprehensive"
    solution or set of solutions. A package should do one thing, no matter how small or
    big it is. The aim is to, as much as possible, allow developers to use only the code
    that they need - by splitting each solution into layers, and providing those layers
    in a separate and granular fashion. Even in a large mechanism, the smallest of
    screws is invaluable, if it it has been designed for usability, created for
    functionality, and tested for durability.

    For this reason, it is very important that solutions are broken down into the
    following layers, each published separately. Additionally, if an interface
    from a package is used in the code (with the exception of typechecking -
    see below), that package MUST be explicitly declared as a dependency -
    even if it is already a dependency of one of the immediate dependencies.

    #### Interface Layer

    No functionality whatsoever may go into this layer. Interface packages
    may depend on other interface packages. Depending on packages containing
    functionality is strongly discouraged. Tests must verify that classes
    implementing the interfaces can be created, and that they implement all
    other required interfaces - which may be a consequence of an interface
    hierarchy. Such packages names MUST be suffixed with `-interface` -
    regardless of the amount of interfaces declared.

    #### Abstract Layer

    No concrete implementations may go into this layer. Only abstract classes
    are allowed. All methods must not be declared as `public`. Static
    methods are advised against to preserve IoC principle. Methods which
    are meant to create instances must be `abstract`. A very high standard
    of granularity should be maintained. Only generic functionality can be
    implemented at this layer. Abstract classes MUST NOT implement interfaces,
    or declare a public constructor. If descendants are anticipated to
    implement specific interfaces, its methods or constants MUST NOT be
    referred to as those belonging to the abstract class, i.e. by using the
    `static` keyword, or in any other way. Instead, only the protected
    methods of the abstract class itself may be invoked. Interface constants
    MUST be referred to by using their fully qualified name, i.e.
    `Interface::CONSTANT`. It is possible to use the `use` keyword to alias
    other classes and interfaces to shorter symbols. No other functionality,
    except for that in the package itself and its dependencies, can be
    assumed. The exception to this rule is typechecking, such as in cases
    where additional processing should be performed for specific types,
    or when certain exceptions need to be handled. This is because checking
    for types, explicitly or for exception handling, do not cause fatal
    errors if the checked type is unavailable, i.e. the existence of the
    type itself is not required. This layer MUST remain completely agnostic
    of any particular use-case, environment, or application. Such package names
    MUST be suffixed with `-abstract`.

    #### Base Layer (optional)

    This level allows creation of concrete functionality, declaration of
    public methods, and concreate classes. However, this is done only
    for the purpose of saving time when coding concrete implementations
    that frequently use the most common functionality, such as interface
    (public) methods, or exceptions. Classes SHOULD be declared as
    `abstract` even if they have enough data or functionality to be
    instantiated. Such classes MUST be prefixed with the word "Base",
    in addition to the PSR naming conventions mentioned earlier in this
    document. This means that for a base class which excents
    `AbstractTranslator` and is designed as a base for a `Translator`
    class implementing `TranslatorInterface`, the base abstract class
    SHOULD be named `AbstractBaseTranslator`. One exception to this rule
    is for exception classes, i.e. those excending `Exception`. Such
    classes SHOULD be declared as concrete, and the name SHOULD have the
    concrete form, such as `class ValidationException extends
    AbstractValidationException implements ValidationExceptionInterface`.
    This level is the right place for factory methods which create exceptions,
    implementing `abstract` methods declared in the Abstract layer. This
    layer SHOULD remain agnostic of any use-case, environment, or application,
    consuming only the code declared either directly in the package,
    or in one of its concrete dependencies. All implementations SHOULD assume
    instance immutability. For cases where mutability is desired, an immutable
    implementation MUST be provided alongside. Such package names MUST be
    suffixed with `-base`.

    #### Concrete Layer

    This level SHOULD be completely or partly aware of its environment,
    use-case, or application. It MAY consume code that is not immediately
    a part of the package or one of its dependencies. This is particularly
    useful when creating extensions for frameworks and engines, and is
    allowed, provided that the documentation clearly states its intended
    application. Concrete implementations MUST contain all logic needed
    for instances to function properly. Abstract classes are allowed  in order
    to maintain SoC, but MUST NOT use their own public API, even for methods
    declared directly or inherited from base classes. Explicit use of DI
    container instances is allowed on this level, and on no other level.
    This is because all types and the concrete use-case is known here.
    There is no restriction with regard to repository/package names on this
    level, as long as they are descriptive and readable. However, these names
    SHOULD reflect the use-case of the package.

    #### Also

    Both the aim and a direct consequence of the described layer system results
    in a well defined, flexible, and very predictable class hierarchy tree.
    This tree follows these principles:

    -   If you are calling a public method, you MUST be consuming an interface.
    -   If you are declaring a concrete class, the use-case is known. This is a "leaf" class.
    -   You SHOULD NOT extend a concrete class. That class's API or internal
        behaviour is subject to change. Depending on it may result in the
        invalidation of the whole class branch.
    -   Instead, you SHOULD extend an abstract class. Abstract functionality
        is aimed to be as generic as possible, and is much less subject to
        change. Such abstract classes are "branch" classes.
    -   For each "leaf" class, there MUST be at least 1 "branch" class,
        and that "leaf" class MUST implement at least 1 interface.

9.  Transparent naming.

    Interfaces SHOULD be named to reflect the essence of the functionality
    which they aim to expose. Abstract class names SHOULD correlate with the
    names of the interfaces, which their descendants aim to implement.
    There is no restriction on the name of the concrete classes, as long as
    those names are descriptive and readable. Example:

    -   `interface TranslationCapableInterface` declares `public function translate()`.
    -   `interface TranslatorInterface extends TranslationCapableInterface`.
    -   `abstract class AbstractTranslator` declares `protected function _translate()`.
    -   `class Translator extends AbstractTranslator implements TranslatorInterface`
        declares `public function translate()`, which uses `_translate()`.

10. Adequate testing.

    Test as much code as possible, aiming for complete coverage. Test only the
    code in the repository containing the tests. Do not declare tests as "unit
    tests" unless you are absolutely sure that they test units in complete
    isolation. For test classes namespaces, use `Vendor\Package\FuncTest`
    and `Vendor\Package\UnitTest` base namespaces. Try to avoid declaring stub
    classes, but if you must, then use the `Vendor\Package\TestStub` base
    namespace. Test protected methods as well as public ones; if there is
    functionality, then it deserves a test. When testing getters and setters,
    do not test protected properties. Instead, test that the values returned
    by the getters are predicted by those set by the setters. Setters and
    getters are not required to read or write to any specific location, as long
    as value integrity is preserved. Remember: protected methods are an API
    too, but for descendants instead of external consumers.



[SemVer]:                   http://semver.org/
[PHP-FIG]:                  http://www.php-fig.org
[PSR-1]:                    http://www.php-fig.org/psr/psr-1/
[PSR-2]:                    http://www.php-fig.org/psr/psr-2/
[psr-2-properties]:         http://www.php-fig.org/psr/psr-2/#properties
[psr-naming-conventions]:   http://www.php-fig.org/bylaws/psr-naming-conventions/
[caret-operator]:           https://getcomposer.org/doc/articles/versions.md#caret
[Markdown]:                 https://daringfireball.net/projects/markdown/syntax
[keepachangelog.com]:       http://keepachangelog.com

[dhii/php-cs-fixer-config]: https://packagist.org/packages/dhii/php-cs-fixer-config

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
