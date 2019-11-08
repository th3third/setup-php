<p align="center">
  <a href="https://github.com/marketplace/actions/setup-php-action" target="_blank">
    <img src="https://repository-images.githubusercontent.com/206578964/e0a18480-dc65-11e9-8dd3-b9ffbf5575fe" alt="Setup PHP in GitHub Actions" width="400">
  </a>
</p>

<h1 align="center">Setup PHP in GitHub Actions</h1>

<p align="center">
  <a href="https://github.com/shivammathur/setup-php" title="GitHub action to setup PHP"><img alt="GitHub Actions status" src="https://github.com/shivammathur/setup-php/workflows/Main%20workflow/badge.svg"></a>
  <a href="https://codecov.io/gh/shivammathur/setup-php" title="Code coverage"><img alt="Codecov Code Coverage" src="https://codecov.io/gh/shivammathur/setup-php/branch/master/graph/badge.svg"></a>
  <a href="https://github.com/shivammathur/setup-php/blob/master/LICENSE" title="license"><img alt="LICENSE" src="https://img.shields.io/badge/license-MIT-428f7e.svg"></a>
  <a href="#tada-php-support" title="PHP Versions Supported"><img alt="PHP Versions Supported" src="https://img.shields.io/badge/php-%3E%3D%205.6-8892BF.svg"></a> 
  <a href="https://www.patreon.com/shivammathur" title="Support Shivam Mathur on Patreon"><img alt="Support me on Patreon" src="https://shivammathur.com/badges/patreon.svg"></a>   <a href="https://www.paypal.me/shivammathur"><img alt="Support me on PayPal" src="https://shivammathur.com/badges/paypal.svg"></a>
  <a href="https://www.codementor.io/shivammathur?utm_source=github&utm_medium=button&utm_term=shivammathur&utm_campaign=github" title="Contact Shivam Mathur on Codementor"><img alt="Contact me on Codementor" src="https://cdn.codementor.io/badges/contact_me_github.svg"></a>  
</p>

Setup PHP with required extensions, php.ini configuration and composer in [GitHub Actions](https://github.com/features/actions "GitHub Actions"). This action can be added as a step in your action workflow and it will setup the PHP environment you need to test your application. Refer to [Usage](#memo-usage "How to use this") section and [examples](#examples "Examples of use") to see how to use this.

## Contents

- [PHP Support](#tada-php-support)
- [OS/Platform Support](#cloud-osplatform-support)
- [PHP Extension Support](#wrench-php-extension-support)
- [Coverage support](#signal_strength-coverage-support)
  - [Xdebug](#xdebug)
  - [PCOV](#pcov)
  - [Disable coverage](#disable-coverage)
- [Usage](#memo-usage)
  - [Basic Usage](#basic-usage)
  - [Matrix Testing](#matrix-testing)
  - [Cache dependencies](#cache-dependencies)
  - [Examples](#examples)
- [License](#scroll-license)
- [Contributions](#1-contributions)
- [Support this project](#sparkling_heart-support-this-project)
- [This action uses the following works](#bookmark-this-action-uses-the-following-works)
- [Further Reading](#bookmark_tabs-further-reading)

## :tada: PHP Support

|PHP Version|Stability|Release Support|
|--- |--- |--- |
|5.6|`Stable`|`End of life`|
|7.0|`Stable`|`End of life`|
|7.1|`Stable`|`Security fixes only`|
|7.2|`Stable`|`Active`|
|7.3|`Stable`|`Active`|
|7.4|`RC4`/`RC5`|`Active`|

**Note:** PHP 7.4 is currently in development, do not use in production/release branches.

## :cloud: OS/Platform Support

|Virtual environment|matrix.operating-system|
|--- |--- |
|Windows Server 2019|`windows-latest` or `windows-2019`|
|Ubuntu 18.04|`ubuntu-latest` or `ubuntu-18.04`|
|Ubuntu 16.04|`ubuntu-16.04`|
|macOS X Catalina 10.15|`macOS-latest` or `macOS-10.15`|

## :wrench: PHP Extension Support
- On `ubuntu` extensions which have the package in `APT` are installed. If extension is not in `APT`, you can use `PECL` to install the extension as fallback by specifying `pecl: true`. 
- On `windows` extensions which have `windows` binary on `PECL` can be installed.
- On `macOS` extensions which are on `PECL` can be installed.
- Extensions which are installed along with PHP if specified are enabled.
- Extensions which cannot be installed gracefully leave an error message in the logs, the action is not interrupted.

## :signal_strength: Coverage support

### Xdebug

Specify `coverage: xdebug` to use `Xdebug`.  
Runs on all [PHP versions supported](#tada-php-support "List of PHP versions supported on this GitHub Action")    

```yaml
uses: shivammathur/setup-php@master
with:
  php-version: '7.3'  
  coverage: xdebug
```

### PCOV

Specify `coverage: pcov` to use `PCOV`.  
It is much faster than `Xdebug`.  
`PCOV` needs `PHP >= 7.1`.  
If your source code directory is other than `src`, `lib` or, `app`, specify `pcov.directory` using the `ini-values-csv` input.  


```yaml
uses: shivammathur/setup-php@master
with:
  php-version: '7.3'
  ini-values-csv: pcov.directory=api #optional, see above for usage.
  coverage: pcov
```

### Disable coverage

Specify `coverage: none` to disable both `Xdebug` and `PCOV`.
Consider disabling the coverage using this PHP action for these reasons.

- You are not generating coverage reports while testing.
- It will disable `Xdebug`, which will have a positive impact on PHP performance.
- You are using `phpdbg` for running your tests.

```yaml
uses: shivammathur/setup-php@master
with:
  php-version: '7.3'
  coverage: none
```

## :memo: Usage

Inputs supported by this GitHub Action.

- php-version `required`
- extension-csv `optional`
- ini-values-csv `optional`
- coverage `optional`
- pecl `optional`

See [action.yml](action.yml "Metadata for this GitHub Action") and usage below for more info.

### Basic Usage

```yaml
steps:
- name: Checkout
  uses: actions/checkout@master
- name: Setup PHP
  uses: shivammathur/setup-php@master
  with:
    php-version: '7.3'
    extension-csv: mbstring, xdebug #optional, setup extensions
    ini-values-csv: post_max_size=256M, short_open_tag=On #optional, setup php.ini configuration
    coverage: xdebug #optional, setup coverage driver
    pecl: true #optional, setup PECL
- name: Check PHP Version
  run: php -v
- name: Check Composer Version
  run: composer -V
- name: Check PHP Extensions
  run: php -m
```

### Matrix Testing

```yaml
jobs:
  run:    
    runs-on: ${{ matrix.operating-system }}
    strategy:
      max-parallel: 15
      matrix:
        operating-system: [ubuntu-latest, windows-latest, macOS-latest]
        php-versions: ['5.6', '7.0', '7.1', '7.2', '7.3']
    name: PHP ${{ matrix.php-versions }} Test on ${{ matrix.operating-system }}
    steps:
    - name: Checkout
      uses: actions/checkout@master
    - name: Setup PHP
      uses: shivammathur/setup-php@master
      with:
        php-version: ${{ matrix.php-versions }}
        extension-csv: mbstring, xdebug #optional, setup extensions
        ini-values-csv: post_max_size=256M, short_open_tag=On #optional, setup php.ini configuration
        coverage: xdebug #optional, setup coverage driver
        pecl: true #optional, setup PECL
    - name: Check PHP Version
      run: php -v
    - name: Check Composer Version
      run: composer -V
    - name: Check PHP Extensions
      run: php -m
```

### Cache dependencies

You can persist composer's internal cache directory using the [`action/cache`](https://github.com/actions/cache) GitHub Action. Dependencies cached are loaded directly instead of downloading them while installation. The files cached are available across check-runs and will reduce the workflow execution time.

**Note:** Please do not cache `vendor` directory using `action/cache` as that will have side-effects.

```yaml
- name: Get Composer Cache Directory
  id: composer-cache
  run: echo "::set-output name=dir::$(composer config cache-files-dir)"

- name: Cache on linux and macOS
  if: runner.os != 'Windows'
  uses: actions/cache@v1
  with:
    path: ${{ steps.composer-cache.outputs.dir }}
    key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
    restore-keys: ${{ runner.os }}-composer-

- name: Cache on windows
  if: runner.os == 'Windows'
  uses: actions/cache@v1
  with:
    path: ${{ steps.composer-cache.outputs.dir }}
    key: ${{ runner.os }}-composer-${{ hashFiles('**composer.lock') }}
    restore-keys: ${{ runner.os }}-composer-

- name: Install Dependencies
  run: composer install --prefer-dist
```

### Examples

Examples for setting up this GitHub Action with different PHP Frameworks/Packages.

|Framework/Package|Runs on|Workflow|
|--- |--- |--- |
|CodeIgniter|`macOS`, `ubuntu` and `windows`|[codeigniter.yml](./examples/codeigniter.yml "GitHub Action for CodeIgniter")|
|Laravel with `MySQL` and `Redis`|`ubuntu`|[laravel-mysql.yml](./examples/laravel-mysql.yml "GitHub Action for Laravel with MySQL and Redis")|
|Laravel with `PostgreSQL` and `Redis`|`ubuntu`|[laravel-postgres.yml](./examples/laravel-postgres.yml "GitHub Action for Laravel with PostgreSQL and Redis")|
|Laravel without services|`macOS`, `ubuntu` and `windows`|[laravel.yml](./examples/laravel.yml "GitHub Action for Laravel without services")|
|Lumen with `MySQL` and `Redis`|`ubuntu`|[lumen-mysql.yml](./examples/lumen-mysql.yml "GitHub Action for Lumen with MySQL and Redis")|
|Lumen with `PostgreSQL` and `Redis`|`ubuntu`|[lumen-postgres.yml](./examples/lumen-postgres.yml "GitHub Action for Lumen with PostgreSQL and Redis")|
|Lumen without services|`macOS`, `ubuntu` and `windows`|[lumen.yml](./examples/lumen.yml "GitHub Action for Lumen without services")|
|Phalcon with `MySQL`|`ubuntu`|[phalcon-mysql.yml](./examples/phalcon-mysql.yml "GitHub Action for Phalcon with MySQL")|
|Phalcon with `PostgreSQL`|`ubuntu`|[phalcon-postgres.yml](./examples/phalcon-postgres.yml "GitHub Action for Phalcon with PostgreSQL")|
|Slim Framework|`macOS`, `ubuntu` and `windows`|[slim-framework.yml](./examples/slim-framework.yml "GitHub Action for Slim Framework")|
|Symfony with `MySQL`|`ubuntu`|[symfony-mysql.yml](./examples/symfony-mysql.yml "GitHub Action for Symfony with MySQL")|
|Symfony with `PostgreSQL`|`ubuntu`|[symfony-postgres.yml](./examples/symfony-postgres.yml "GitHub Action for Symfony with PostgreSQL")|
|Yii2 Starter Kit with `MySQL`|`ubuntu`|[yii2-mysql.yml](./examples/yii2-mysql.yml "GitHub Action for Yii2 Starter Kit with MySQL")|
|Yii2 Starter Kit with `PostgreSQL`|`ubuntu`|[yii2-postgres.yml](./examples/yii2-postgres.yml "GitHub Action for Yii2 Starter Kit with PostgreSQL")|
|Zend Framework|`macOS`, `ubuntu` and `windows`|[zend-framework.yml](./examples/zend-framework.yml "GitHub Action for Zend Framework")|

## :scroll: License

The scripts and documentation in this project are released under the [MIT License](LICENSE "License for shivammathur/setup-php"). This project has multiple [dependencies](https://github.com/shivammathur/setup-php/network/dependencies "Dependencies for this PHP Action") and their licenses can be found in their respective repositories.

## :+1: Contributions

Contributions are welcome! See [Contributor's Guide](.github/CONTRIBUTING.md "shivammathur/setup-php contribution guide"). If you face any issues while using this or want to suggest a feature/improvement, create an issue [here](https://github.com/shivammathur/setup-php/issues "Issues reported").

## :sparkling_heart: Support this project

If this action helped you.

- Please star the project and share it, this helps reach more people.
- If you blog, write about your experience using this.
- Support on this project on <a href="https://www.patreon.com/shivammathur"><img alt="Patreon" src="https://shivammathur.com/badges/patreon.svg"></a> or using <a href="https://www.paypal.me/shivammathur"><img alt="Paypal" src="https://shivammathur.com/badges/paypal.svg"></a>.

## :bookmark: This action uses the following works

- [powershell-phpmanager](https://github.com/mlocati/powershell-phpmanager "Package to handle PHP on windows")
- [Homebrew](https://brew.sh/ "MacOS package manager")
- [ppa:ondrej/php](https://launchpad.net/~ondrej/+archive/ubuntu/php "Pre-compiled ubuntu packages")
- [exolnet/homebrew-deprecated](https://github.com/eXolnet/homebrew-deprecated "Pre-compiled deprecated PHP for macOS")
- [phpbrew](https://github.com/phpbrew/phpbrew "PHP packages manager")

## :bookmark_tabs: Further Reading

- [About GitHub Actions](https://github.com/features/actions "GitHub Actions")
- [GitHub Actions Syntax](https://help.github.com/en/articles/workflow-syntax-for-github-actions "GitHub Actions Syntax")
- [Other Awesome Actions](https://github.com/sdras/awesome-actions "List of Awesome GitHub Actions")
