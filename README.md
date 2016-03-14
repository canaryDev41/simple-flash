# Simple Flash Messages [![SensioLabsInsight](https://insight.sensiolabs.com/projects/64bbe2d0-055e-402a-8704-ea7dd6087b16/small.png)](https://insight.sensiolabs.com/projects/64bbe2d0-055e-402a-8704-ea7dd6087b16)

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Software License][ico-license]](LICENSE.md)
[![Build Status][ico-travis]][link-travis]
[![Coverage Status][ico-scrutinizer]][link-scrutinizer]
[![Quality Score][ico-code-quality]][link-code-quality]
[![Total Downloads][ico-downloads]][link-downloads]

Easy, framework agnostic flash notifications. Inspired by [laracasts/flash](https://github.com/laracasts/flash) and [plasticbrain/PHP-Flash-Messages](https://github.com/plasticbrain/PHP-Flash-Messages). It supports multiple CSS frameworks out of the box: 

* [Bootstrap 3](http://getbootstrap.com) [default style]
* [Bootstrap 4](http://v4-alpha.getbootstrap.com)
* [Foundation 5](http://foundation.zurb.com/sites/docs/v/5.5.3)
* [Foundation 6](http://foundation.zurb.com)
* [Semantic UI 2](http://semantic-ui.com)
* [UIKit 2](http://getuikit.com)

Demo: [http://demo.tamtamchika.net/simple-flash/examples/](http://demo.tamtamchika.net/simple-flash/examples/)

![simple-flash](https://dl.dropboxusercontent.com/u/1285445/pub/simple-flash.png)

## Install

Via [Composer](http://getcomposer.org).

```bash
$ composer require tamtamchik/simple-flash
```

Inside your project make sure to start a session and load [Composer](http://getcomposer.org) autoload to make everything work.

````php
<?php
// Start a Session
if( !session_id() ) @session_start();

// Initialize Composer Autoload
require_once 'vendor/autoload.php';
````

> **Warning!** This library contains global `flash()` function, that potentially can break your function with this name. Now you are warned!

## Usage

There are 3 ways to use library:

```php
use \Tamtamchik\SimpleFlash\Flash;

// Instance
$flash = new Flash();
$flash->message('Tea.');

// Static
Flash::message('Earl Gray.');

// Function
flash()->message('Hot!');
```

Messages added by calling `message($message, $type = 'info')` method. In case of calling a function `flash()` you can pass `$message, $type` just to function like so: `flash('resistance is futile')`.

Because any of creation types return `\Tamtamchik\SimpleFlash\Flash` instance, so you can always use chaining to add multiple messages. Shortcuts available for all types of base message types, also you can pass arrays as `$message`.

```php
flash()->error(['Invalid email!', 'Invalid username!'])
       ->warning('Warning message.')
       ->info('Info message.')
       ->success('Success message!');
```

Out of the box library support 4 different types of messages: `error`, `warning`, `info`, `success`. So far output is hard coded and designed for [Bootstrap](http://getbootstrap.com).

```html
<div class="alert alert-danger" role="alert">
  <p>Invalid email!</p>
  <p>Invalid username!</p>
</div>
<div class="alert alert-warning" role="alert"><p>Warning message.</p></div>
<div class="alert alert-info" role="alert"><p>Info message.</p></div>
<div class="alert alert-success" role="alert"><p>Success message!</p></div>
```

Rendering is simple:

```php
// Rendering specific type
$output = flash()->display('error');

// Rendering all flash
$output = flash()->display();

// Also rendering possible when you just read instance of \Tamtamchik\SimpleFlash\Flash object as a string
(string) flash(); 

// or ... it's totally just for display, never do this in real life...
<?php 
// ... some code 
$flash = new Flash();  
$flash->warning('It is totally just for display, never do this in real life...');
// ... some other code 
?>
<!-- ... some html -->
<div class="flashes">
  <?= $flash; ?>
</div>
<!-- ... some other html -->
```

## Templates

### Template Factory

Package comes with a set of templates for most popular CSS frameworks. [Bootstrap 3](http://getbootstrap.com), 
[Bootstrap 4](http://v4-alpha.getbootstrap.com), [Foundation 5](http://foundation.zurb.com/sites/docs/v/5.5.3),
[Foundation 6](http://foundation.zurb.com), [Semantic UI 2](http://semantic-ui.com), [UIKit 2](http://getuikit.com).

This templates can be created using [TemplateFactory](src/TemplateFactory.php) that comes with package.  
All templates have aliases defined in [Templates](src/Templates.php).

```php
use Tamtamchik\SimpleFlash\Flash;
use Tamtamchik\SimpleFlash\TemplateFactory;
use Tamtamchik\SimpleFlash\Templates;

// get template from factory, e.g. template for Foundation 6 
$template = TemplateFactory::create(Templates::FOUNDATION_6);

// passing template via function
flash('Info message using Foundation 6 template!', 'info', $template);

// passing to constructor
$flash = new Flash($template);

// using setTemplate function
$flash->setTemplate($template);
```

### Creating own templates

Template is basically any class that implements [TemplateInterface](src/TemplateInterface.php). But to make it easy 
you can extend [BaseTemplate](src/BaseTemplate.php), it contains most of the functions.

Defining and using this sample class as template:

```php
use Tamtamchik\SimpleFlash\BaseTemplate;
use Tamtamchik\SimpleFlash\TemplateInterface;

class CustomTemplate extends BaseTemplate implements TemplateInterface
{
    protected $prefix  = '<li>'; // every line prefix
    protected $postfix = '</li>'; // every postfix
    protected $wrapper = '<ul class="alert-%s">%s</ul>'; // wrapper over messages of same type
    
    /**
     * @param $messages - message text
     * @param $type     - message type: success, info, warning, error
     *
     * @return string
     */
    public function wrapMessages($messages, $type)
    {
        return sprintf($this->getWrapper(), $type, $messages);
    }
}

flash()
    ->setTemplate(new CustomTemplate)
    ->error(['Invalid email!', 'Invalid username!'])
    ->warning('Warning message.')
    ->info('Info message.')
    ->success('Success message!')
    ->display();
```

Will output following:

```html
<ul class="alert-error">
    <li>Invalid email!</li>
    <li>Invalid username!</li>
</ul>
<ul class="alert-warning">
    <li>Warning message.</li>
</ul>
<ul class="alert-info">
    <li>Info message.</li>
</ul>
<ul class="alert-success">
    <li>Success message!</li>
</ul>
```

## Interfaces

Package provide 2 main interfaces:

* `FlashInterface` - for `Flash` type of objects.
* `TemplateInterface` - for Simple Flash templates.

## Change log

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Testing

``` bash
$ composer tests
```

## Examples

``` bash
$ composer examples
```
And then just visit [http://localhost:8000](http://localhost:8000)

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) and [CONDUCT](CONDUCT.md) for details.

## Security

If you discover any security related issues, please email <yuri.tam.tkachenko@gmail.com> instead of using the issue tracker.

## Credits

- [Yuri Tkachenko][link-author]
- [All Contributors][link-contributors]

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[ico-version]: https://img.shields.io/packagist/v/tamtamchik/simple-flash.svg?style=flat-square
[ico-license]: https://img.shields.io/packagist/l/tamtamchik/simple-flash.svg?style=flat-square
[ico-travis]: https://img.shields.io/travis/tamtamchik/simple-flash/master.svg?style=flat-square
[ico-scrutinizer]: https://img.shields.io/scrutinizer/coverage/g/tamtamchik/simple-flash.svg?style=flat-square
[ico-code-quality]: https://img.shields.io/scrutinizer/g/tamtamchik/simple-flash.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/tamtamchik/simple-flash.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/tamtamchik/simple-flash
[link-travis]: https://travis-ci.org/tamtamchik/simple-flash
[link-scrutinizer]: https://scrutinizer-ci.com/g/tamtamchik/simple-flash/code-structure
[link-code-quality]: https://scrutinizer-ci.com/g/tamtamchik/simple-flash
[link-downloads]: https://packagist.org/packages/tamtamchik/simple-flash
[link-author]: https://github.com/tamtamchik
[link-contributors]: ../../contributors

[![Join the chat at https://gitter.im/tamtamchik/simple-flash](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/tamtamchik/simple-flash?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
