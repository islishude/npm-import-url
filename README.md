# 不常见的 NPM 包管理技巧

## 使用 Git 包 

除了 npmjs.com 这个集中包服务，npm 还可以使用 git 来管理包。

git url 的格式：`<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]`

其中 `<protocol>` 可以是 `git`，`git+ssh`，`git+http`，`git+https`，或者 `git+file`。

`#<commit-ish>` 可以选择 commit 的点，`#semver:<semver>` 是选择 tag 并且支持语义化版本，如此以来我们不用发布到 npm 也能使用包了！在 Golang 中就是这样这样进行包管理，不过 Golang 1.10 还没有最终确定的版本管理方案。

如果没有使用 `commit-ish` 或者 `semver` 会直接使用 master 分支。

各种格式示例如下：

```
git+ssh://git@github.com:npm/npm.git#v1.0.27
git+ssh://git@github.com:npm/npm#semver:^5.0
git+https://isaacs@github.com/npm/npm.git
git://github.com/npm/npm.git#v1.0.27
```

如果是 GitHub 的话更简单了，直接使用 `npm install username/repository` 就可以了。这里还可以通过 `#feature\/branch` 定义分支。

```json
{
  "name": "foo",
  "version": "0.0.0",
  "dependencies": {
    "express": "expressjs/express",
    "mocha": "mochajs/mocha#4727d357ea",
    "module": "user/repo#feature\/branch"
  }
}
```

## 使用本地模块

`require` 引用当前项目的其它文件需要使用相对路径的地址，如果层级关系太多，就会写很多 `../`，有一种本地包的方式可以很好的解决。

配置很简单，类似本地包一样，新建一个包目录并且包括 package.json 文件，指定 main 字段即可，包名的话最好按照 `项目名-包名`。如本项目所示，在本项目 `helper` 包中新建一个项目包目录，并且命名为 `local-helper`。

然后在项目使用 `npm install file:package-path` 就可以了。如本项目所示，直接使用 `npm install file:./helper`。

然后就可以看到在 `package.json` 文件 `dependencies` 字段新增了 `"local-helper": "file:helper"` 。

```json
"dependencies": {
    "local-helper": "file:helper",
    "random.ts": "git+https://github.com/isLishude/random.ts.git#semver:2.0.0",
    "simple-node-promisify": "github:islishude/promisify"
}
```

`dependencies` 中的字段就是相应包中定义的名称。

## REF

- https://docs.npmjs.com/files/package.json#urls-as-dependencies
- https://github.com/isLishude/npm-import-url