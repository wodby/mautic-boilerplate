# Mautic boilerplate

Keep Mautic and your own plugins and themes in Git and deploy them with Wodby.

Use it with the [buildable Mautic](https://github.com/wodby/service-mautic-buildable)
service (`mautic-buildable`). For Mautic without your own plugins, where Wodby
keeps Mautic up to date for you, use the
[Mautic](https://github.com/wodby/service-mautic) service instead.

It is [Mautic's recommended Composer project](https://github.com/mautic/recommended-project)
for Mautic 7.2, with a `composer.lock`, so every build installs the same
versions. The lock file is resolved for PHP 8.2, the oldest PHP Mautic 7
supports.

## Build

Wodby builds the image from the repository: it runs `composer install`, which
installs Mautic into `docroot/`, copies Mautic's scaffold files, runs `npm ci`
and generates the assets, then adds Wodby's settings on top of
[`wodby/mautic-php`](https://github.com/wodby/mautic-php). To build locally,
install PHP 8.2 or newer, Composer and Node.js, then run `composer install`.

## Plugins and themes

Add plugins and themes with Composer, for example:

```shell
composer require mautic/helloworld-bundle
```

To commit a plugin or theme of your own instead, add it under
`docroot/plugins/` or `docroot/themes/` and re-include it in `.gitignore`, such
as `!/docroot/plugins/MyPluginBundle/`.

After every deployment Wodby runs Mautic's database migrations and
`bin/console mautic:plugins:reload`, so new plugins are installed and updated
ones are upgraded.

## Updates

Update Mautic with:

```shell
composer update "mautic/*" --with-all-dependencies
```

Commit `composer.lock` and deploy. You decide when to update, so watch
[Mautic's releases](https://github.com/mautic/mautic/releases) for security
fixes.

## Files

| Path | Purpose |
| --- | --- |
| `composer.json`, `composer.lock` | Mautic and the plugins and themes installed with Composer |
| `docroot/plugins/`, `docroot/themes/` | Plugins and themes committed to the repository |
| `.wodby/pipeline.yml` | The Wodby CI pipeline that builds and deploys the image |
| `.dockerignore` | Files left out of the build |

Don't commit `config/local.php`: Mautic writes its settings there, and Wodby
keeps it in the files volume. The build fails if the repository has one.
