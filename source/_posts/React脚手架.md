---
title: 脚手架架构设计——React&Mobx从0到1最佳实践
date: 2018-07-01 11:56:13
categories: React全家桶
---
## 一、前言
React现在已经有很多脚手架工具，如**create-react-app**，支持一键创建一个React应用项目结构，很方便，但是享受方便的同时，也失去了对项目架构及技术栈完整学习的机会，而且通常脚手架创建的应用技术架构并不能完全满足我们的业务需求，需要我们自己修改，完善，所以如果希望对项目架构有更深掌控，最好还是从0到1理解一个项目。

## 二、项目结构与技术栈
### 文件目录
这次应用架构设计不使用任何脚手架，需要自己创建每一个文件，引入每一个技术和三方库，最终形成完整的应用，包括选择的完整技术栈。
项目结构图如下：1. src为应用源代码目录； 2. webpack.config.js为webpack配置入口文件； 3. package.json为项目依赖管理文件； 4. .babelrc文件，babel的配置文件，使用babel编译React和JavaScript代码； 5.README.md为项目说明文档； 6.bird-config是gulp-bird转发工具的配置文件；7.postcss.config.js是postcss的配置文件

![文件目录](http://pbzqi3myf.bkt.clouddn.com/%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95.png "文件目录")

### 技术栈
对整个技术栈进行分析，要考虑哪几个方面呢？
1. react和react-dom库是项目前提； 
2. react路由； 
3. 应用状态管理容器；
4. 如何优雅地发送ajax请求；
5. 打包上线发版，测试环境线上环境
6. css预编译及后处理，考虑兼容性写法；
7. 采用ES6写码，辅助工具，写代码更容易；
8. 此外，往往后端开发好的接口会部署在测试环境，而不是本地，因此还需要考虑联调时的代理转发。


根据以上划分决定选用以下第三方库和工具构成项目的完整技术栈：
1. react，react-dom;
2. react-router管理应用路由;
3. mobx作为JavaScript状态容器，mobx-react将React应用与mobx连接，Mobx相关详情请参见[Mobx官方文档](https://cn.mobx.js.org/)； 
4. axios库发送ajax请求，解决接口调用问题，请参见[Axios](https://www.kancloud.cn/yunye/axios/234845)
5. webpack配置;
6. 安装postcss的autoprefixer;
7. 需要安装babel，还可以使用lodash等可选辅助类，工具类库提升开发效率；
8. 代理转发工具选择的是百度BEFE团队开发的[gulp-bird](https://www.npmjs.com/package/gulp-bird)。

### 组件划分
React组件化开发原则是组件负责渲染UI，组件不同状态对应不同UI，通常遵循以下组件设计思路：
1. 路由组件：负责项目路由，可嵌套;
1. 布局组件：仅仅涉及应用UI界面结构的组件，不涉及任何业务逻辑，数据请求及操作;
2. 容器组件：负责获取数据，处理业务逻辑，通常在render()函数内返回展示型组件；
3. UI组件：指抽象出的可重用的UI独立组件，通常是无状态组件;

以本项目section的组件划分为例，index.js是入口文件，同时也承担了路由组件的角色，layout是布局组件，仅仅负责UI界面结构，homeindex是容器组件，是首页的业务逻辑，islider则是轮播图的独立组件，可复用。
![组件划分](http://pbzqi3myf.bkt.clouddn.com/%E7%BB%84%E4%BB%B6.png "组件划分")


## 三、项目搭建步骤
### 3.1准备工作
+ **git新建项目**

```
git init
touch README
git add README
git commit -m'first commit'
git remote add origin git@XXXXX.git
git push origin master

```

+ **npm安装react相关依赖包**

采用`npm install XXX -save`或`npm install XXX -save-dev`配置安装相关npm包,如果直接复制，可以直接`npm install`安装

```
{
    "name": "zhiqiu",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "build": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=production",
        "dev": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=development",
        "qa": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=qa",
        "start": "./node_modules/.bin/webpack-dev-server --config webpack.config.js --env.NODE_ENV=local"
    },
    "proxy": {
        "/mansion": {
            "target": "http://localhost:8009"
        }
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
        "axios": "^0.18.0",
        "babel-cli": "^6.26.0",
        "babel-core": "^6.26.0",
        "babel-loader": "^7.1.4",
        "babel-plugin-transform-decorators-legacy": "^1.3.4",
        "babel-polyfill": "^6.26.0",
        "babel-preset-env": "^1.6.1",
        "babel-preset-es2015": "^6.24.1",
        "babel-preset-react": "^6.24.1",
        "babel-preset-stage-2": "^6.24.1",
        "classnames": "^2.2.5",
        "clean-webpack-plugin": "^0.1.19",
        "copy-webpack-plugin": "^4.5.1",
        "css-loader": "^0.28.11",
        "extract-text-webpack-plugin": "^3.0.2",
        "file-loader": "^1.1.11",
        "html-webpack-inline-source-plugin": "0.0.10",
        "html-webpack-plugin": "^3.2.0",
        "islider": "^0.1.0",
        "mobx": "^4.1.0",
        "mobx-react": "^5.0.0",
        "node-sass": "^4.8.3",
        "path": "^0.12.7",
        "react": "^16.2.0",
        "react-dom": "^16.2.0",
        "react-router": "^4.2.0",
        "react-router-dom": "^4.2.2",
        "sass-loader": "^6.0.7",
        "style-loader": "^0.20.3",
        "url-loader": "^1.0.1",
        "webpack": "^3.10.0",
        "webpack-bundle-analyzer": "^2.11.1",
        "webpack-dev-server": "^2.11.2"
    },
    "dependencies": {
        "autoprefixer": "^8.6.4",
        "babel-plugin-lodash": "^3.3.4",
        "gulp-bird": "^0.2.4",
        "hi-ui": "0.0.9",
        "islider.js": "^2.2.2",
        "jquery": "^3.3.1",
        "lodash": "^4.17.10",
        "postcss-loader": "^2.1.5",
        "vconsole": "^3.2.0"
    }
}
```

### 3.2 路由和数据状态管理
路由安装mobx-react，react-router模块;
[React Router](http://react-guide.github.io/react-router-cn/docs/API.html)是完整的React路由解决方案，也是开发React应用最常使用的路由管理库，它提供简单的API，以声明式方式实现强大的路由功能，诸如按需加载，动态路由等。
1.声明式：语法简洁，清晰；
2.按需加载：延迟加载，根据使用需要判断是否需要加载；
3.动态路由：动态组合应用路由结构，更灵活，更符合组件化开发模式；

```
/**
 * @file index 入口页面
 * @author guoyueting
 */
import React from 'react';
import {render} from 'react-dom';
import {HashRouter, Route, Redirect} from 'react-router-dom';
import {Provider} from 'mobx-react';
import storeTree from './storeTree';
import 'section/common/scss/common.scss';
import Layout from 'section/layout/layout.js';

class App extends React.Component {
    render() {
        return (
            <Provider {...storeTree} >
                <HashRouter>
                    <Route path="/" component={Layout}/>
                </HashRouter>
            </Provider>
        );
    }
}

render(<App/>, document.getElementById('app'));
```
数据状态管理，安装mobx，确定storetree

```
/**
 * @file storeTree 整个app的状态树
 * @author guoyueting
 */
'use strict';

import {observable, action, extendObservable, runInAction} from 'mobx';
import NavState from 'section/nav/navStore';
import iSliderState from 'section/homeindex/component/islider/isliderStore';
import hotPointState from 'section/homeindex/component/hotpoint/hotpointStore';
import recommendState from 'section/homeindex/component/recommend/recommendStore';

import serviceState from 'section/service/serviceStore';
import repairState from 'section/myrepair/myrepairStore';


class AppState {
	// 全局store
	// ...
}

export default {
    AppState: new AppState(),
    NavState: new NavState(),
    iSliderState: new iSliderState(),
    hotPointState: new hotPointState(),
    recommendState: new recommendState(),
    serviceState: new serviceState(),
    repairState: new repairState()
}
```

**@observer** 函数/修饰器用于react组件。通过mobx-react依赖包来提供。它通过mobx.autorun来包装了组件的render函数，以确保组件的render函数在任何数据的更改是强制重新渲染。

**Autorun**是用在一些你想要产生一个不用观察者参与的被动调用函数里面。当autorun被使用的时候，一旦依赖项发生变化，autorun提供的函数就会被执行。

**action**是任何改变状态的事物。

```
/**
 * @file serviceStore
 * @author guoyueting
 */
'use strict';

import {observable, action, runInAction} from 'mobx';
import _ from 'lodash';
import * as model from 'src/itsm/model/model';
import {getSearchParam} from 'section/common/js/utils';

export default class serviceState {
    @observable serviceList = [];

    @action getServiceList() {
        let params = {
            mappedAppKey: getSearchParam('appKey')
        };
        model.getServiceListList(params).then(data => {
            runInAction(()=>{
                this.serviceList = _.get(data, 'data.data');
            });
        });
    }

    @action serviceClick(id) {
        let params = {
            mappedAppKey: getSearchParam('appKey'),
            id: id
        };
        model.serviceClick(params);
    }
}
```

### 3.3 Axios库发送ajax请求
安装axios，并通过get/post/put/delete等方式请求接口，对于json数据，一般在post请求进行数据格式转换，并在请求头部设置：

```
/**
 * @file  接口文件
 * @author guoyueting
 */
import axios from 'axios';
let axiosConfig = {
    headers: {
        'Content-Type': 'application/json;charset=UTF-8',
        'Access-Control-Allow-Origin': '*'
    }
};
// 获取列表
export let getHotList = function (data) {
    return axios.post('/rdwtv2/api/hot', JSON.stringify(data), axiosConfig);
};
```

### 3.4 代理转发gulp-bird
[gulp-bird](https://www.npmjs.com/package/gulp-bird)是由百度BEFE团队开发的代理转发工具，并不是一个gulp插件，在bird基础上进行了一些优化并发布到了npm，配置方法和bird一样。
配置目标服务器host和port等，参考如下。

```
/**
 * @file  bird-config.js
 * @author guoyueting
 */
var bird = require('./node_modules/gulp-bird/index');
// 静态服务器配置，可同时配置多个，域名需host到127.0.0.1
var server = {
    '8009': {
        // 静态文件根目录
        'basePath': './src/',
        // 是否开启调试模式，true(表示server端不缓存)，false（反之）
        'debug': true
        // 忽略的静态文件请求，与此正则匹配的请求将直接走转发规则（可选配置）
        // 'ignoreRegExp': /\/js\/urls\.js/g

    }
};
// 转发规则——静态服务器没有响应的或者忽略的请求将根据一下规则转发
var transpondRules = {
    '8009': {
        // 目标服务器的ip和端口，域名也可，但注意不要被host了
        targetServer: {
            'port': '8680',
            // 'port': '8080',
            'host': 'http://m1-ite-hidev04.m1.baidu.com',
            // 'host': 'cp01-ps-dev373-liuchao31.epc.baidu.com',
            // 当为true时，如果cookie or header中有相同key，则替换
            'replaceHeaders': true,
            'headers': {
                    'cookie': ''}
            }
        // 特殊请求转发，可选配置，内部的host、port和attachHeaders为可选参数
        regExpPath: {
            // '/oa-frontend-apply-1.0.0-SNAPSHOT': {
            //     'host': 'cp01-dev-heliping.epc.baidu.com/',
            //     'port': '8080',
            //     //'attachHeaders': {'app-id': 5},
            //     'path': '\/'
            // }npm
        }
    },
    'ajaxOnly': false
};

var toolsConf = {
    weinre: {
        // 和移动调试工具条中的vconsole冲突, 当为true时vconsole自动关闭
        open: false,
        port: 8009
    },
    // 移动端调试工具条，PC端开发可关闭
    showTools: false
};

bird.start(server, transpondRules, toolsConf);

```

### 3.5webpack配置打包
下面是一个较为基础的webpack文件配置。entry是配置模块的入口，webpack执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块；output定义了打包后的输出文件名、路径；module配置模块的读取和解析规则，通常用来配置loader；resolve配置Webpack如何寻找模块对应的文件；Plugins用于扩展webpack的功能，几乎所有Webpack无法直接实现的功能都能在社区找到开源的Plugin去解决。

```
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const path = require('path');
const webpack = require('webpack');

module.exports = function(env) {
    console.log(env);
    return {
        entry: {
            'index': './src/itsm/index.js'
        },
        output: {
            filename: 'js/[name]-[chunkhash].js',
            path: path.resolve(__dirname, './build'),
            chunkFilename: '[name].[chunkhash:4].child.js'
        },
        module: {
            rules: [{
                test: /(\.jsx|\.js)$/i,
                use: [{
                    loader: 'babel-loader'
                }]
                // exclude: /node_modules/
            },
            {
                test: /(\.scss|\.sass)$/i,
                use: ExtractTextPlugin.extract({
                    fallback: 'style-loader',
                    use: [{
                        loader: 'css-loader',
                        options: {
                            module: false,
                            minimize: true
                        }
                    }, {
                        loader: 'postcss-loader',
                    }, {
                        loader: 'sass-loader',
                        options: {
                            sourceMap: false
                        }
                    }]
                })
            },
            {
                test: /(\.png|\.jpg|\.jpeg|\.gif)$/i,
                use: [{
                    loader: 'url-loader',
                    options: {
                        limit: 100
                    }
                }]
            }]
        },
        resolve: {
            alias: {
                'src': path.resolve(__dirname, 'src'),
                'section': path.resolve(__dirname, 'src/itsm/section'),
                'img': path.resolve(__dirname, 'src/itsm/img'),
                'modules': path.resolve(__dirname, 'node_modules/islider.js/build')
            }
        },
        plugins: [
            new CleanWebpackPlugin(['./build']),
            new webpack.DefinePlugin({
                'process.env.NODE_ENV': JSON.stringify(env.NODE_ENV || 'development'),
                'isDev': JSON.stringify(env.isDev || 'true')
            }),
            new ExtractTextPlugin('style.css'),
            new HtmlWebpackPlugin({
                template: './src/itsm/index.html',
                filename: './index.html',
                chunks: ['index']
            })
        ],
        devServer: {
            host: '0.0.0.0',
            port: 9822,
            proxy: {
                '/rdwtv2': 'http://localhost:8009'
            }
        }
    }
}
```

### 3.6打包指令配置
配置不同环境的变量值，生产环境，QA环境，开发环境某些变量可能需要根据环境进行配置，然后用webpack的definePlugin插件，定义全局变量，可以保证在和环境有关的变量取值的正确性。


```
"scripts": {
    "build": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=production",
    "dev": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=development",
    "qa": "./node_modules/.bin/webpack --config webpack.config.js --env.NODE_ENV=qa",
    "start": "./node_modules/.bin/webpack-dev-server --config webpack.config.js --env.NODE_ENV=local"
}
```

## 四、未来改进
未来架构设计还需要完善的点：
1. Immutable数据，考虑引入Immutable.js； 
2. 测试，考虑jest集成应用测试
3. 函数库，如ramda，underscore； 
4. 调试工具，reactotron,react-devtools


## 五、Demo地址
最后，附上demo地址：`https://github.com/guoyueting/react-mobx-seed`




