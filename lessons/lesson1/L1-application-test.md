# 传统硬件应用测试

在传统的硬件开发中，测试应用的方式是，将开发好的软件打成目标镜像，烧写到目标硬件上。因为通常镜像文件比较大，所以，烧写文件的过程通常以分钟计，多者甚至长达几十分钟。

如果只是最终的集成测试过程，这么做是可以接受的。但通常在开发过程中，开发者进行的测试通常是一些应用逻辑的测试。如果这样的测试也只能在硬件上进行，无疑浪费了大量的时间。

# Ruff 应用测试

Ruff 致力于简化硬件开发过程，它提供一个 App Runner 和模拟设备作为应用测试的基础设施，如此一来，大多数应用测试都可以在开发机上完成，省去了每次部署到硬件的繁琐过程。

以[起步走](https://github.com/ruffjs/Dive-into-Ruff/blob/master/lessons/lesson1/L1-application-development-get-started.md)的代码为例，因为用到是既有的驱动，我们只要保证调到了对应的函数，剩下的工作，驱动就帮我们完成好了。测试可以如下编写：

```js
var runner = require('ruff-app-runner');
var verify = require('ruff-mock').verify;

exports['test should call turn on while application is ready'] = function() {
    runner.run(appPath, function() {
        verify($('#led-r')).turnOn();
    });
};

require('test').run(exports);
```
(test/app-test.js)

运行如下命令执行测试：

```sh
ruff test/app-test.js
```

这里用到的是几个框架：
* ruff-app-runner，是 Ruff 提供的应用测试框架，它就是前面提到的 App Runner ，所有的设备都已经由模拟对象替代，保证可以在开发机上执行。
* ruff-mock，是 Ruff 提供的一个通用 mock 框架，在这里主要用于支持模拟设备。
* 一个简单的测试框架，符合[CommonJS的Unit Testing规范](http://wiki.commonjs.org/wiki/Unit_Testing)，运用其它的单元测试框架达成同样的目的。

下面会分别介绍一下 App Runner 和模拟设备，这是 Ruff 应用测试的两个核心概念。

# App Runner

App Runner 是 Ruff 提供的一个模拟运行环境，让应用可以在没有硬件的情况下执行。其基本用法如下所示：

```javascript
var runner = require('ruff-app-runner');

runner.run(appPath, function() {
    // your test code when your application is running

}).end(function() {
    // your test code after your application is end

});
```

其中，
```javascript
var runner = require('ruff-app-runner');
```

引用了 `ruff-app-runner` ，我们就可以在接下来使用 App Runner 的功能了。

我们的测试场景主要包括两个部分，一个是应用运行时，一个是应用结束之后，分别对应前面的两个方法：

* run，应用已经就绪，应用的 ready 部分代码已经调用。 该方法有两个参数，其中， `appPath` 就是应用的工程所在路径， App Runner 会加载相应的工程配置，另一个是一个函数，就是我们要运行的代码。
* end，应用已经结束，应用的 end 部分代码已经调用。

如果你对 `ready`和`end`还不了解，可以先了解一下[编程模型](https://github.com/ruffjs/Dive-into-Ruff/blob/master/lessons/lesson1/L1-application-programming-model.md)。

# 模拟设备

模拟设备是 Ruff 保证硬件应用运行在开发机上一种解决方案，它对驱动编程模型中的基本概念提供了很好的支持：

* 模拟数据返回，比如，温度传感器返回温度值。
* 验证硬件行为，比如，LED 灯被打开。
* 触发驱动事件，模拟某些中断行为，比如，有人接近红外传感器。

其中，模拟数据返回和验证硬件行为是由`ruff-mock`支持，触发驱动事件是由`event`机制支持的。

## 模拟数据返回

模拟设备可以设置其调用方法的返回值，如下所示：

```javascript
when($('#temperature'))
    .getTemperature(Function)
    .then(function (callback) {
        callback(undefined, 26);
    });
```

## 验证硬件行为

我们可以通过 `verify` 方法验证某个方法是否得到调用：

```js
verify($('#led')).turnOn();
```

## 触发驱动事件

每个模拟设备对象都实现了 `EventEmitter` 接口，调用 `emit` 接口，就可以触发其事件：

```js
$('#ir').emit('away');
```


