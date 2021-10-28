# Set New Relic Apdex T values on Multidev Creation #


[All sites on Pantheon include access to New Relic APM Pro](https://pantheon.io/features/new-relic). This application performance monitoring relies on the site owners setting time benchmark to measure against. Ideally, your Drupal or WordPress responds quickly to requests. And if the site is not responding quickly, New Relic can alert you.

The question is, where do you want to set the bar? By default, New Relic uses 0.5 as the target number of seconds. This value is called "T" in the [Apdex (Application Performance Index) formula](https://docs.newrelic.com/docs/apm/new-relic-apm/apdex/apdex-measuring-user-satisfaction).

In addition to monitoring how fast the server (Drupal or WordPress) respond, New Relic can monitor how fast real world browsers render your site. Browser performance is measured with the same Apdex formula. By default, New Relic uses a much more generous 7 seconds as the T value in browser Apdex.

We recommend that any team working on a site discuss expectations for server-side and client-side performance and set T values accordingly. As you are developing new features with [Pantheon Multidev,](https://pantheon.io/features/multidev-cloud-environments) you might even want the Multidev environments to have more stringent T values than Test or Live environments.

This Quicksilver example shows how you can set custom T values for Multidev environments when they are created. Otherwise each environment will use the default values of 0.5 and 7 for server and browser respectively.

To do the actual setting of default values this script first gets an API key and then uses that key to interact with New Relic's REST API to set a T values based on the existing values from the dev (or test/live) environment.

## Requirements

While these scripts can be downloaded individually, they are meant to work with Composer. See the installation in the next section.

- Quicksilver script projects and the script name itself should be consistent in naming convention.
- README should include a recommendation for types of hooks and stages that the script should run on.
  - For example, "This script should run on `clone_database` and the `after` stage.
  - Provide a snippet that can be pasted into the `pantheon.yml` file.

### Installation

You should [Activate New Relic Pro](https://pantheon.io/docs/new-relic/#activate-new-relic-pro) within your site dashboard. 

This project is designed to be included from a site's `composer.json` file, and placed in its appropriate installation directory by [Composer Installers](https://github.com/composer/installers).

In order for this to work, you should have the following in your composer.json file:

```json
{
  "require": {
    "composer/installers": "^1"
  },
  "extra": {
    "installer-paths": {
      "web/private/scripts/quicksilver": ["type:quicksilver-script"]
    }
  }
}
```

The project can be included by using the command:

`composer require pantheon-quicksilver/new-relic-apdex-t:^1`

If you are using one of the example PR workflow projects ([Drupal 8](https://www.github.com/pantheon-systems/example-drops-8-composer), [Drupal 9](https://www.github.com/pantheon-systems/drupal-project), [WordPress](https://www.github.com/pantheon-systems/example-wordpress-composer)) as a starting point for your site, these entries should already be present in your `composer.json`.

One gotcha is that this script cannot be the first or only script called as part of Multidev creation. Before the New Relic API recognizes the the Multidev environment, that environment needs to have received at least one previous request.

### Example `pantheon.yml`

Here's an example of what your `pantheon.yml` would look like if this were the only Quicksilver operation you wanted to use.

```yaml
api_version: 1

workflows:
  sync_code:
    after:
      - type: webphp
        description: Drush Config Import
        script: private/scripts/drush_config_import.php
      - type: webphp
        description: Set Apdex T values
        script: private/scripts/quicksilver/new-relic-apdex-t/new_relic_apdex_t.php
```
