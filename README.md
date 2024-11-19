# easy-debugging-doc

<!-- TOC -->
* [easy-debugging-doc](#easy-debugging-doc)
  * [Starter Setup](#starter-setup)
  * [Tutorial](#tutorial)
    * [Enable debugging in vagrant](#enable-debugging-in-vagrant)
    * [Setup PHPStorm](#setup-phpstorm)
      * [Add a new Debug Configuration](#add-a-new-debug-configuration)
      * [Select or Add a new Web Server](#select-or-add-a-new-web-server)
    * [Grand finale](#grand-finale)
  * [Troubleshooting](#troubleshooting)
    * [Brakepoints dont breake](#brakepoints-dont-breake)
<!-- TOC -->

<!-- How to enable Debugging with PHP (Laravel), PHPStorm and vagrant (Homestead)? -->

You have a new Laravel project in a Homestead Vagrant box and want to debug your PHP code? But again you are faced with
the problem that you no longer know how the heck to set this up with PHPStorm? The internet is full of solutions and
tutorials, but they don't really work?
Here's my simple step-by-step solution on how I quickly set up debugging.

## Starter Setup

- running PHPStorm with our Project
- running Laravel Homestead Vagrant box with our Project

## Tutorial

In this Tutorial we will enable Xdebug in vagrant box and setup PHPStorm to debug your PHP code in a Laravel Project.

### Enable debugging in vagrant

First connect to your Vagrant box via a terminal. Open a terminal and go to the folder in which your Homestead.yaml is
located. Run there `vagrant ssh` to connect to the vagrant box.

For debugging, I use Xdebug. If you are in the vagrant box, open the xdebug.ini file. In the example I am using PHP
version 8.2.x. If you are using a different version, adjust "8.2" in the command according to your version.

````shell
sudo nano /etc/php/8.2/mods-available/xdebug.ini
````

I don't know if all config parameters are needed, but this is what my config looks like: (never touch a running
system :D)

````text
zend_extension = xdebug.so
xdebug.mode = debug
xdebug.discover_client_host = true
xdebug.client_port = 9003
xdebug.max_nesting_level = 512
xdebug.mode = debug
xdebug.discover_client_host = true
xdebug.client_port = 9003
xdebug.max_nesting_level = 512

xdebug.remote_enable = on
xdebug.remote_port = 9000
xdebug.remote_connect_back = on
xdebug.idekey = PHPSTORM
xdebug.show_error_trace = 1
xdebug.remote_autostart = 0
````

When you have customized the config, close nano with ctrl+x and confirm with y that you want to save. Now we have to
restart a few services for the changes to take effect. Customize ``php8.2-fpm.service`` with the PHP version you are
using.

````shell
sudo systemctl restart php8.2-fpm.service nginx.service
````

Well done! You have now completed all preparations in the vagrant box and Xdebug is activated. Now we can continue in
PHPStorm.

### Setup PHPStorm

<!-- (Build #PS-232.10227.13, built on November 18, 2023) -->

In this tutorial I use PhpStorm 2023.2.4 with the "new UI". In other versions, the flow can be changed a little.

#### Add a new Debug Configuration

Go to Edit Configurations ``Run > Edit Configurations`` in the Main Menu or
over ``Run / Debug Configurations > Edit Configurations`` in the top right section of PHPStorm.

Now add a new configuration with ``Alt+Enter`` or over the "+" button on the window and select ``PHP Web Page`` from the
dropdown.

Name the configuration like ``debug``. In general your want to check HTTPS and set the start URL to "/". At last, we
need to select or configure a Server.

#### Select or Add a new Web Server

If you have already created a web server configuration for the vagrant box, select it in the dropdown next to Server. If
you have not already done so, click on the three dots next to the dropdown selection.

In the new opened window add a new Server configuration by pressing ``insert`` or click the "+" button on the window.

Name the Server like ``vagrant``. Set the Host to the site your configured in your ``Homestead.yaml`` file
unter ``sites > map`` (like hello-world.test).

In general your want to use the port 80.

Now select Xdebug on the Debugger dropdown.

To avoid mapping problems you should use path mappings. Map the following two folders:

Use for project_path the absolute path to your project root (like ``C:\projects\halloWorld``).
Use for server_path the absolute path to your project root on the server (like ``/home/vagrant/halloWorld``).

In general, you can look up both in your ``Homestead.yaml`` under ``folders``.

| Directory           | Absolute path on the server |
|---------------------|-----------------------------|
| project_path        | server_path                 |
| project_path\public | server_path/public          |

Now you can save the settings and close the Servers window.

### Grand finale

If you have selected our existing or new created server in the ``Run / Debug Configurations`` window, you can save the
new ``PHP WEb Page`` configuration and close the window.

Congratulations! You have now a working Debugger! :D

Add a breakpoint and start the new debugging configuration in ``Debugg`` mode. Don't forget to activate the debugging listener. :P

Happy Debugging!

## Troubleshooting

### Breakpoints don't break

- Try to install the [Xdebug helper](https://chromewebstore.google.com/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc) extension in your browser.
- Check, if the browser don't block the page Cookies (blocking third para cookies is on most cases ok). The debugging session will be handled by page cookies.

---

~ Debugging

1. Being the detective in a crime movie where you are also the murderer.
