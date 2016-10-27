---
title: Yii-sa-路由解析
categories:
  - null
date: 2016-10-27 01:04:19
---

> 简单追寻了Yii2 从接受请求到响应sg


	1.web/index.php
	(new yii\web\Application($config))->run();
	2.vendor/yiisoft/yii2/web/Application.php
	class Application extends \yii\base\Application
	3.vendor/yiisoft/yii2/base/Application.php
	public function __construct($config = [])
    {
        Yii::$app = $this;
        static::setInstance($this);
        $this->state = self::STATE_BEGIN;
        $this->preInit($config);
        $this->registerErrorHandler($config);
        Component::__construct($config);
    }
    4.vendor/yiisoft/yii2/base/Component.php
    class Component extends Object
    
    5.vendor/yiisoft/yii2/base/Object.php
    public function __construct($config = [])
    {
        if (!empty($config)) {
            Yii::configure($this, $config);
        }
        $this->init();
    }
    
    6.vendor/yiisoft/yii2/base/Application.php
    public function init()
    {
        $this->state = self::STATE_INIT;
        $this->bootstrap();
    }
    
    public function run()
    {
        try {

            $this->state = self::STATE_BEFORE_REQUEST;
            $this->trigger(self::EVENT_BEFORE_REQUEST);

            $this->state = self::STATE_HANDLING_REQUEST;
            $response = $this->handleRequest($this->getRequest());

            $this->state = self::STATE_AFTER_REQUEST;
            $this->trigger(self::EVENT_AFTER_REQUEST);

            $this->state = self::STATE_SENDING_RESPONSE;
            $response->send();

            $this->state = self::STATE_END;

            return $response->exitStatus;

        } catch (ExitException $e) {

            $this->end($e->statusCode, isset($response) ? $response : null);
            return $e->statusCode;

        }
    }
    
    7.vendor/yiisoft/yii2/web/Application.php
    
    public function handleRequest($request)
    {
        if (empty($this->catchAll)) {
            list ($route, $params) = $request->resolve();
        } else {
            $route = $this->catchAll[0];
            $params = $this->catchAll;
            unset($params[0]);
        }
       
        try {
            Yii::trace("Route requested: '$route'", __METHOD__);
            $this->requestedRoute = $route;
            $result = $this->runAction($route, $params);
            if ($result instanceof Response) {
                return $result;
            } else {
                $response = $this->getResponse();
                if ($result !== null) {
                    $response->data = $result;
                }

                return $response;
            }
        } catch (InvalidRouteException $e) {
            throw new NotFoundHttpException(Yii::t('yii', 'Page not found.'), $e->getCode(), $e);
        }
    }
    
    
    
    
    
    