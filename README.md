# Resume-vuepress
![](https://img.shields.io/badge/build-passing-brightgreen.svg)
![](https://img.shields.io/badge/VuePress-v1.0.2-orange.svg)
![](https://img.shields.io/badge/license-MIT-%23373737.svg)

## 简介

通过本项目，你可以使用 Markdown 书写简历，并通过 VuePress 部署为可预览的页面。

[点击此处预览项目](https://siricee.github.io/Resume-vuepress)

来源于这个项目 [wannaxiao/vuepress-theme-resume](https://github.com/wannaxiao/vuepress-theme-resume)<br>
原项目依赖`VuePress v0.10.0`，本人在VuePress更新至正式版`v1.0.2`时将其重写。


## 使用

```bash
git clone https://github.com/Siricee/Resume-vuepress.git
cd Resume-vuepress
npm install
```
在`resume`文件夹内，根据`scaffold.md`修改`README.md`并保存，后执行以下命令
```bash
npm run dev 	# 预览
npm run build	# 生成静态页面
```


**打印简历**：

Chrome 页面中右键 -> 打印 -> 另存为 pdf。

*注意：打印-更多设置-取消勾选页眉和页脚。否则会有标题和日期。*

## 部署

Github-Pages部署说明：

本项目的部署条件

1. VuePress 以本地依赖的形式被安装到你的项目中，并且配置了如下的 npm scripts:
    ```javascript
    // package.json
    {
      "scripts": { // npm 命令映射
          "dev": "vuepress dev resume", // resume为文件夹名，可以更改
          "build": "vuepress build resume"
      },
      "devDependencies": { // 本地依赖
        "vuepress": "^1.0.2"
      }
    }
    ```

2. 在 `resume/.vuepress/config.js` 中设置正确的 `base`
    ```javascript
    module.exports = {
        title: 'Resume',
        base: '/Resume-vuepress/', //该行如下说明
        themeConfig: {
            navbar: false,
            sidebar: [{
                title: '简历',
                collapsable: false,
                children: [ '/' ]
            },
          ],
        }
    }
    ```
    如果你打算发布到 `https://<USERNAME>.github.io/`，则可以省略这一步，因为 `base` 默认即是 `"/"`。

    如果你打算发布到 `https://<USERNAME>.github.io/<REPO>/`（也就是说你的仓库在 `https://github.com/<USERNAME>/<REPO>`），则将 `base` 设置为 `"/<REPO>/"`

3. 在你的项目根目录中，创建一个如下的 `deploy.sh` 文件（**请自行判断启用注释掉的命令**）:
    ```shell
    #!/usr/bin/env sh

    # 确保脚本抛出遇到的错误
    set -e

    # 生成静态文件
    npm run docs:build

    # 进入生成的文件夹
    cd docs/.vuepress/dist

    # 如果是发布到自定义域名
    # echo 'www.example.com' > CNAME

    git init
    git add -A
    git commit -m 'deploy'

    # 如果发布到 https://<USERNAME>.github.io
    # git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

    # 如果发布到 https://<USERNAME>.github.io/<REPO>
    # git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

    cd -
    ```
    *提示：这部分可以参考本项目master分支示例代码*

4. 本地进入项目中双击执行`deploy.sh`即可自动部署。

其他方式部署这里不讲解，请参阅 [VuePress文档|部署](https://v1.vuepress.vuejs.org/guide/deploy.html)。

---

## 开发

本项目依赖于VuePress 1.0.2，结构按官方文档推荐风格设计，有问题请先参阅 [官方文档](https://v1.vuepress.vuejs.org/guide/deploy.html)。

### 命名

所有页面、目录下的默认页均为 `README.md` 且 **不可更改**，这是VuePress的约定。

本项目中的 `根目录 README.md` 与 `resume/README.md` 完全不同，前者为项目说明文档，后者为简历文档。

### 命令

开发者应注意，VuePress指令不同于npm，[官方文档|全局安装](https://v1.vuepress.vuejs.org/zh/guide/getting-started.html#全局安装)

```bash
vuepress dev resume		# 开发模式
vuepress build resume	# 生产模式
```

未配置 `package.json` 时 `npm run dev/build` 会报错，在 `package.json` 中添加命令

```javascript
{
    "scripts": {
        "dev": "vuepress dev resume",
        "build": "vuepress build resume"
  	}
}
```

`npm run dev/build` 才可以正常运行。

### 添加页面

添加页面时在 `resume` 内增加文件夹并添加默认README.md，然后在 `resume\.vuepress\config.js` 中 `sidebar` 字段添加信息即可，此处可参考 [官方文档|目录结构](https://v1.vuepress.vuejs.org/zh/guide/directory-structure.html)。

Author：[@Sirice](https://github.com/Siricee)   License：MIT
