# Ruff 应用开发的基本概念

下面是 Ruff 应用开发最简单的一个例子：

```javascript
$.ready(function() {
   $('#led-0').turnOn();
});

```

## 设备

设备是 Ruff 应用开发中的一个概念，它描述了用来组装最终硬件的各种器件。

### 选择设备

在 Ruff 应用开发中，首先我们要找到一个设备，然后，就可以对它执行一些操作。 Ruff 提供了如下的 API 让开发者选择设备。

```javascript
$('#led-0').turnOn();
```

这里我们通过设备 ID 找到对应的设备，之后就可以对其进行操作了

### 定义设备

如果能够在 Ruff 应用中使用设备，首先要做的是，定义设备。

如果你不了解设备信息，请先到 [软件包仓库](http://rap.ruff.io)查找设备信息。

然后，我们可以通过 `rap` 向应用中添加一个设备：

```shell
rap device add --id your-device-id
```

这里的 `your-device-id` 是你会在程序里面用到的，我们可以通过 `$('#your-device-id')` 进行引用。

在交互过程中，我们可以输设备信息，选择相应的驱动。

这个命令会在 `package.json` 中添加相应的依赖，并在 `app.json` 中定义这个变量相关的信息。

## 软件包

软件包可以把它理解成常规的程序库，它可以让 Ruff 程序员更容易地分享和重用代码。

目前软件包主要包括如下几类：

* 模块（module），普通的 JavaScript 程序库
* 驱动（driver），硬件设备的驱动，比如，某款传感器的驱动程序。
* 板卡（board），板卡的驱动，比如，Ruff 开发板的驱动。

你可以到[软件包仓库](http://rap.ruff.io)找到别人贡献的各种软件包。

### 安装软件包

安装软件包有如下几种方式。

#### 直接安装

通过 `rap` 直接安装是最简单的一种方式。

```shell
rap install your-package-name
```

`rap` 会根据 `your-package-name` 到[软件包仓库](http://rap.ruff.io)中进行查找，并下载安装到本地，同时更新你的 `package.json` 。

#### package.json

如果你有多个软件包要安装，另外一种做法是，在 `package.json` 中添加相关的依赖。然后，通过运行如下命令进行安装：

```shell
rap install
```

这样就可以一次性地把多个软件包直接下载到本地。

### 开发依赖

除了普通的依赖之外，另外还有一种依赖称之为开发依赖。两种依赖最大的差别是，开发依赖并不会随应用部署到硬件上，只会在开发阶段用到。

我们可以通过如下命令安装一个开发依赖：

```shell
rap install your-rap-name --save-dev
```

或是在 `package.json` 中声明 `devDependencies`，然后再通过如下命令进行安装：

```shell
rap install
```

## 应用

目前一个 Ruff 硬件同时只能运行一款 Ruff 应用，它将拥有自己独立的进程。应用开发者可以响应应用事件，执行相关操作。

```javascript
$.ready(function() {
  // do initialization work

});

$.end(function() {
  // do cleanup work

});
```

### 应用事件

目前 Ruff 应用支持如下事件：

* ready，描述设备正常启动之后，应用准备开始运行的状态。应用可以在这里做一些初始化的工作
* end，描述设备在关闭之前，应用即将关闭的状态。应用可以在这里做一些清理的工作


