# drupal-contrib-ddev
A ready made setup to run and write all kinds of tests for Drupal contrib modules

## Usage

1. Clone this repository
2. Run `ddev start`
2. Run `ddev composer install`
3. You can now require any contrib module you want to test with `ddev composer require drupal/<module_name>`. For example `ddev composer require drupal/gutenberg`

## Running tests

You don't need to install Drupal or anything like that to run tests. The tests will install their own Drupal.

### PHPUnit

To run the tests of a specific module, you can just to something like this (example):

```
ddev exec phpunit web/modules/contrib/gutenberg
```

### FunctionalJavascript tests with a browser

This project comes with a preconfigured container for selenium and Chrome. You should be able to visit the VNC part of the chrome container at something like [http://drupal-contrib-ddev.ddev.site:7910/](http://drupal-contrib-ddev.ddev.site:7910/). The password is `secret`.

When run tests, you should now be able to see what's happening inside the browser.

You can probably use your imagination to figure out how to debug these tests, for example with xdebug. Here is one such example, including illustrating opening dev tools for easier debugging inside the browser. The command used in the GIF is this:

```
# First SSH into the container
$ ddev ssh
$ ./vendor/bin/phpunit web/modules/contrib/gutenberg/tests/src/FunctionalJavascript/UpdateAddMediaTest.php
```

![GIF of running tests](https://github.com/frontkom/drupal-contrib-ddev/blob/main/testdebug.gif)

### FunctionalJavascript tests with your own OS browser

At least on Mac and Linux this should be possible, and personally I prefer it. The examples below is for Ubuntu Linux, but should be similar on Mac. You may or may not have to replace `host.docker.internal` with `docker.for.mac.host.internal` on Mac.


To do this, you need to install chromedriver on your host machine. You can do that with npm. So run this, for example in a new terminal window:

```
$ npm install chromedriver
```

Then let's start that, and keep that running in one terminal window:

```
$ ./node_modules/.bin/chromedriver --port=8643 --url-base=wd/hub --allowed-ips= --allowed-origins='host.docker.internal'
```

Then you need to configure your tests to use this driver. We do this on a per-session basis, inside the container with ssh:

```
$ ddev ssh
$ export SIMPLETEST_BASE_URL=http://drupal-contrib-ddev.ddev.site:12780
$ export MINK_DRIVER_ARGS_WEBDRIVER='["chrome", {"browserName":"chrome","chromeOptions":{"w3c":false,"args":["--disable-gpu","--no-sandbox", "--disable-dev-shm-usage"]}}, "http://host.docker.internal:8643/wd/hub"]'
```

Then you can run the tests as usual. To use the same example as above:

```
$ ./vendor/bin/phpunit web/modules/contrib/gutenberg/tests/src/FunctionalJavascript/UpdateAddMediaTest.php
```

You should now see your OS browser open and run the tests. Which is more convenient, and you can interact with the inspector like you are used to, and so on.
