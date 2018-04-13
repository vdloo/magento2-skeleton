# Magento 2 skeleton project

This is a bare install of Magento 2 using composer just to test things out so I can experiment with some deployment strategies. Project setup according to this [Magento 2 Git and Deployment](https://firebearstudio.com/blog/magento-2-git-and-deployment.html) article.

## Setting up a Magento 2 project

1. Create a new empty project directory, do not `git init` yet.

1. Get authentication keys for the Magento composer repo (yes, you need this):
http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html

1. Create ~/.composer/auth.json with these contents:

```
{
   "http-basic": {
      "repo.magento.com": {
         "username": "<the public key>",
         "password": "<the private key>"
      }
   }
}
```

1. Initialize the base installation in your project directory

```
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition .
```

1. Create a README.md

1. Expand on the `.gitignore`, for example replace it with [this one](https://github.com/magento/magento2-community-edition/blob/master/.gitignore) and add `/update` and `/phpserver`. Also see [this article](https://firebearstudio.com/blog/magento-2-git-and-deployment.html).

```
/.buildpath
/.cache
/.metadata
/.project
/.settings
atlassian*
/nbproject
/sitemap
/.idea
/.gitattributes

/app/code/Magento
/app/design/*/Magento
/app/etc
/app/i18n/magento
/app/*.*

/bin

/dev/shell

/dev/tests/*/framework
/dev/tests/*/testsuite/Magento
/dev/tests/*/tmp
/dev/tests/*/etc
/dev/tests/*/*.*
/dev/tests/*.*
/dev/tests/api-functional/config
/dev/tests/api-functional/_files/Magento
/dev/tests/js/JsTestDriver/framework
/dev/tests/js/JsTestDriver/testsuite/lib
/dev/tests/js/JsTestDriver/testsuite/mage
/dev/tests/js/JsTestDriver/*.*
/dev/tests/js/jasmine/assets
/dev/tests/js/jasmine/spec_runner
/dev/tests/js/jasmine/tests/app/code/Magento
/dev/tests/js/jasmine/tests/lib/mage
/dev/tests/js/jasmine/*.*
/dev/tests/performance
/dev/tests/functional/lib/Magento
/dev/tests/functional/tests/app/Magento
/dev/tests/functional/testsuites/Magento
/dev/tests/functional/utils

/dev/tools/Magento
/dev/tools/grunt
/dev/tools/*.*

/dev/*.*

/lib

/pub

/setup

/var

/vendor

/update

/phpserver

/*.*

!/composer.json
!/composer.lock
!/README.md
```

1. `git init` in your project dir

1. Add all files and commit the initial commit: `git add -A; git commit --verbose`

## Initial Magento 2 deployment (installation)

1. Create a releases directory somewhere (like `/data/web/releases`)

1. Clone or copy your revision to the release dir under a certain revision number

```
mkdir -p /data/web/releases/1/
cp -R ~/dev/magento2-skeleton /data/web/releases/1/
```

1. Create a 'current' symlink to the first revision. This will be updated later with new revisions.

```
cd /data/web/releases
ln -s 1 current
```

1. Symlink the release dir to your webroot

```
ln -s /data/web/releases/current/magento2-skeleton/pub/ /data/web/public
```

1. Create a new database

```
mysql> create database magento2skeleton;
Query OK, 1 row affected (0.02 sec)
```

1. Perform the initial installation

```
cd /data/web/releases/current/magento2-skeleton
./bin/magento setup:install --db-host=mysqlmaster --db-name=magento2skeleton --db-user=app --db-password=<YOUR DB PWD, SEE /data/web/.my.cnf> --backend-frontname=admin --base-url=http://<YOURHYPERNODENAME>.hypernode.io --language=en_US --timezone=Europe/Amsterdam --currency=EUR --admin-lastname=Admin --admin-firstname=Admin --admin-email=admin@example.com --admin-user=admin --admin-password=<A GOOD PASSWORD> --cleanup-database --use-rewrites=1
```

1. Add the indexer cron

```
echo "* * * * * flock -n ~/.cron.lock php /data/web/releases/current/magento2-skeleton/bin/magento cron:run" | crontab -
```

## Deploying changes

1. In your new release, make sure you have run `composer update` and commited the `composer.lock` file if it has changed.

Composer update installs all latest available module versions. The `composer.lock` file has to be commited as part of your revision. This is because on staging / production you want to install exactly the same module versions every time for a revision.

1. Clone or copy the new revision to `/data/releases/<NUMBER>/`

1. Composer install to update to get the modules for this revision

```
cd /data/releases/<NUMBER>/magento2-skeleton
composer install
```

1. Unlink the current symlink `unlink /data/releases/current`. Note that from this step on there will be downtime.

1. Link the new checkout `ln -s /data/web/releases/2 /data/web/releases/current`

1. Upgrade the installation

```
cd /data/web/releases/current/magento2-skeleton
./bin/magento setup:upgrade
./bin/magento setup:di:compile
./bin/magento setup:static-content:deploy -f
```

Resources:
https://firebearstudio.com/blog/magento-2-git-and-deployment.html
http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html

