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

Resources:
https://firebearstudio.com/blog/magento-2-git-and-deployment.html
http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html
