# Want to develop a plugin and add it to the stable plugin list? Here are some standards that will make it even more awesome!

1. It must be covered with tests. Depending on what problem it solves, at least with one of these three tools - Behat, PHPSpec or PHPUnit.
2. It must have a description with an overview, installation guide, and contribution guide, take [CmsPlugin](https://github.com/bitbag-commerce/CmsPlugin) as an example.
3. It must be compatible with Sylius 1.0.0+
4. It must be tagged with a version
5. If it's based on SyliusPluginSkeleton, it must be named plugin. If not, it could be named bundle.
6. Obviously, it must follow composer standards and be published on packagist.
7. It must follow Sylius coding standards.
