---
title: Plugin API
group: Plugins
sort: 0
---

T> For a high-level introduction to writing plugins, start with [writing a plugin](/contribute/writing-a-plugin).

Many objects in webpack extend the `Tapable` class, which exposes a `plugin` method. And with the `plugin` method, plugins can inject custom build steps. You will see `compiler.plugin` and `compilation.plugin` used a lot. Essentially, each one of these plugin calls binds a callback to fire at specific steps throughout the build process.

webpack中的许多对象扩展了`Tapable`类，它暴露了一个`plugin`方法。通过`plugin`方法，插件可以注入自定义构建步骤。你会看到`compiler.plugin`和`compilation.plugin`经常被使用。实质上在整个构建过程中，每一个插件调用都会绑定一个在特定步骤中触发的回调函数。

There are two types of plugin interfaces...

有两种类型的插件接口...

__Timing Based__

- sync (default): The plugin runs synchronously and returns its output.
- async: The plugin runs asynchronously and uses the give `callback` to return its output.
- parallel: The handlers are invoked in parallel.

- sync (default): 插件同步运行并返回其输出。
- async: 插件异步运行并通过回调函数返回其输出。
- parallel: 并行调用处理程序。

__Return Value__

- not bailing (default): No return value.
- bailing: The handlers are invoked in order until one handler returns something.
- parallel bailing: The handlers are invoked in parallel (async). The first returned value (by order) is significant.
- waterfall: Each handler gets the result value of the last handler as an argument.
- not bailing (default): 没有返回值.
- bailing: 处理程序按顺序调用，直到一个处理程序返回一些东西.
- parallel bailing: 处理程序并行调用（异步）。第一个返回的值（按顺序）是重要的.
- waterfall: 每个处理程序都将最后一个处理程序的结果值作为参数。

A plugin is installed once as webpack starts up. webpack installs a plugin by calling its `apply` method, and passes a reference to the webpack `compiler` object. You may then call `compiler.plugin` to access asset compilations and their individual build steps. An example would look like this:

启动webpack时，插件会被安装。 webpack通过调用其apply方法来安装插件，并将引用传递给webpack`compiler`对象。然后你可以调用`compiler.plugin`来访问静态编译和他们各自的构建步骤。一个例子看起来像这样：

__my-plugin.js__

``` js
function MyPlugin(options) {
  // Configure your plugin with options...
}

MyPlugin.prototype.apply = function(compiler) {
  compiler.plugin("compile", function(params) {
    console.log("The compiler is starting to compile...");
  });

  compiler.plugin("compilation", function(compilation) {
    console.log("The compiler is starting a new compilation...");

    compilation.plugin("optimize", function() {
      console.log("The compilation is starting to optimize files...");
    });
  });

  compiler.plugin("emit", function(compilation, callback) {
    console.log("The compilation is going to emit files...");
    callback();
  });
};

module.exports = MyPlugin;
```

__webpack.config.js__

``` js
plugins: [
  new MyPlugin({
    options: 'nada'
  })
]
```


## Tapable & Tapable Instances

The plugin architecture is mainly possible for webpack due to an internal library named `Tapable`.
**Tapable Instances** are classes in the webpack source code which have been extended or mixed in from class `Tapable`.
由于名为Tapable的内部库，插件体系结构主要可能用于webpack。 Tapable实例是webpack源代码中的类，它们已经从Tapable类中扩展或混合了。


For plugin authors, it is important to know which are the `Tapable` instances in the webpack source code. These instances provide a variety of event hooks into which custom plugins can be attached.
Hence, throughout this section are a list of all of the webpack `Tapable` instances (and their event hooks), which plugin authors can utilize.

对于插件作者，要知道哪些是webpack源代码中的Tapable实例。这些实例提供了各种可以附加自定义插件的事件挂钩。因此，在本节中，所有的webpack Tapable实例（及其事件钩子）都是插件作者可以使用的。

For more information on `Tapable` visit the [complete overview](/api/tapable) or the [tapable repository](https://github.com/webpack/tapable).
