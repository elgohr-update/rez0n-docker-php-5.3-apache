# PHP 5.3 Apache

PHP 5.3 [reached EOL](http://php.net/eol.php) on 14 Aug 2014 and thus, official docker support was [dropped](https://github.com/docker-library/php/pull/20). Use only for legacy projects that's can run only PHP 5.3

# How to use this image.

### Without a `Dockerfile`

If you don't want to include a `Dockerfile` in your project, it is sufficient to do the following:

    docker run -it --rm --name my_blog -v "$PWD":/var/www/html nibrev/php-5.3-apache

This image contains Debian's Apache httpd in conjunction with PHP (as `mod_php`) and uses `mpm_prefork` by default.

### Apache with a `Dockerfile`

```dockerfile
FROM nibrev/php-5.3-apache
COPY src/ /var/www/html/
```

Where `src/` is the directory containing all your PHP code. Then, run the commands to build and run the Docker image:

```console
$ docker build -t my-php-app .
$ docker run -d --name my-running-app my-php-app
```

We recommend that you add a `php.ini` configuration file; see the "Configuration" section for details.

### Apache without a `Dockerfile`

```console
$ docker run -d -p 80:80 --name my-apache-php-app -v "$PWD":/var/www/html nibrev/php-5.3-apache
```

### Changing `DocumentRoot` (or other Apache configuration)

Some applications may wish to change the default `DocumentRoot` in Apache (away from `/var/www/html`). The following demonstrates one way to do so using an environment variable (which can then be modified at container runtime as well):

```dockerfile
FROM nibrev/php-5.3-apache

ENV APACHE_DOCUMENT_ROOT /path/to/new/root

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
```

### Installing modules

To install additional modules use a `Dockerfile` like this:

``` Dockerfile
FROM nibrev/php-5.3-apache

# Installs curl
RUN docker-php-ext-install curl
```

Then build the image:

``` bash
$ docker build -t my-php .
```

## Configuration

This image ships with the default [`php.ini-development`](https://github.com/php/php-src/blob/master/php.ini-development) and [`php.ini-production`](https://github.com/php/php-src/blob/master/php.ini-production) configuration files.

It is *strongly* recommended to use the production config for images used in production environments!

The default config can be customized by copying configuration files into the `$PHP_INI_DIR/conf.d/` directory.

### php.ini change example

```dockerfile
FROM nibrev/php-5.3-apache

# Use the default production configuration
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
```


The default Apache2 virtual host configuration file is `/etc/apache2/sites-enabled/000-default.conf`, you can replace it by overwriting via Dockerfile or direct mounting external file.

### Mounting example

```console
$ docker run -d -p 80:80 --name my-apache-php-app -v "$PWD/vhost.conf":/etc/apache2/sites-enabled/000-default.conf nibrev/php-5.3-apache
```

# License

View [license information](http://php.net/license/) for the software contained in this image.
