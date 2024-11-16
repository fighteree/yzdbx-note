
## 简介

Elasticsearch 是一个分布式、`RESTful` 风格的搜索和数据分析引擎，能够解决不断涌现出的各种用例。作为 Elastic Stack 的核心，Elasticsearch 会集中存储您的数据，让您飞快完成搜索，微调相关性，进行强大的分析，并轻松缩放规模。

## 安装

[下载地址](https://www.elastic.co/cn/elasticsearch)

## ES 的基本使用

[图片](https://i-blog.csdnimg.cn/blog_migrate/469524898e8da10f6ca078a61957270a.png)

① 下载和解压 Elasticsearch，无需安装解压后即可用，解压后目录如上图：

- bin：二进制系统指令目录，包含启动命令和安装插件命令等。
- config：配置文件目录。
- data：数据存储目录。
- lib：依赖包目录。
- logs：日志文件目录。
- modules：模块库，例如 x-pack 的模块。
- plugins：插件目录。

②[ 安装目录下](dwa)运行 bin/elasticsearch 来启动 ES。

③ 默认在 9200 端口运行，请求 curl [http://localhost:9200/](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A9200%2F) 或者浏览器输入 [http://localhost:9200](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A9200)，得到一个 JSON 对象，其中包含当前节点、集群、版本等信息。

> [!faq] dwa
> POST {
> 	str:"aaa",
> 	name:"liisi"
> }

