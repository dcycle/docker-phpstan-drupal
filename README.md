[![CircleCI](https://circleci.com/gh/dcycle/docker-phpstan-drupal.svg?style=svg)](https://circleci.com/gh/dcycle/docker-phpstan-drupal)

Perform static analysis of Drupal PHP code with [PHPStan](https://github.com/phpstan/phpstan) and [PHPStan-Drupal](https://github.com/mglaman/phpstan-drupal).

For example:

    docker run --rm \
      -v $(pwd)/example01/modules_i_want_to_test:/var/www/html/modules/custom \
      dcycle/phpstan-drupal:1 /var/www/html/modules/custom
    docker run --rm \
      -v $(pwd)/example02/modules_i_want_to_test:/var/www/html/modules/custom \
      dcycle/phpstan-drupal:1 /var/www/html/modules/custom
    docker run --rm \
      -v $(pwd)/example03/modules_i_want_to_test:/var/www/html/modules/custom \
      -v $(pwd)/example03/phpstan-drupal:/phpstan-drupal \
      dcycle/phpstan-drupal:1 /var/www/html/modules/custom
    docker run --rm \
      -v $(pwd)/example04/some_module:/var/www/html/modules/custom/some_module \
      -v $(pwd)/example04/phpstan-drupal:/phpstan-drupal \
      dcycle/phpstan-drupal:1 /var/www/html/modules/custom

Ignoring a single line of code
-----

At the time of this writing, PHPStan by itself does not support ignoring an error on a single line. We used an adapted solution from [@kolibabchuk](https://github.com/kolibabchuk) [documented here](https://github.com/phpstan/phpstan/issues/786#issuecomment-532865549), so using this Dockerized version of PHPStan-Drupal, you can ignore a single line using, for example:

    // phpstan:ignoreError
    return \Drupal::cache()->get(Unicode::strtolower($something));

or even:

    // Whatever whatever phpstan:ignoreError whatever whatever.
    return \Drupal::cache()->get(Unicode::strtolower($something));

Obviously, we generally want to fix the underlying problem, but if for whatever reason you need to ignore an error, you can now do so.

See [this project on the Docker Hub](https://hub.docker.com/r/dcycle/phpstan-drupal/).

Custom config file
-----

If you need a custom config file, for example if you want a different level, or to tell PHPStan to ignore certain files, you can do so by including the provided config file. See [example05](https://github.com/dcycle/docker-phpstan-drupal/tree/master/example05) for details; it can be run using:

    docker run --rm \
      -v $(pwd)/example05/modules_i_want_to_test:/var/www/html/modules/custom \
      dcycle/phpstan-drupal:1 /var/www/html/modules/custom \
      -c /var/www/html/modules/custom/phpstan.neon

If you look at the [custom config file in example05](https://github.com/dcycle/docker-phpstan-drupal/blob/master/example05/modules_i_want_to_test/phpstan.neon), it looks like this:

    # See https://github.com/dcycle/docker-phpstan-drupal/blob/master/README.md#custom-config-file
    parameters:
      excludes_analyse:
        - */tests/*
    includes:
      - /var/www/html/phpstan.neon

This tells PHPStan that we want our custom configuration exclude paths like `*/tests/*` from PHPStan analysis, and use the default `/var/www/html/phpstan.neon` for everything else. `/var/www/html/phpstan.neon` is not in your own code, it is in the dcycle/phpstan-drupal container. Its contents can be found [here](https://github.com/dcycle/docker-phpstan-drupal/blob/master/docker-resources/phpstan.neon).
