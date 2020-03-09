# kirche.com Neos distribution "Genesis"

[![kirche.com Logo](https://github.com/kirchecom/site-genesis/blob/master/Resources/Public/Icons/favicon-32x32.png)](https://kirche.com)

kirche.com is powered by the Open Source Content Application Platform [Neos](https://www.neos.io).
This repository holds the Neos distribution specificly developed for kirche.com.
It can be used to set up platforms similar to kirche.com using your own content.

Note that this distribution already includes the [kirche.com site package](https://github.com/kirchecom/site-genesis),
so if you use this distribution, there is no need to manually install the site package.

## Installation

### 1. Requirements

Please ensure your web server meets the following requirements:

* PHP >= 7.2
* MySQL > 5.7.7 or MariaDB > 10.2.2
* [Composer](https://getcomposer.org/)
* One of the supported PHP graphic libraries: ImageMagick, GraphicsMagick, VIPS or GD2
* PHP modules mbstring, tokenizer and pdo_mysql
* Apache or Nginx

Depending on your system something similar to these commands might install the requirements:
```bash
sudo apt install mariadb-server mariadb-client
sudo apt install php-xml php-mbstring php-tokenizer php-mysql php-imagick
curl -sS https://getcomposer.org/installer | php
```

### 2. Create a database

Create a new empty database in a graphical database management system of your choice or using commands similar to
(replacing `YOURDATABASENAME` and `YOURPASSWORD` with values of your choice):

```bash
sudo mysql -u root
```
```mysql
create database YOURDATABASENAME;
grant all privileges on YOURDATABASENAME.* TO 'flow'@'localhost' identified by 'YOURPASSWORD';
flush privileges;
```

### 3. Install the Neos distribution

We recommend using composer to clone this repository and install all dependencies.
Replace `DIRECTORY` by your web server directory you want to install Neos into:

```bash
composer create-project --no-dev --keep-vcs kirchecom/neos-genesis DIRECTORY
```

If you are in a local development context (i.e. on a development machine) you can now enter the directory just created and
execute `./flow server:run` to start a local development server. Open the URL displayed to set up and configure your
Neos instance.

If you are in production context, please edit the file `Web/.htaccess` and uncomment the following line to set up
production context:

```
SetEnv FLOW_CONTEXT Production
```

Now open your web browser and visit the application's URL to configure your Neos installation.
Since it already includes the [kirche.com site package](https://github.com/kirchecom/site-genesis),
there is no need to install any site package.

### Installation Troubleshooting

On some systems you might need to adjust `Configuration/Settings.yaml` for Neos to run properly.
Below you find an example configuration that may solve some issues. Always adapt the file to your specific system!

```yaml
Neos:
  Flow:
    persistence:
      backendOptions:
        driver: pdo_mysql
        host: <REPLACE_WITH_YOUR_HOST>
        dbname: <REPLACE_WITH_YOUR_DB_NAME>
        user: <REPLACE_WITH_YOUR_DB_USER_NAME>
        password: <REPLACE_WITH_YOUR_DB_PASSWORD>
    core:
      phpBinaryPathAndFilename: /usr/local/bin/php
    resource:
      targets:
        localWebDirectoryPersistentResourcesTarget:
          target: Neos\Flow\ResourceManagement\Target\FileSystemSymlinkTarget
          targetOptions:
            relativeSymlinks: true
        localWebDirectoryStaticResourcesTarget:
          target: Neos\Flow\ResourceManagement\Target\FileSystemSymlinkTarget
          targetOptions:
            relativeSymlinks: true
  Imagine:
    driver: Gd
```

Specifically, setting `phpBinaryPathAndFilename` helps when Neos doesn't find your PHP interpreter.
Set it to the actual path on your system.

Configuring `relativeSymlinks: true` helps if you need relative symlinks for your assets.
Execute `./flow resource:publish` to update the asset paths.

If you ran into issues concerning the database charset, `./flow database:setcharset` helps.
Also `./flow flow:cache:flush --force`, `./flow flow:session:destroyAll` and `rm -rf Data/Temporary/*` can solve caching issues.

## Update

To update Neos, the [kirche.com site package](https://github.com/kirchecom/site-genesis) and other dependencies to
the latest minor version change, including security bugfixes, just switch to the directory where you installed Neos
and execute:

```bash
composer update --no-dev
```

To update the distribution to the latest version, which might include breaking changes, just fetch the latest
version of the repository:

```bash
git checkout master
git pull
composer install --no-dev
composer update --no-dev
```

After updating, database migrations might need to be applied:

```bash
./flow doctrine:migrationstatus
./flow doctrine:migrate
./flow node:migrationstatus
./flow node:migrate
./flow flow:cache:flush --force
./flow flow:session:destroyAll
./flow resource:publish
rm -rf Data/Temporary/*
```

## License

Neos is licensed under the
[GNU General Public License v3.0](https://github.com/neos/neos-development-collection/blob/master/LICENSE).
The [kirche.com site package](https://github.com/kirchecom/site-genesis) is licensed under the
[European Union Public License 1.2](https://github.com/kirchecom/site-genesis/blob/master/LICENSE.txt).
