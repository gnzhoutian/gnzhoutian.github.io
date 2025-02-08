# 关于我的博客

  [![GitHub stars](https://img.shields.io/github/stars/gnzhoutian/blog.svg)](https://github.com/gnzhoutian/blog/stargazers)
  [![GitHub forks](https://img.shields.io/github/forks/gnzhoutian/blog.svg)](https://github.com/gnzhoutian/blog/network)
  [![GitHub issues](https://img.shields.io/github/issues/gnzhoutian/blog.svg)](https://github.com/gnzhoutian/blog/issues)
  [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/gnzhoutian/blog/main/LICENSE)


## 一、前言

博客主题基于 [HyG Blog](https://github.com/Gaohaoyang/gaohaoyang.github.io) 设计修改。欢迎大家借鉴使用。

**涉及技术栈**

`Github Pages`  `Jekyll`  `Liquid`  `Markdown`  `Yaml`  `HTML`  `CSS`  `JavaScript`

**已有特性功能**

`最近提交`  `摘要`  `分类`  `标签`  `快捷搜索(双击Ctrl)`  `代码高亮`  `jemoji`  `mathjax` 

`移动端适配`  `离线运行`  `订阅支持`  `Discus/giscus评论`  `百度统计` `Google分析` `阅读量统计`


## 二、使用说明


### 1. 环境准备

由于 `Github Pages` 可以直接编译发布该仓库，因而下述说明仅涉及 **本地调试环境** 搭建。

1. 安装 `Ruby > 3.0`
2. 安装依赖：`bundle install -V`
3. 启动服务：`bundle exec jekyll s -VH 0.0.0.0 -P 4000` 或 `./run.sh`


### 2. 框架使用

1. 所有可配置项按需在 `_config.yml` 中调整
2. 在 `_posts` 路径中新增文章，建议基于模版  `_posts/1999-01-01-template.md` 仿写
3. `_posts` 路径可以按需创建文件夹，但文件命名需要遵循 `YYYY-MM-DD-title.EXT` 格式
4. `_posts` 详细使用说明详见 [Jekyll官方文档](https://jekyllrb.com/docs/posts/)
5. 在 `_assets` 中新增附件，引用示例：`![小婵]({{ '/_assets/test/chan.png' | relative_url }})`


### 3. 主要文件及用途说明

```shell
.
├── .git
├── .vscode
├── .gitignore
├── LICENSE
│
├── assets                       # 框架 - 资源
│   ├── [css/img/js]             # 框架 - 私有资源
│   └── vendor                   # 框架 - 第三方资源
├── _sass                        # 框架 - 样式
├── _includes                    # 框架 - 公共模版
├── _layouts                     # 框架 - 页面模版
├── page                         # 框架 - 页面
├── index.html                   # 框架 - 首页
├── Gemfile                      # 框架 - 依赖
├── CNAME                        # 框架 - 域名 Github自定义域名用
├── _config.yml                  # 框架 - 框架配置
│
├── _data                        # 数据 - 格式化数据，通过site.data访问，仅支持YAML/JSON/CSV/TSV
│   ├── friends.yml              # 数据 - 友链
│   └── links.yml                # 数据 - 收藏链接
├── _assets                      # 数据 - 文章附件，引用方式: {{ /_assets/xxx | relative_url }}
├── _drafts                      # 数据 - 草稿，不发布
├── _posts                       # 数据 - 文章
│   └── 1999-01-01-template.md   # 数据 - 文章模版
│
├── README.md                    # 说明
└── run.sh                       # 运行 - 本地调试

```

### 4. 代码提交备忘

```shell
# 1. 备份(可选)
rm -rf blog_bak/; cp -af blog blog_bak

# 2. rebase, 指向上一次正式提交, 弹出编辑窗口后，第一条commit用r，其它用f
git rb xxxxxx -> r / f

# 3. 日志格式：N为中文大写
新增文章N篇：字体选型及概念说明、利用LaTex生成技术简历

# 4. 提交
git push
```

## 致谢

| 序号 | 站点 | 描述 |
|:---: | :--- | :--- |
| 1 | [HyG Blog](https://github.com/Gaohaoyang/gaohaoyang.github.io) | 一个古典的Jekyll主题
| 2 | [Hexo Search](https://github.com/androiddevelop/hexo-search) | 一个静态博客搜索插件
| 3 | [Font Awesome](https://www.bootcss.com/p/font-awesome/) | 相对通用的字体
| 4 | [Liquid](https://jekyllrb.com/docs/liquid/filters/) | Liquid选择器帮助文档
| 5 | [Jekyll](https://jekyllrb.com/docs/) | 相对通用的字体
| 6 | [Code Beautify](https://codebeautify.org/) | 前端工具箱

