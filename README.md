# Macula 官方文档

基于[Hugo](https://gohugo.io)和[Docsy](https://docsy.dev)的Macula官方文档


## 安装Hugo

请[参考官网](https://gohugo.io/installation/)

## 运行

```bash
git clone --depth 1 https://github.com/macula-projects/macula-website.git

## If you want to do SCSS edits and want to publish these, you need to install `PostCSS`

npm install

## Running the website locally
### 离线运行
#方式一:
#修改 config.toml 文件，增加
#[build]
#disableKinds = ["sitemap", "rss"]
#然后运行 hugo server
#方式二:
#直接运行 hugo server --disableKinds=sitemap,rss

hugo server


```