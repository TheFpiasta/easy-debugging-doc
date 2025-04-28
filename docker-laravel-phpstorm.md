# Debugging in PHPStorm with Laravel in a Docker Compose environment

<!-- TOC -->
* [Debugging in PHPStorm with Laravel in a Docker Compose environment](#debugging-in-phpstorm-with-laravel-in-a-docker-compose-environment)
  * [Configure Docker-Compose](#configure-docker-compose)
  * [Configure Dockerfile](#configure-dockerfile)
  * [Configure php.ini](#configure-phpini)
  * [Configure PHPStorm](#configure-phpstorm)
  * [Troubleshooting](#troubleshooting)
<!-- TOC -->

You have a new Laravel project in a docker compose environment and want to debug your PHP code? But again you are faced with the problem that you no longer know how the heck to set this up with PHPStorm? The internet is full of solutions and tutorials, but they don't really work? Here's my simple step-by-step solution on how I quickly set up debugging.

## Configure Docker-Compose

In your docker-compose.yml file, you need to add the `extra_hosts` in the service where Laravel and Xdebug is running:

```yaml
services:
  php:
    extra_hosts: # add this to the service
      - host.docker.internal:host-gateway
```

## Configure Dockerfile

Next, you have to enable Xdebug in your Dockerfile. You can do this by adding the following lines to your Dockerfile:

```dockerfile
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host = host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
```

## Configure php.ini

You also need to add the following lines to your php.ini file:

```ini
zend_extension=xdebug.so

[xdebug]
xdebug.mode=develop,debug
xdebug.client_host=host.docker.internal
xdebug.start_with_request=yes
; The following configuration is optional I think
xdebug.profiler_output_name = cachegrind.out.%p
xdebug.output_dir = /var/log/php/
xdebug.profiler_append = 0
xdebug.use_compression = true
```

## Configure PHPStorm

Now we can restart the Docker container and configure PHPStorm.

First, enable the Debug listener (the bug icon in the top right corner of PHPStorm) (in older versions it was the phone icon)

Now you should automatically get an incoming debugging connection window, witch request you to configure the server mapping.
If you have mapped the server correctly, you should now ready to debug your code.



## Troubleshooting

- If you xdebug use a different port then the default 9003 port in the php.ini, make sure you have add this port to PHP Storm (PHP > Debug > Debug Port).
- Make sure you have chosen the correct PHP CLI interpreter in PHPStorm (PHP > CLI Interpreter). This should be the CLI Interpreter of the docker container.
- Check the server Mapping (PHP > Servers). They should be like: `Host: _`, `Port: 80`, `Debugger: Xdebug`, `Use path mappings: checked`. The path mapping should be like: `LOCAL DIR` to `DOCKER DIR`.
- If the popup for server configuration don't come, try to delete all existing server configurations.
