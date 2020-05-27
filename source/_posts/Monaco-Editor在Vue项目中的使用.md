---
title: Monaco Editor在Vue项目中的使用
date: 2020-05-27 19:56:02
tags: Monaco Editor、编辑器、Vue、SQL自动补全
---
本文以SQL为例，展示Monaco Editor在Vue项目中的使用

## 安装包

### Monaco Editor的安装

``` bash
$ npm install --save monaco-editor
```

### Webpack打包Monaco Editor的插件

``` bash
$ npm install --save-dev monaco-editor-webpack-plugin
```

### Monaco Editor汉化包

``` bash
$ npm install --save monaco-editor-locales-plugin
```

## vue.config.js配置

``` javascript
const MonacoEditorWebpackPlugin = require('monaco-editor-webpack-plugin')
const MonacoEditorLocalesPlugin = require('monaco-editor-locales-plugin')
module.exports = {
    // ...
    configurWebpack: config => {
        config.plugins.push(new MonacoEditorWebpackPlugin({
            languages: ['sql']
        }))
        config.plugins.push(new MonacoEditorLocalesPlugin({
            languages: ['es', 'zh-cn'],
            defaultLanguages: 'zh-cn',
            logUnmatched: false,
            mapLanguages: {}
        }))
    }
}
```

## 使用

### 在Vue文件中使用Monaco Editor

创建div，并添加ref属性，用于初始化和使用Monaco Editor

``` html
<div ref="editor"></div>
```

初始化、主题色修改、自动补全

``` javascript
import * as monaco from 'monaco-editor'
// 修改编辑器主题（可引入本地json样式文件，也可以从从社区获取定义好的主题）
import('./Theme.json').then(data => {
    monaco.editor.defineTheme('yourTheme', data)
    monaco.editor.setTheme('yourTheme')
})
// 初始化
const this = _this // 用于在Editor函数内使用this
_this.monacoEditor = monaco.editor.create(_this.$ref.container, {
    value: _this.copyCode || _this.codes ,
    language: 'sql',
    // 其他需要配置的属性
    minimap: {
        enabled: false // 编辑器右侧的缩略图
    }
})
// 自动补全
this.monacoHints = monaco.languages.registerCompletionItemProvider('sql', {
    provideCompletionItems(model, position, context, token) {
        const suggestion = _this.yourHintsArray.map(item => {
            return {
                label: item,
                quickSuggestions: false,
                kind: monaco.languages.CompletionItemKind['Text'], // 用于定义提示的icon，可参照官网进行选择
                insertText: item
            }
        })
        return {
            suggestions: suggestion
        }
    },
    triggerCharacters: ['.'] // 选择触发提示的字符
})
// 当组件注销时，记得注销提示
this.monacoHints.dispose()

// 编辑区域，右键操作
const definedContextMenu = [{
    id: 'executeSQL',
    label: '执行',
    keybindings: [monaco.KeyMod.CtrlCmd | monaco.KeyCode.Enter],
    contextMenuGroupId: 'navigation',
    contextMenuOrder: 1,
    run: function(editor) {
        // 处理函数
    }
}]
definedContextMenu.forEach(item => {
    _this.monacoEditor.addAction(item)
})
```



