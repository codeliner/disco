# Bean parameters

Bean instances can be parametrized by a given configuration array. To
access configuration elements add both the ```@Parameters``` and the
```@Parameter``` annotation to your bean configuration method.

The ```@Parameters``` annotation acts as a collection holding one or more
```@Parameter``` annotations. The ```@Parameter``` annotation requires
at least a name which will be used as key to look for in the configuration
array.

In the following example the value of configuration key `test` gets passed
to the bean configuration method as method parameter. The configuration
parameters are passed in the same order as noted to the bean configuration
method:

```php
<?php

use bitExpert\Disco\Annotations\Bean;
use bitExpert\Disco\Annotations\Configuration;
use bitExpert\Disco\Helper\SampleService;

/**
 * @Configuration
 */
class MyConfiguration
{
    /**
     * @Bean
     * @Parameters({
     *  @Parameter({"name" = "test"})
     * })
     */
    public function mySampleService($test = '') : SampleService
    {
        $service = new SampleService();
        $service->setTest($test);
        return $service;
    }
}
```

The configuration array gets passed to the `\bitExpert\Disco\AnnotationBeanFactory`
instance as second constructor parameter:

```php
<?php

$parameters = ['test' => 'This is a test.'];

$beanFactory = new \bitExpert\Disco\AnnotationBeanFactory(MyConfiguration::class, $parameters);
\bitExpert\Disco\BeanFactoryRegistry::register($beanFactory);
```

## Default Parameter Values

Whenever a requested configuration key is not found - the key does not
exist or the value is null or empty - Disco will throw an exception. This
can be avoided by defining a default value that gets passed to the bean
configuration method instead:

```php
<?php

use bitExpert\Disco\Annotations\Bean;
use bitExpert\Disco\Annotations\Configuration;
use bitExpert\Disco\Helper\SampleService;

/**
 * @Configuration
 */
class MyConfiguration
{
    /**
     * @Bean
     * @Parameters({
     *  @Parameter({"name" = "test", "default" = "Some default value"})
     * })
     */
    public function mySampleService($test = '') : SampleService
    {
        $service = new SampleService();
        $service->setTest($test);
        return $service;
    }
}
```

## Default bean configuration method parameter values

There is no need to define a default parameter for the method parameters
but if you do not provide a default value your IDE might report a problem
when calling `$this->mySampleService()` without any method parameters.
That's why it is recommended to define a default parameter for each of the
bean configuration method parameters.

## Nested configuration array

This is possible to refer to nested configurations keys as defined like
this:

```php
<?php

$parameters = [
    'nested' => [
        'key' => 'This is a test.'
    ]
];

```

Use the '.' notation to access the nested elements:

```php
<?php

use bitExpert\Disco\Annotations\Bean;
use bitExpert\Disco\Annotations\Configuration;
use bitExpert\Disco\Helper\SampleService;

/**
 * @Configuration
 */
class MyConfiguration
{
    /**
     * @Bean
     * @Parameters({
     *  @Parameter({"name" = "nested.key"})
     * })
     */
    public function mySampleService($test = '') : SampleService
    {
        $service = new SampleService();
        $service->setTest($test);
        return $service;
    }
}
```

In this case the same rules apply for the default value handling: If one
of the nested elements is not found, the default value - if defined - gets
passed to bean creation method instead. If no default value was provided,
an exception gets thrown.
