# PHP Code Standards
Packages complying with the Dhii standard for PHP code must follow the below
rules:

1.  Maintain complete compatibility with the [PSR-1] and [PSR-2] standards.

    This is done partly by relying on the [dhii/php-cs-fixer-config] package.
    However, because the term [used][psr-2-properties] in relation to the
    underscore prefix for private/protected methods is "SHOULD NOT", this prefix.
    MAY be used. This is encouraged, and lets us avoid any potential conflicts
    in method names, which is especially important given our layered
    architecture.

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

    It is important that consumers can at a glance understand what change
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




[SemVer]:                   http://semver.org/
[PHP-FIG]:                  http://www.php-fig.org
[PSR-1]:                    http://www.php-fig.org/psr/psr-1/
[PSR-2]:                    http://www.php-fig.org/psr/psr-2/
[psr-2-properties]:         http://www.php-fig.org/psr/psr-2/#properties
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
