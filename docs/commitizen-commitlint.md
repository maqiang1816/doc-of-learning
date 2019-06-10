# 使用 commitizen 和 commitlint 规范你的 commit message

[TOC]

## 前言

编写Commit Message需要遵循一定的范式，内容应该清晰明了，指明本次提交的目的，便于日后追踪问题。  
commitizen就是这么样一款工具,它用来规范化我们的commit信息。

## 安装commitizen

执行如下命令全局安装`commitizen`工具

``` bash
sudo npm instal -g commitizen
```

### 全局使用Angular项目commit规范

执行如下命令全局安装`cz-conventional-changelog`规范适配器

``` bash
sudo npm install -g cz-conventional-changelog
```

在您的主目录中创建一个.czrc文件，其路径指向首选的、全局安装的commitizen适配器

``` bash
echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc
```

之后，就可以在所有git工程中使用

### 单个工程使用Angular项目commit规范

切换到项目根目录，即`.git`文件夹目录，执行如下命令

```bash
commitizen init cz-conventional-changelog --save --save-exact
```

该命令需要当前目录有`package.json`文件，如果没有会报错，可以使用如下命令生成

``` bash
npm init -y
```

上述命令执行完后会在`pacakge.json`文件生成如下内容

``` javascript
"config": {
  "commitizen": {
    "path": "./node_modules/cz-conventional-changelog"
  }
},
"devDependencies": {
  "cz-conventional-changelog": "2.1.0",
}
```

以后使用`git commit`命令的地方统一换成`git cz`，就会出现选项，用来生成符合格式的 Commit message。
另外，git cz 命令支持所有git commit 的参数。例如，git cz -a

## Commit Message 规范

``` html
<type>(<scope>): <subject>
<空行>
<body>
<空行>
<footer>
```

上面是一次Commit后Message格式规范，分成标题，内容详情，结尾三个部分，各有各的用处，没有多余项。

头部即首行，是可以直接在页面中预览的部分，入上面图中所示，一共有三个部分`<type>，<scope>，<subject>`，含义分别如下

### Type

用于说明 commit 的类别

* feat: 新功能(feature)
* fix: 修补bug
* docs: 文档修改
* style: 格式(不影响代码运行的改动)
* refactor: 重构(既不是新增功能，也不是修改bug的代码改动)
* perf: 代码优化(提高性能的代码改动) 
* test: 增加遗漏或修正现有的测试用例
* build: 影响到构建过程或外部依赖的改动
* ci: ci配置文件及脚本的改动
* revert： 前一个commit的回退
* chore: src和test文件夹外的其他改动

### Scope

用来说明本次Commit影响的范围，即简要说明修改会涉及的部分

### Subject

用来简要描述本次改动，概述就好了，不超过50个字符。并且最好遵循下面三条:

* 以动词开头，使用第一人称现在时
* 英文首字母不要大写
* 结尾不用句号

### Body

该部分是对本次 commit 的详细描述，可以分成多行。有两个注意点

* 使用第一人称现在时
* 应该说明代码变动的动机以及与以前行为的对比

### Footer

Footer部分只用于两种情况

* 不兼容变动
  
> 如果当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法

* 关闭issue
  
> 如果当前 commit 针对某个issue，那么可以在 Footer 部分关闭这个 issue (可依次关闭过个issueCloses #123, #245, #992)

### Revert

还有一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以revert:开头，后面跟着被撤销 Commit 的 Header

Body部分的格式是固定的，必须写成`This reverts commit <hash>.`，其中的hash是被撤销 commit 的 SHA 标识符。

## 安装 commitlint 校验

[commitlint官网](https://commitlint.js.org/)

[config-conventional校验配置](http://npm.taobao.org/package/@commitlint/config-conventional)

因为我们现在使用的是Angular项目的commit规范，所以，使用angular项目的commit校验规范

切换到项目根目录，执行下列操作

### 安装依赖

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

执行完后，package.json 会多出两个依赖

``` javascript
"devDependencies": {
  "@commitlint/cli": "8.0.0",
  "@commitlint/config-angualr": "8.0.0",
}
```

### 生成配置文件

``` bash
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > .commitlintrc.js
```

### 添加 git hooks 配置

安装完`husky`后，在`package.json`文件添加如下配置

``` javascript
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }  
}
```

如此配置，在我们每次提交commit信息时，commitlint 工具都会通过angular规范进行校验，不通过时会给出相应的提示信息

## 生成 Change log

如果你的所有 Commit 都符合 Angular 规范格式，那么发布新版本时， Change log 就可以用脚本自动生成。

生成的文档包括以下三个部分

* New features
* Bug fixes
* Breaking changes

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。
当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。

### 安装changelog

``` bash
sudo npm install -g conventional-changelog
sudo npm install -g conventional-changelog-cli
```

然后进入到你要操作的项目目录，执行

``` bash
conventional-changelog -p angular -i CHANGELOG.md -s
```

此时项目中多了CHANGELOG.md文件，表示生成 Change log成功了。

``` bash
conventional-changelog -p angular -i CHANGELOG.md -w
```

上面命令不会覆盖以前的 Change log，只会在CHANGELOG.md的头部加上自从上次发布以来的变动。  

如果你想生成所有发布的 Change log，要改为运行下面的命令。

```bash
conventional-changelog -p angular -i CHANGELOG.md -w -r 0
```

为了方便使用，可以将其写入package.json的scripts字段。

``` javascript
"scripts": {
  "changelog": "conventional-changelog -p angular -i CHANGELOG.md -w -r 0"
}
```

以后，直接运行下面的命令即可。

``` bash
npm run changelog
```
