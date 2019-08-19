# 概览

- [回到首页](../readme.md)
- [原文链接](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html)

> 本文档讲解设置ES模块的内容

```txt
这小结的目的在于讨论怎么设置ES并且让它运行起来，包括
```

- 下载
- 安装
- 开启
- 配置

* 支持的平台

```txt
官方支持的操作系统和JVM矩阵可在下面链接获得,ES在列出的平台上进行了测试，但它也可能在其他平台上运行。
```

[支持矩阵](https://www.elastic.co/cn/support/matrix)

* Java 版本

```txt
ES是使用Java构建的，并且包含来自每个发行版中的内置版本的OpenJDK。内置的JVM是推荐的JVM，位于ES主目录的jdk目录中

当然也可以使用你自己的jdk版本，并且设置JAVA_HOME这个环境变量。如果你要使用和内置的JDK版本不一致的JDK，那么请参见
上述支持矩阵。如果使用错了JDK版本，那么ES将会启动失败。请注意，当你使用自定义JVM时内置的JDK可能会被删除掉
```

## 1 安装ES

```txt
你可以自己安装ES服务或者使用我们提供的云服务
```

* 自己安装ES

ES提供如下安装包形式

|Platform|说明|
|--|--|
|Linux 或 MacOS 的tar.gz文档|tar.gz存档可以在任何Linux发行版和MacOS上安装|
|Windows .zip文档|zip文档适用于在windows上安装|
|deb|deb包适用于Debian，Ubuntu和其他基于Debian的系统|
|rpm|rpm软件包适合安装在Red Hat，Centos，SLES，OpenSuSE和其他基于RPM的系统上|
|msi|msi软件包适合安装在至少安装了.NET 4.5框架的Windows 64位系统上，是在Windows上开始使用ES的最简单方法|
|docker|镜像可用于将ES作为Docker容器运行。它们可以从Elastic Docker Registry下载|
|brew|可以从Elastic Homebrew tap获得公式，以便使用Homebrew包管理器在macOS上安装ES|

* 配置管理工具

我们还提供以下配置管理工具来帮助进行大型部署

|tools|url|
|--|--|
|Puppet|[puppet-elasticsearch](https://github.com/elastic/puppet-elasticsearch)|
|Chef|[cookbook-elasticsearch](https://github.com/elastic/cookbook-elasticsearch)|
|Ansible|[ansible-elasticsearch](https://github.com/elastic/ansible-elasticsearch)|

### 1.1 Linux或MacOS的安装

* Linux安装

```txt
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.3.0-linux-x86_64.tar.gz
cd elasticsearch-7.3.0/
```

* Mac安装

```txt
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-darwin-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-darwin-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.3.0-darwin-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.3.0-darwin-x86_64.tar.gz
cd elasticsearch-7.3.0/
```

* 启用自动创建系统索引

```txt
一些商业功能会在ES中自动创建系统索引。默认情况下，ES配置为允许自动创建索引，不需要其他步骤。但是，如果您在ES中禁用
自动索引创建，你必须配置在elasticsearch.yml文件中配置 action.auto_create_index去运行商业特性
action.auto_create_index: .monitoring*,.watches,.triggered_watches,.watcher-history*,.ml*
```

* 从命令行启动ES

```
可以从命令行启动ES: ./bin/elasticsearch
```
默认情况下，ES在前台运行，将其日志打印到标准输出，并可以通过按Ctrl-C来停止

* Check ES在运行中

```
您可以通过向localhost上的端口9200发送HTTP请求来测试您的Elasticsearch节点是否正在运行：
curl -X GET "localhost:9200/?pretty"
ES应该给你一个像这样的回复：

{
  "name" : "Cp8oag6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "AT69_T_DTp-1qgIJlatQqA",
  "version" : {
    "number" : "7.3.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f27399d",
    "build_date" : "2016-03-30T09:51:41.449Z",
    "build_snapshot" : false,
    "lucene_version" : "8.1.0",
    "minimum_wire_compatibility_version" : "1.2.3",
    "minimum_index_compatibility_version" : "1.2.3"
  },
  "tagline" : "You Know, for Search"
}
```

* 运行守护进程

```txt
要将ES作为守护程序运行，请在命令行中指定-d，并使用-p选项将进程ID记录在文件中：
./bin/elasticsearch -d -p pid
可以在$ES_HOME/logs/目录中找到日志消息
杀掉ES进程可使用
pkill -F pid
```

* 在命令行中指定配置参数

```txt
默认情况下，ES从$ES_HOME/config/elasticsearch.yml文件加载其配置。
也可以在命令行中使用-E语法指定可在配置文件中指定的任何设置，如下所示：
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

* 档案目录布局

```txt
归档分发完全是独立的。默认情况下，所有文件和目录都包含在$ES_HOME中,也就是解压缩归档时创建的目录。
这非常方便，因为您不必创建任何目录来开始使用ES，卸载ES就像删除$ES_HOME目录一样简单。但是，建议更改config目录，
数据目录和logs目录的默认位置，以便以后不删除重要数据。
```

|Type|Description|Default Location|Settings|
|--|--|--|--|
|home|ES的主目录或$ES_HOME|解压压缩文件的地址||
|bin|二进制脚本包括用于启动节点的elasticsearch和用于安装插件的elasticsearch-plugin|$ES_HOME/bin||
|conf|配置文件elasticsearch.yml|$ES_HOME/config|ES_PATH_CONF|
|data|节点上分配的每个索引/分片的数据文件的位置,可配置多个位置。|$ES_HOME/data|path.data|
|logs|日志文件位置|$ES_HOME/logs|path.logs|
|plugins|插件文件位置，每个插件都将包含在一个子目录中|$ES_HOME/plugins||
|repo|共享文件系统存储库位置。可以容纳多个位置。文件系统存储库可以放在此处指定的任何目录的任何子目录中|无|path.repo|
|script|脚本文件的位置|$ES_HOME/scripts|path.scripts|

* 下一步

```txt
你现在已经设置了ES的测试环境。在开始认真开发或使用ES投入生产之前，您必须进行一些额外的设置：
```

[怎么配置ES](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)  
[配置ES重要参数](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html)  
[配置系统重要参数](https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html)

### 1.2 Windows上的安装

略

### 1.3 Debian包的安装

略

### 1.4 RPM安装包的安装

```txt
ES的RPM可以从我们的网站或我们的RPM存储库下载。它可用于在任何基于RPM的系统上安装ES，例如OpenSuSE，SLES，Centos，
Red Hat和Oracle Enterprise。
```

* 引入 ES PGP Key

```
我们使用带有指纹的ES签名密钥（PGP密钥D88E42B4，可从https://pgp.mit.edu获得）对我们的所有软件包进行签名：
4609 5ACC 8548 582C 1A26 99A9 D27D 666C D88E 42B4
下载并安装公共签名密钥：
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

* 从RPM库进行安装

```txt
在/etc/yum.repos.d/目录中为基于RedHat的发行版创建一个名为elasticsearch.repo的文件，或者在/etc/zypp/repos.d/目录
中为基于OpenSuSE的发行版创建一个文件，其中包含：
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md

您的存储库已准备就绪。您现在可以使用以下命令之一安装ES：
1 sudo yum install elasticsearch 在CentOS和较旧的基于Red Hat的发行版上使用yum
2 sudo dnf install elasticsearch 在Fedora和其他较新的Red Hat发行版上使用dnf。
3 sudo zypper install elasticsearch 在基于OpenSUSE的发行版上使用zypper
```

* 手动下载rpm文件

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-x86_64.rpm
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.0-x86_64.rpm.sha512
shasum -a 512 -c elasticsearch-7.3.0-x86_64.rpm.sha512
sudo rpm --install elasticsearch-7.3.0-x86_64.rpm
```

* 开启自动创建index能力

请参见tar.gz安装

* SysV init VS systemd

```txt
安装后ES不会自动启动。如何启动和停止ES取决于您的系统是使用SysV init还是systemd。您可以通过运行下面命令
来判断正在使用哪个:
ps -p 1
```

* 使用SysV init来启动ES

```txt
使用chkconfig命令将ES配置为在系统启动时自动启动：
sudo chkconfig --add elasticsearch
可以使用service命令启动和停止ES：
sudo -i service elasticsearch start
sudo -i service elasticsearch stop
如果ES因某原因无法启动，可以在/var/log/elasticsearch/中找到失败的原因
```

* 使用Systemd来启动ES

```
要将ES配置为在系统启动时自动启动，请运行以下命令：
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
ES可以按如下方式启动和停止：
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
```

* Chenk ES是否在运行中

```txt
curl -X GET "localhost:9200/?pretty"
```

* 配置ES

```txt
ES默认使用/etc/elasticsearch进行运行时配置。此目录和此目录中的所有文件的所有权都设置为安装ES时的根目录。该目录
设置了setgid标志，以便在/etc/elasticsearch下创建的任何文件和子目录也使用该所有权创建,例如: 使用keystore工具创建keystore
预计会对此进行维护，以便Elasticsearch进程可以通过组权限读取此目录下的文件。
默认情况下，ES从/etc/elasticsearch/elasticsearch.yml文件加载其配置。
RPM还有一个系统配置文件（/etc/sysconfig/elasticsearch），它允许您设置以下参数：
```

|option|description|
|--|--|
|JAVA_HOME|设置要使用的自定义Java路径|
|MAX_OPEN_FILES|最大打开文件数，默认为65535|
|MAX_LOCKED_MEMORY|最大锁定内存大小。如果您在elasticsearch.yml中使用bootstrap.memory_lock选项，则设置为无限制|
|MAX_MAP_COUNT|进程可能具有的最大内存映射区域数。如果使用mmapfs作为索引存储类型，请确保将其设置为较高的值。默认为262144|
|ES_PATH_CONF|配置文件目录（需要包含elasticsearch.yml，jvm.options和log4j2.properties文件）;默认为/etc/elasticsearch|
|ES_JAVA_OPTS|您可能想要应用的任何其他JVM系统属性|
|RESTART_ON_UPGRADE|略|

* RPM安装时先关的路径

|Type|Description|Default Location|Settings|
|--|--|--|--|
|home|ES的根目录或$ES_HOME|/usr/share/elasticsearch||
|bin|二进制脚本包括用于启动节点的ES和用于安装插件的elasticsearch-plugin|/usr/share/elasticsearch/bin||
|conf|配置文件包括elasticsearch.yml|/etc/elasticsearch||
|conf|环境变量，包括堆大小，文件描述符|/etc/sysconfig/elasticsearch||
|data|节点上分配的每个索引/分片的数据文件的位置，可以配置多个位置|/var/lib/elasticsearch|path.data|
|logs|日志文件位置|/var/log/elasticsearch|path.logs|
|plugins|插件文件位置。每个插件都将包含在一个子目录中|/usr/share/elasticsearch/plugins||
|repo|略||||

### 1.5 Windows MIS包的安装

略

### 1.6 Docker的安装

### 1.6 HomeBrew的安装

略

## 2 配置ES

## 3 重要的ES配置

## 4 重要的系统配置

## 5 启动检查项

## 6 开启ES

## 7 关闭ES

## 8 向集群中添加节点

## 9 设置X-Pack

## 10 配置监控

## 11 安全配置

## 12 通过Java客户端配置X-Pack

## 13 X-Pack的启动检查项
