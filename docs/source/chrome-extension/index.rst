Chrome extension
================

| 使用Vue-cli3.0和element-plus搭建的chrome插件。 | 

.. _installation:

项目初始化
------------



.. _usage:

项目使用
--------

1. 项目初始化
    |vue create bun-consumption-scope|
    |vue add chrome-extension-cli|
2. 安装element-plus
    |npm install element-plus|
3. 修改vue.config.js
    ```
    const path = require('path')
    const fs = require('fs')

    // Generate pages object
    const pages = {}

    function getEntryFile(entryPath) {
    let files = fs.readdirSync(entryPath)
    return files
    }

    const chromeName = getEntryFile(path.resolve(`src/entry`))

    function getFileExtension(filename) {
    return /[.]/.exec(filename) ? /[^.]+$/.exec(filename)[0] : undefined
    }
    chromeName.forEach((name) => {
    const fileExtension = getFileExtension(name)
    const fileName = name.replace('.' + fileExtension, '')
    pages[fileName] = {
        entry: `src/entry/${name}`,
        template: 'public/index.html',
        filename: `${fileName}.html`
    }
    })

    const isDevMode = process.env.NODE_ENV === 'development'
    const NodePolyfillPlugin = require("node-polyfill-webpack-plugin"); // Add this to fix the error about Crawler
    module.exports = {
    pages,
    filenameHashing: false,
    chainWebpack: (config) => {
        config.plugin('copy').use(require('copy-webpack-plugin'), [
        {
            patterns: [
            {
                from: path.resolve(`src/manifest.${process.env.NODE_ENV}.json`),
                to: `${path.resolve('dist')}/manifest.json`
            },
            {
                from: path.resolve(`src/tools/api.js`),
                to: `${path.resolve('dist')}/api.js`
            },
            {
                from: path.resolve(`src/assets/icon.png`),
                to: `${path.resolve('dist')}/icon.png`
            }
            ]
        }
        ])
    },
    configureWebpack: {
        plugins: [new NodePolyfillPlugin()], // Add this to fix the error about Crawler
        externals: {
        fs: require('fs'), // Add this to fix the error about Crawler
        http2: require('http2'), // Add this to fix the error about Crawler
        },
        output: {
        filename: `[name].js`,
        chunkFilename: `[name].js`
        },
        devtool: isDevMode ? 'inline-source-map' : false
    },
    css: {
        extract: false // Make sure the css is the same
    }
    }
    ```
4. 修改manifest.json
    ```
    {
        "manifest_version": 3,
        "name": "bnu-consumption-scope",
        "version": "0.0.1",
        "description": "Plug-in for mining consumption patterns for students at Beijing Normal University",
        "author": "Shaw",
        "icons": {
            "16": "/icon.png",
            "32": "/icon.png",
            "48": "/icon.png",
            "128": "/icon.png"
        },
        "content_scripts": [
            {
                "matches": [
                    "*://card.bnu.edu.cn/CardManage/*"
                ],
                "js": [
                    "/content.js"
                ],
                "run_at": "document_end"
            }
        ],
        "content_security_policy": {
            "script-src": [
                "'self'",
                "'unsafe-eval'"
            ],
            "object-src": [
                "'self'"
            ]
        },
        "devtools_page": "devtools.html",
        "permissions": [
            "tabs",
            "webRequest",
            "activeTab"
        ]
    }
    ```
5. 修改popup.html

6. 修改background.js

7. 修改content.js

8. 修改popup.js

9. 修改popup.css

10. build
    |npm run build-watch|
    |npm run build|
