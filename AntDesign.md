# AntDesignUI框架

![](https://gw.alipayobjects.com/zos/rmsportal/KDpgvguMpGfqaHPjicRK.svg)

`antd-mobile`是 Ant Design 的移动规范的 React 开源框架。

### 特性和优势

* UI 样式高度可配置，拓展性更强，轻松适应各类产品风格
* 基于 React Native 的 iOS / Android / Web 多平台支持，组件丰富、能全面覆盖各类场景
* 提供 "组件按需加载" / "Web 页面高清显示" / "SVG Icon" 等优化方案，一体式开发
* 使用 ES6 开发，提供类型定义文件，支持类型及属性智能提示，方便业务开发
* 全面兼容 react / preact


### 适用场景

* 适合于中大型产品应用
* 适合于基于 react / preact / react-native 的多终端应用
* 适合不同 UI 风格的高度定制需求的应用


### 快速上手

1，创建一个项目

通过`create-react-app`创建一个React模板工程。

2，通过`yarn`安装如下工具包

* `antd-mobile`
* `babel-plugin-import`
* `react-app-rewired`

3，修改模板工程中的`index.html`文件为如下内容：

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no" />
    <script src="https://as.alipayobjects.com/g/component/fastclick/1.0.6/fastclick.js"></script>
    <script>
      if ('addEventListener' in document) {
        document.addEventListener('DOMContentLoaded', function() {
          FastClick.attach(document.body);
        }, false);
      }
      if(!window.Promise) {
        document.writeln('<script src="https://as.alipayobjects.com/g/component/es6-promise/3.2.2/es6-promise.min.js"'+'>'+'<'+'/'+'script>');
      }
    </script>
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>

```

4，修改模板工程中的`package.json`文件下的`scripts`值为如下内容：

```
{
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test --env=jsdom",
    "eject": "react-scripts eject"
}
```

5，然后在项目根目录创建一个 `config-overrides.js` 文件，其内容为：

```
const { injectBabelPlugin } = require('react-app-rewired');

module.exports = function override(config, env) {
    config = injectBabelPlugin(['import', { libraryName: 'antd-mobile', style: 'css' }], config);
    return config;
};
```

6，在需要使用组件的模块中引入所需组件即可：

```
import { Button } from 'antd-mobile';
```