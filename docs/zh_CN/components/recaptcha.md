# ReCaptcha

The Google recaptcha component for Hyperf.

## 安装

```shell
composer require friendsofhyperf/recaptcha
```

## 使用

- 定义中间件

```php
namespace App\Middleware;

use FriendsOfHyperf\ReCaptcha\Middleware\ReCaptchaMiddleware;

class V3CaptchaMiddleware extends ReCaptchaMiddleware
{
    protected string $version = 'v3';
    protected string $action = 'register'; 
    protected float $score = 0.35; 
    protected string $hostname; 
}

class V2CaptchaMiddleware extends ReCaptchaMiddleware
{
    protected string $version = 'v2';
    protected string $action = 'register'; 
    protected float $score = 0.35; 
    protected string $hostname; 
}
```

- 验证器使用

```php
<?php

namespace App\Controller;

use Hyperf\Di\Annotation\Inject;
use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\Validation\Contract\ValidatorFactoryInterface;

class IndexController
{
    #[Inject]
    protected ValidatorFactoryInterface $validationFactory;

    public function foo(RequestInterface $request)
    {
        $validator = $this->validationFactory->make(
            $request->all(),
            [
                'g-recaptcha' => 'required|recaptcha:register,0.34,hostname,v3',
            ],
            [
                'g-recaptcha.required' => 'g-recaptcha is required',
                'g-recaptcha.recaptcha' => 'Google ReCaptcha Verify Fails',
            ]
        );

        if ($validator->fails()){
            // Handle exception
            $errorMessage = $validator->errors()->first();  
        }
        // Do something
    }
}
```