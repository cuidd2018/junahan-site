+++
title = "Fabric CA 用户手册"
author = "Junahan"
date = 2018-07-29
keywords = ["fabric"]
lastmod = 2018-08-02T08:38:13+08:00
tags = ["Blockchain", "Fabric", "CA"]
categories = ["Blockchain"]
draft = false
+++

## <span class="section-num">1</span> 摘要 {#摘要}

Fabric CA 是 Fabric 的证书授权 (CA) 服务，它提供如下功能：

-   身份注册或者链接至 LDAP 作为用户注册中心
-   颁发注册证书 (ECerts)
-   证书的续期和吊销

Fabric CA 由服务器和客户端组件构成。

下图展示了 Fabric CA 在 Fabric 网络中的架构。

{{< figure src="https://ws2.sinaimg.cn/large/006tKfTcgy1ftu9xgzej0j30sv0k8di1.jpg" caption="Figure 1: Fabric CA 在 Fabric 网络架构中的作用" >}}

有两种方法和 Fabric CA 服务器交互：使用 Fabric CA 客户端或者是一个 Fabric SDKs。所有和 Fabric CA 服务器的通讯均通过 REST APIs 进行。有关 REST APIs swagger 文档，请参阅 `fabirc-ca/swagger/swagger-fabric-ca.json` 。你可以通过 [Swagger 在线编辑器](http://editor2.swagger.io/)查看这些文档。

Fabric CA 客户端或者 SDK 可能链接至 Fabric CA 集群，如上图中右上角部分显示的集群示意图。客户端的访问被 HA 代理端点通过负载均衡服务导入至集群的一个成员。

集群内所有 Fabric CA 服务器共享相同的数据库以保持对身份和证书的一致追踪。如果 LDAP 被配置，则身份信息会保存在 LDAP 而不是数据库。

一个服务器可能包含多个 CAs，每个 CA 要么是根 CA 或者一个中间 CA，一个中间 CA 有一个父 CA，该父 CA 要么是根 CA，要么是另外一个中间 CA。


## <span class="section-num">2</span> 开始 {#开始}


### <span class="section-num">2.1</span> 前提要求 {#前提要求}

-   Go 1.9+
-   正确的设置了 `GOPATH` 环境变量
-   正确安装 `libtool` 和 `libtdhl-dev` 包

运行如下命令在 Ubuntu 下安装 libtool

```sh
sudo apt install libtool libltdl-dev
```

运行如下命令在 MacOSX 中安装 libtool

```sh
brew install libtool
```

> 如果通过 Homebrew 安装 libtool，则libtldl-dev 无需安装。

更多有关 `libtool` 的信息，请参阅 <https://www.gnu.org/software/libtool>。
更多有关 `libltdl-dev`, 请参阅 <https://www.gnu.org/software/libtool/manual/html%5Fnode/Using-libltdl.html>。


### <span class="section-num">2.2</span> 安装 {#安装}

如下命令安装 `fabric-ca-server` 和 `fabric-ca-client` 二进制包进入 `$GOPATH/bin` 目录。

```sh
go get -u github.com/hyperledger/fabric-ca/cmd/...
```

注意：如果你已经克隆 `fabric-ca` 代码库，请确保在运行如上命令前，你位于 `master` 分支。否则，会出现如下错误：

```sh
<gopath>/src/github.com/hyperledger/fabric-ca; git pull --ff-only
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=<remote>/<branch> tlsdoc

package github.com/hyperledger/fabric-ca/cmd/fabric-ca-client: exit status 1
```


### <span class="section-num">2.3</span> 启动本地服务器 {#启动本地服务器}

运行如下命令在本地启动 `fabric-ca-server` - 使用默认设置：

```sh
fabric-ca-server start -b admin:adminpw
```

其中的 `-b` 选项指定启动管理员注册 (enrollment) ID 和密码。如果 LDAP 没有被激活 ("ldap.enabled" 设置为 `false` )， `-b` 选项必须提供。

文件名字为 `fabric-ca-server-config.yaml` 的默认设置在本地目录被创建，可以通过该文件定制 CA 服务器配置。


### <span class="section-num">2.4</span> 通过 Docker 启动服务器 {#通过-docker-启动服务器}


#### <span class="section-num">2.4.1</span> Docker Hub {#docker-hub}

访问 <https://hub.docker.com/r/hyperledger/fabric-ca/tags/>, 找到你需要的 `fabric-ca` Docker 镜像版本标签。

定位到 `$GOPATH/src/github.com/hyperledger/fabric-ca/docker/server` 目录并使用一个文本编辑器打开 `docker-compose.yml` 文件。

更改该文件中 `image` 那行去指定你需要的版本标签。该文件看起来如下所示：

```sh
fabric-ca-server:
  image: hyperledger/fabric-ca:x86_64-1.0.0-beta
  container_name: fabric-ca-server
  ports:
    - "7054:7054"
  environment:
    - FABRIC_CA_HOME=/etc/hyperledger/fabric-ca-server
  volumes:
    - "./fabric-ca-server:/etc/hyperledger/fabric-ca-server"
  command: sh -c 'fabric-ca-server start -b admin:adminpw'
```

在终端中运行如下命令可以启动 CA 服务器：

```sh
$ docker-compose up -d
```

这个命令将拉取指定的 `fabric-ca` 镜像版本，并启动一个 CA 服务器实例。


#### <span class="section-num">2.4.2</span> 构建自己的 Docker 镜像 {#构建自己的-docker-镜像}

你可以通过在终端中运行如下命令构建 `fabric-ca` Docker 镜像并启动一个 CA 服务器实例：

```sh
cd $GOPATH/src/github.com/hyperledger/fabric-ca
make docker
cd docker/server
docker-compose up -d
```

`hyperledger/fabric-ca` Docker 镜像包含 `fabric-ca-server` 和 `fabrica-ca-client` 组件。


### <span class="section-num">2.5</span> 浏览 Fabric CA CLI {#浏览-fabric-ca-cli}

如下链接展示了有关[服务器 CLI](https://hyperledger-fabric-ca.readthedocs.io/en/latest/servercli.html) 和[客户端 CLI](https://hyperledger-fabric-ca.readthedocs.io/en/latest/clientcli.html) 的使用帮助信息。


### <span class="section-num">2.6</span> 配置 {#配置}

Fabric CA 支持三种配置方法，优先级列表如下：

1.  CLI 选项
2.  环境变量
3.  配置文件

在该文档随后的部分，我们默认指通过配置文件进行配置。然而，配置文件的配置可以被环境变量或者 CLI 覆盖。

例如，如果我们有如下客户端配置文件：

```sh
tls:
  # Enable TLS (default: false)
  enabled: false

  # TLS for the client's listenting port (default: false)
  certfiles:
  client:
    certfile: cert.pem
    keyfile:
```

如下环境变量可以被用来覆盖在配置文件中的 `cert.pem` 设置:

```sh
export FABRIC_CA_CLIENT_TLS_CLIENT_CERTFILE=cert2.pem
```

如果我们想覆盖环境变量和配置文件设置，我们可以使用 CLI 选项：

```sh
fabric-ca-client enroll --tls.client.certfile cert3.pem
```


#### <span class="section-num">2.6.1</span> 有关文件路径 {#有关文件路径}

所有 Fabric CA 服务器和客户端配置文件均支持相对和绝对路径。相对路径相对于配置文件所在的位置。例如，如果配置目录是 `~/config` , 并且 `tls` 配置如下所示，那么 Fabric CA 服务器或者客户端将在 `~/config` 目录寻找 `root.perm` 文件，在 `~/config/certs` 目录寻找 `cert.pem` 文件并且在 `/abs/path` 目录寻找 `key.pem` 文件。

```sh
tls:
  enabled: true
  certfiles:
    - root.pem
  client:
    certfile: certs/cert.pem
    keyfile: /abs/path/key.pem
```


## <span class="section-num">3</span> Fabric CA 服务器 {#fabric-ca-服务器}

本节描述 Fabric CA 服务器。

在启动 Fabric CA 服务器之前需要初始化它。这提供了一个机会去生成默认配置文件，在启动之前，你可以查看和定制配置文件。

Fabric CA 服务根目录按照如下方式确定：

-   如果 `-home` 选项被设置，则使用该设置作为根目录
-   否则，如果 `FABRIC_CA_SERVER_HOME` 环境变量被设置，则使用该设置
-   否则，如果 `FABRIC_CA_HOME` 环境变量被设置，则使用该设置
-   否则，如果 `CA_CFG_PATH` 环境变量被设置，则使用该设置
-   否则，使用当前目录

在随后的有关 Fabric 服务器章节，我们假设你已经设置 `FABRIC_CA_HOME` 环境变量为 `$HOME/fabric-ca/server` 。

如下教程假设服务器配置文件在服务器根目录存在。


### <span class="section-num">3.1</span> 初始化服务器 {#初始化服务器}

执行如下命令初始化 Fabric CA 服务器：

```sh
fabric-ca-server init -b admin:adminpw
```

如果 LDAP 被禁用，选项 `-b` (引导用户身份) 需要在初始化时提供。至少一个引导用户身份应当提供，这个用户身份是服务器管理员。

服务配置文件中包含一个证书签名申请 (CSR) 部分，该部分能够被配置。下面是一个简单的 CSR 配置实例：

```yaml
cn: fabric-ca-server
names:
   - C: US
     ST: "North Carolina"
     L:
     O: Hyperledger
     OU: Fabric
hosts:
  - host1.example.com
  - localhost
ca:
   expiry: 131400h
   pathlength: 1
```

所有以上域和 X.509 签名密钥和证书有关，证书通过 `fabric-ca-server init` 命令产生。这个和 `ca.cerfile` 以及 `ca.keyfile` 文件相符。如下是相关域：

-   **cn** 指常用名称
-   **O** 指组织名字
-   **OU** 是组织单元
-   **L** 是位置或者城市
-   **ST** 是省份
-   **C** 是国家

如果需要，你可以定制 CSR 配置文件，删除 `ca.certfile` 和 `ca.keyfile` 配置项，并重新运行 `fabric-ca-server init -b admin:adminpw` 命令。

命令 `fabric-ca-server init` 在没有指定 `-u <parent-fabric-ca-server-URL>` 选项情况下，产生一个自签名 CA 证书。如果 `-u` 选项被指定，该 CA 服务器证书被父 Fabric CA 服务器签名。为了父 Fabric CA 服务器认证，URL 必须形如 `<scheme>://<enrollmentID>:<secret>@<host>:<port>` , 这里 `<enrollmentID>` 和 `<secret>` 的值和一个 `hf.IntermediateCA` 属性值为 `true` 的用户身份相符。命令 `fabric-server init` 也在服务器根目录产生一个名为 `fabric-ca-server-config.yaml` 的配置文件。

如果你想 Fabric CA 服务器使用你提供的 CA 签名证书和密钥文件，你必须分别放置这些文件到 `ca.certfile` 和 `ca.keyfile` 引用的目录。两个文件必须是 PEM 编码且不能够被加密。特别是，CA 证书文件内容必须以 `-----BEGIN CERTIFICATE-----` 开头，私钥内容必须以 `-----BEGIN PRIVATE KEY-----` 开头而不是 `-----BEGIN ENCRYPTED PRIVATE KEY-----` 开头。


#### <span class="section-num">3.1.1</span> 算法和密钥大小 {#算法和密钥大小}

CSR 能够被定制以产生支持椭圆曲线 (ECDSA) 算法的X.509 证书和密钥。如下是一个使用曲线 `prime256v1` 和签名算法 `ecdsa-with-SHA256` 的椭圆曲线数字全名算法 (ECDSA) 实例：

```sh
key:
   algo: ecdsa
   size: 256
```

算法和密钥大小的选择取决于安全的需要。

椭圆曲线签名算法提供如下几个密钥大小选项：

| 密钥大小 | ASN1 OID   | 签名算法          |
|------|------------|---------------|
| 256  | prime256v1 | ecdsa-with-SHA256 |
| 384  | secp384r1  | esdsa-with-SHA384 |
| 521  | secp521r1  | esdsa-with-SHA512 |


### <span class="section-num">3.2</span> 启动服务器 {#启动服务器}

运行如下命令启动 CA 服务器：

```sh
fabric-ca-server start -b <admin>:<adminpw>
```

如果此前还没有初始化服务器，它会在第一次启动时初始化它自己。在初始化期间，如果 `ca-cert.pem` 和 `ca-key.pem` 文件不存在，服务器会产生 他们并且如果配置文件不存在，它也创建一个默认的配置文件。详情请参阅[初始化 Fabric CA 服务器](#初始化服务器)一节。

除非 Fabric CA 服务器被配置为使用 LDAP，它必须至少被配置一个预先注册好的引导身份以能够注册另外的身份标示。选项 `-b` 用于指定为引导身份指定用户名和密码。

可以通过设置 `tls.enabled` 配置项为 `true` 来配置 Fabric CA 服务器监听 `https` 端口而不是 `http` 端口。

**安全警告:** Fabric CA 服务器应当总是被配置为激活 TLS (tls.enable 设置为 `true`)。否则，会导致黑客容易攻破脆弱的服务器以访问网络。

可以通过设置配置文件中的配置项 `registry.maxenrollments` 来限制相同密码被注册用户使用的次数。如果设置该值为 1，Fabric CA 服务器仅允许密码被特定注册 ID 使用一次。如果设置为 -1，则该密码可以被重复注册使用。默认值为 -1。该指设置为 0，Fabric CA 服务器则禁止注册身份标示。

启动 Fabric CA 服务器后，服务器开始监听 7054 端口。

如果你想配置 Fabric CA 服务器集群或者使用 LDAP，你可以跳过以下章节去 [Fabric CA 客户端](#fabric-ca-客户端)一节。


### <span class="org-todo todo TODO">TODO</span> <span class="section-num">3.3</span> 配置数据库 {#配置数据库}

本节描述如何配置 Fabric CA 服务器链接到 PostgreSQL 或者 MySQL 数据库。默认数据库是 SQLite 并且默认数据库文件 `fabric-ca-server.db` 位于 Fabric CA 服务器根目录。

如果你不关心运行 Fabric CA 服务器集群，你可以跳过本节，否则，必须配置 PostgreSQL 或者 MySQL。Fabric CA 服务器集群支持如下版本：

-   PostgreSQL: 9.5.5 或者更高版本
-   MySQL: 5.7 或者更高版本


#### <span class="section-num">3.3.1</span> PostgreSQL {#postgresql}

如下示例可以被添加到服务器配置文件以链接 PostgreSQL 数据库。请确保配置合适的变量值。数据库名字允许使用的字符有限制。更多信息，请参阅 PostgreSQL 文档：<https://www.postgresql.org/docs/current/static/sql-syntax-lexical.html#SQL-SYNTAX-IDENTIFIERS>.

```yaml
db:
  type: postgres
  datasource: host=localhost port=5432 user=Username password=Password dbname=fabric_ca sslmode=verify-full
```

指定 `sslmode` 选项配置 SSL 认证类型，合法的 `sslmode` 配置包括：

| Mode        | 描述                                                   |
|-------------|------------------------------------------------------|
| disable     | 不使用 SSL                                             |
| require     | 总是使用 SSL (跳过验证证书)                            |
| verify-ca   | 总是使用 SSL (验证服务器证书以确保该证书由可信 CA 签名) |
| verify-full | 在 verify-ca 基础上添加对服务器 hostname 验证以确保服务器 hostname 和证书匹配 |

如果你希望使用 TLS, 那么 CA 服务器配置文件的 `db.tls` 小节必须被配置。如果 PostgreSQL 服务器激活 SSL 客户端认证，那么客户端证书和密钥文件也必须在 `db.tls.client` 小节配置。如下是一个有关 `db.tls` 小节配置案例：

```yaml
db:
  ...
  tls:
      enabled: true
      certfiles:
        - db-server-cert.pem
      client:
            certfile: db-client-cert.pem
            keyfile: db-client-key.pem
```

-   **certfiles** - PEM-encoded 可信根证书文件列表；
-   **certfile** and **keyfile** - CA 服务器 PEM-encoded 证书和公钥文件 - 用于和 PostgreSQL 服务器通讯；

1.   PostgreSQL SSL 配置

    1.   PostgreSQL 服务器 SSL 配置基本指导:

        1.  修改 postgresql.conf, 去掉 SSL 配置注释并设置其值为 "on" (SSL=on)
        2.  在 PostgreSQL 数据目录放置 PostgreSQL 服务器证书和密钥文件；

        为 PostgreSQL 服务器产生一个自签名证书的指导 - <https://www.postgresql.org/docs/9.5/static/ssl-tcp.html>。

        注意：自签名证书适合测试目的，请不要在生产环境中使用自签名证书；

    2.   PostgreSQL 服务器 - 需要客户端证书

        1.  将 CA 服务器 `root.crt` 中你信任的证书认证 (CAs) 证书放置在 PostgreSQL 数据目录；
        2.  修改 `postgresql.conf`, 设置 "ssl<sub>ca</sub><sub>file</sub>" 指向客户端根证书 (root cert)；
        3.  修改 `pg_hba.conf`, 在 `hostssl` 配置小节设置 `clientcert` 参数为 1 ；

        有关更多 PostgreSQL 服务器 SSL 配置细节，请参阅 PostgreSQL 文档 - <https://www.postgresql.org/docs/9.4/static/libpq-ssl.html>.


#### <span class="org-todo todo TODO">TODO</span> <span class="section-num">3.3.2</span> MySQL {#mysql}


### <span class="section-num">3.4</span> 配置 LDAP {#配置-ldap}

Fabric CA 服务器能够被配置为和 LDAP 服务器一起工作。

特别地，Fabirc CA 服务器能够链接至 LDAP 服务器以：

-   认证已经注册的身份
-   获取身份属性用于授权

修改配置文件的 LDAP 部分以配置 CA 服务器链接至 LDAP 服务器：

```yaml
ldap:
   # Enables or disables the LDAP client (default: false)
   enabled: false
   # The URL of the LDAP server
   url: <scheme>://<adminDN>:<adminPassword>@<host>:<port>/<base>
   userfilter: <filter>
   attribute:
      # 'names' is an array of strings that identify the specific attributes
      # which are requested from the LDAP server.
      names: <LDAPAttrs>
      # The 'converters' section is used to convert LDAP attribute values
      # to fabric CA attribute values.
      #
      # For example, the following converts an LDAP 'uid' attribute
      # whose value begins with 'revoker' to a fabric CA attribute
      # named "hf.Revoker" with a value of "true" (because the expression
      # evaluates to true).
      #    converters:
      #       - name: hf.Revoker
      #         value: attr("uid") =~ "revoker*"
      #
      # As another example, assume a user has an LDAP attribute named
      # 'member' which has multiple values of "dn1", "dn2", and "dn3".
      # Further assume the following configuration.
      #    converters:
      #       - name: myAttr
      #         value: map(attr("member"),"groups")
      #    maps:
      #       groups:
      #          - name: dn1
      #            value: orderer
      #          - name: dn2
      #            value: peer
      # The value of the user's 'myAttr' attribute is then computed to be
      # "orderer,peer,dn3".  This is because the value of 'attr("member")' is
      # "dn1,dn2,dn3", and the call to 'map' with a 2nd argument of
      # "group" replaces "dn1" with "orderer" and "dn2" with "peer".
      converters:
        - name: <fcaAttrName>
          value: <fcaExpr>
      maps:
        <mapName>:
            - name: <from>
              value: <to>
```

其中：

-   **scheme** 是 `ldap` 或者 `ldaps` ；
-   **adminDN** 是管理员用户名；
-   **pass** 是管理员用户密码；
-   **host** 是 LDAP 服务器名字或者 IP 地址；
-   **port** 是可选 LDAP 服务器端口，默认 `ldap` 是 389, `ldaps` 是 636；
-   **base** 是可选的用于检索的 LDAP 树根；
-   **filter** 是一个可转换登录用户名至一个可区分名字的过滤器。例如，检索过滤器 `(uid=%s)` 用于检索 `uid` 属性值为登录用户名的 LDAP 条目。与之相似， `(email=%s)` 用于通过 Emial 地址登录；
-   **LDAPAttrs** 是一个包含多个 LDAP 属性名的数组；
-   `atrribute.converters` 部分用于转换 LDAP 属性至 Fabric CA 属性；
-   `attribute.maps` 部分用于映射 LDAP 响应值。

使用 govaluate 包的LDAP 表达式语言用户手册可以访问 <https://github.com/Knetic/govaluate/blob/master/MANUAL.md>。 它定义了像是 "=~" 和 "revoker\*" 这样的正则表达式操作符。


### <span class="section-num">3.5</span> 配置集群 {#配置集群}

本节提供一个如何使用 Haproxy 以路由负载均衡流量至 Fabric CA 服务器集群。请确保修改机器名和端口以匹配你的 Fabric CA 服务器设置。

haproxy.conf 文件配置如下:

```yaml
global
      maxconn 4096
      daemon

defaults
      mode http
      maxconn 2000
      timeout connect 5000
      timeout client 50000
      timeout server 50000

listen http-in
      bind *:7054
      balance roundrobin
      server server1 hostname1:port
      server server2 hostname2:port
      server server3 hostname3:port
```

注意: 如果使用 TLS，需要配置 `mode tcp` 。


### <span class="section-num">3.6</span> 设置多 CAs {#设置多-cas}

默认情况下，Fabric CA 服务器包含一个单一默认 CA。然而，多个 CAs 能够通过使用 cafiles 或者 cacount 配置选项加入一个 CA 服务器。每个 CA 将有自己的根目录 (home directory)。


#### <span class="section-num">3.6.1</span> cacount {#cacount}

`cacount` 提供了一个快速启动 X 个默认额外 CAs 的方法。每个 CA 根目录都相对于服务器根目录，使用这个选项，目录结构如下：

```sh
--<Server Home>
  |--ca
    |--ca1
    |--ca2
```

每个额外的 CA 将获得一个在其根目录生成的默认配置文件，配置文件包含一个唯一 CA 名字。

例如，如下命令将启动 2 个默认 CA 实例：

```sh
fabric-ca-server start -b admin:adminpw --cacount 2
```


#### <span class="section-num">3.6.2</span> cafiles {#cafiles}

当使用 `cafiles` 配置选项的时候，如果绝对路径没有被提供，CA 根目录将被设置为相对于服务器根目录。

使用这个选项，CA 配置文件必须已经被生成且被配置为启动每个 CA。每个配置文件必须有一个唯一的 CA 名字和 Common Name (CN)，否则，服务将启动失败。CA 配置文件将覆盖任何默认 CA 配置，并且任何在 CA 配置文件中没有设置的选项将使用默认 CA 对应的值替换。

优先级如下：

1.  CA 配置文件
2.  默认 CA CLI 标志
3.  默认 CA 环境变量
4.  默认 CA 配置文件

一个 CA 配置文件必须包含至少如下信息：

```yaml
ca:
# Name of this CA
name: <CANAME>

csr:
  cn: <COMMONNAME>
```

你可以配置你的目录结构如下：

```shell
--<Server Home>
  |--ca
    |--ca1
      |-- fabric-ca-config.yaml
    |--ca2
      |-- fabric-ca-config.yaml
```

例如，如下命令将启动两个定制 CA 实例：

```shell
fabric-ca-server start -b admin:adminpw --cafiles ca/ca1/fabric-ca-config.yaml
--cafiles ca/ca2/fabric-ca-config.yaml
```


### <span class="section-num">3.7</span> 注册 (Enrolling) 一个中间 CA {#注册--enrolling--一个中间-ca}

为了给一个中间 CA 创建一个签名证书，中间 CA 必须和一个 CA 客户端一样的方式注册 (enroll) 至 CA 服务器。这可以通过使用 `-u` 选项指定一个父 CA URL , enroment ID 和密码 (如下所述)。和这个注册 ID 相关的身份标示必须具有一个名字为 "hf.IntermediateCA" 且值为 "true" 的属性。其证书中 CN (Common Name) 属性将被设置为 enroment ID。如果一个中间 CA 尝试明确指定一个 CN 属性值将会产生错误。

```sh
fabric-ca-server start -b admin:adminpw -u http://<enrollmentID>:<secret>@<parentserver>:<parentport>
```

更多有关中间 CA 选项，请参阅 [Fabric CA 服务器配置文件格式](#fabric-ca-服务器配置文件格式)一节。


### <span class="org-todo todo TODO">TODO</span> <span class="section-num">3.8</span> 升级 CA 服务器 {#升级-ca-服务器}

Fabric CA 服务器必须先于 Fabric CA 客户端被更新。在更新之前，建议做好当前数据库的备份：

-   如果使用 sqlite3, 备份当前数据库文件 (默认名字是 `fabric-ca-server.db`)。
-   对于其他类型的数据库，请使用合适的备份/复制机制。

升级一个单一实例的 Fabric CA 服务器的步骤：

1.  停止 `fabric-ca-server` 进程；
2.  确认当前数据库已经被备份；
3.  使用新版本替换旧版本 `fabric-ca-server` 二进制文件；
4.  启动 `fabric-ca-server` 进程；
5.  使用如下命令检验 `fabric-ca-server` 进程是否可用 (<host> 指的是 CA 服务器主机名)：

```sh
fabric-ca-client getcainfo -u http://<host>:7054
```


#### <span class="org-todo todo TODO">TODO</span> <span class="section-num">3.8.1</span> 升级一个集群 {#升级一个集群}


## <span class="section-num">4</span> Fabric CA 客户端 {#fabric-ca-客户端}

本节描述如何使用 `fabric-ca-client` 命令。

Fabric CA 客户端根目录按如下规则确定：

-   如果 `-home` 命令行选项被提供，使用该值；
-   否则，如果 `FABRIC_CA_CLIENT_HOME` 环境变量被设置，使用该值；
-   否则，如果 `FABRIC_CA_HOME` 环境变量被设置，使用该值；
-   否则，如果 `CA_CFG_PATH` 环境变量被设置，使用该值；
-   否则，使用 `$HOME/.fabric-ca-client` ；

后面的教程假设客户端配置文件存在于客户端根目录。


### <span class="section-num">4.1</span> 注册引导身份 {#注册引导身份}

首先，如果需要的话，定制客户端配置文件的 CSR (Certificate Signing Request) 小节。注意 `src.cn` 域必须设置为引导身份 ID。默认 CSR 值如下所示：

```yaml
csr:
  cn: <<enrollment ID>>
  key:
    algo: ecdsa
    size: 256
  names:
    - C: US
      ST: North Carolina
      L:
      O: Hyperledger Fabric
      OU: Fabric CA
  hosts:
   - <<hostname of the fabric-ca-client>>
  ca:
    pathlen:
    pathlenzero:
    expiry:
```

然后运行 `fabric-ca-client enroll` 命令以注册身份。例如，如下命令通过调用运行在本地 7054 端口的 CA 服务器注册一个身份，其 ID 是 `admin`, 密码是 `adminpw` 。

```sh
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
fabric-ca-client enroll -u http://admin:adminpw@localhost:7054
```

命令 `enroll` 在 CA 客户端 `msp` 目录存储一个注册证书 (ECert)，该证书和子目录中的一个私钥及证书链 PEM 文件相关。可以看到一些指示 PEM 文件存储位置的消息。


### <span class="org-todo todo TODO">TODO</span> <span class="section-num">4.2</span> 登记 (Registering) 一个新身份 {#登记--registering--一个新身份}

执行登记请求的身份必须是当前已经注册且具有适当登记身份类型授权。

具体的说，在登记操作期间，要通过如下三种授权检查：

1.  管理员 (调用者) 必须具有 "hf.Registra.Roles" 属性，该属性的值是一个逗号隔开的值列表，每一个值代表一个被授权的登记身份类型；例如，如果管理员具有 "hf.Registrar.Roles" 属性值列表为 "peer,app,user"，该管理员可以登记身份类型是 `peer`, `app`, 和 `user` 的身份， 但不能登记身份类型为 `order` 的身份。
2.  管理员组织属性 (affiliation) 的值必须等于或者是被待登记身份组织属性值的前缀。例如，所属组织为 "a.b" 的管理员可以登记组织为 "a.b.c" 的身份标示但不能登记组织为 "a.c" 的身份。如果登记一个根组织身份，则组织应当指定为点号 (".") 并且管理员也必须是根组织管理员。如果没有指定组织，则新身份被登记为和调用管理员组织相同。
3.  如果符合如下条件，管理员可以登记一个带有相应属性的身份：
    -   对于 Fabric CA 保留的具有前缀 "hf." 的属性，只有管理员自己具有这些属性且他们是属性 "hf.Registrar.Attributes" 值列表的一部分的时候，管理员才可以登记。更多的，如果要登记的属性是列表类型，其值一定等于或者是管理员拥有的该属性的值的一个子集。如果要登记的属性的类型是布尔类型，则管理员拥有该属性的值必须是 "true" 才可以登记该属性。
    -   对于定制属性 (例如，任何名字不以 "hf." 开头的属性) ，要求管理具有 "hf.Registar.Attributes" 属性且该值和被登记的属性相同或者模式相匹配方可登记。模式匹配仅支持结束位置的 "**" 通配符。例如，"a.b.**" 是一个合法匹配模式，其可以匹配所有以 "a.b." 开头的属性。例如，如果管理员具有 `hf.Registrar.Attributes=orgAdmin`, 那么管理员只能为身份添加或者移除 "orgAdmin" 属性。
    -   如果请求的属性名字是 "hf.Registrar.Atrributes" 本身，会执行额外的检查以确保要登记的该属性值和管理员拥有的该属性值的相同或者是其的一个子集。这要求每一个请求的值都要匹配管理员属性 "hr.Registrar.Atrributes" 的相应值。例如，如果管理员属性 "hr.Registrar.Atrributes" 的值是 "a.b.\*, x.y.z" 并且请求的属性值是 "a.b.c, x.y.z"，检查合法，因为 "a.b.c" 匹配 "a.b.\*" 且 "x.y.z" 匹配管理员属性值 "x.y.z"。

**示例：**

-   合法示例：
    -   如果管理员具有属性 "hf.Registrar.Attributes = ab.\*, x.y.z" 并且要登记的属性是 "a.b.c"，这是合法的，因为 "a.b.c" 匹配 "a.b.\*"；
    -   如果管理员具有属性 "hf.Registrar.Attributes = ab.\*, x.y.z" 并且要登记的属性是 "x.y.z"，这是合法的；
    -   如果管理员具有属性 "hf.Registrar.Attributes = ab.\*, x.y.z" 并且要登记的属性是 "a.b.c, x.y.z"，这是合法的；
    -   如果管理员具有属性 "hf.Registrar.Roles = peer,client" 并且要登记的属性 "hf.Registrar.Roles" 的值是 "peer" 或者是 "peer,client"，这是合法的，因为请求的值等于或者是管理员值的一个子集；
-   非法示例：
    -   TODO

下表中列出所有能够被登记的保留属性。属性名字大小写敏感。

| 名字                       | 类型 | 描述                                                                                                    |
|--------------------------|----|-------------------------------------------------------------------------------------------------------|
| hf.Registrar.Roles         | 列表 | 允许管理员管理的角色列表                                                                                |
| hf.Registrar.DelegateRoles | 列表 | List of roles that the registrar is allowed to give a registree  for its ‘hf.Registrar.Roles’ attribute |
| hf.Registrar.Attributes    | 列表 | 允许管理员登记的属性列表                                                                                |
| hf.GenCRL                  | 布尔 | 该属性值为真的身份能够产生 CRL                                                                          |
| hf.Revoker                 | 布尔 | 该属性值为真的身份能够吊销一个用户 and/or 证书                                                          |
| hf.AffiliationMgr          | 布尔 | 该属性值为真的身份能够管理组织                                                                          |
| hf.InermediateCA           | 布尔 | 该属性值为真的身份能够作为中间 CA enroll                                                                |

注意：当登记一个身份的时候，你可以指定一个属性名字和值列数组。如果该数组包含有多个名字相同的元素，只有最后的元素被使用。换句话说，不支持合并多值属性。

如下命令使用 **admin** 身份证书登记一个新用户，该用户 enrollment id 是 "admin2"，组织是 "org1.department1"，且属性 "hf.Revoker=true"，并且属性 "admin=true"。后缀 ":ecert" 是指默认属性 "admin=true" 将被插入用户 注册证书，随后可被用于访问控制。

```sh
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
fabric-ca-client register --id.name admin2 --id.affiliation org1.department1 --id.attrs 'hf.Revoker=true,admin=true:ecert'
```

Enrollment secret 会在终端打印出来，这个密码会被 `enroll` 命令使用。这允许管理员登记一个身份并随后把 enrollment ID 和密码给另外的人去注册该身份。

多个属性可以通过 `--id.attrs` 选项指定，每个属性必须使用逗号分割。如果一个属性值包含逗号，该属性必须使用双引号。下面是例子：

```sh
fabric-ca-client register -d --id.name admin2 --id.affiliation org1.department1 --id.attrs '"hf.Registrar.Roles=peer,user",hf.Revoker=true'
```

或者：

```sh
fabric-ca-client register -d --id.name admin2 --id.affiliation org1.department1 --id.attrs '"hf.Registrar.Roles=peer,user"' --id.attrs hf.Revoker=true
```

你可以通过修改客户端配置文件，使用登记命令设置任意域的默认值。例如，如下所示的配置文件：

```yaml
id:
  name:
  type: user
  affiliation: org1.department1
  maxenrollments: -1
  attributes:
    - name: hf.Revoker
      value: true
    - name: anotherAttrName
      value: anotherAttrValue
```

如下命令会登记一个 enrollment id 是 "admin3" 的新身份，其 enrollment id 来自于命令行，其他部分来自于配置文件，包括 type: "user", affiliation: "org1.department1", 以及属性 "hf.Revoker" 和 "anotherAttrName"。

```yaml
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
fabric-ca-client register --id.name admin3
```

To register an identity with multiple attributes requires specifying all attribute names and values in the configuration file as shown above.

Setting maxenrollments to 0 or leaving it out from the configuration will result in the identity being registered to use the CA’s max enrollment value. Furthermore, the max enrollment value for an identity being registered cannot exceed the CA’s max enrollment value. For example, if the CA’s max enrollment value is 5. Any new identity must have a value less than or equal to 5, and also can’t set it to -1 (infinite enrollments).

Next, let’s register a peer identity which will be used to enroll the peer in the following section. The following command registers the peer1 identity. Note that we choose to specify our own password (or secret) rather than letting the server generate one for us.

```sh
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
fabric-ca-client register --id.name peer1 --id.type peer --id.affiliation org1.department1 --id.secret peer1pw
```

Note that affiliations are case sensitive except for the non-leaf affiliations that are specified in the server configuration file, which are always stored in lower case. For example, if the affiliations section of the server configuration file looks like this:

```sh
affiliations:
  BU1:
    Department1:
      - Team1
  BU2:
    - Department2
    - Department3
```

BU1, Department1, BU2 are stored in lower case. This is because Fabric CA uses Viper to read configuration. Viper treats map keys as case insensitive and always returns lowercase value. To register an identity with Team1 affiliation, bu1.department1.Team1 would need to be specified to the –id.affiliation flag as shown below:

```sh
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/admin
fabric-ca-client register --id.name client1 --id.type client --id.affiliation bu1.department1.Team1
```


### <span class="section-num">4.3</span> 注册对等节点身份 {#注册对等节点身份}

既然你已经成功的登记了一个对等节点身份，现在，你可以提供 enrollment ID 和密码 (由登记调用返回) 以注册对等节点。除了这里可以使用 `-M` 选项填充 Fabric MSP (Membership Service Provider) 目录结构以外，该过程和注册引导身份。

如下命令注册 peer1。请确保使用你的节点的 MSP 目录替换 `-M` 选项的值。该目录配置于对等节点 `core.yaml` 配置文件的 "mspConfigPath" 配置项。你也可以设置 `FABRIC_CA_CLIENT_HOME` 执行对等节点的 home 目录。

```sh
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca/clients/peer1
fabric-ca-client enroll -u http://peer1:peer1pw@localhost:7054 -M $FABRIC_CA_CLIENT_HOME/msp
```

组册一个 orderer 节点和此类似，只不过 MSP 目录的路径是 "LocalMSPDir"，该设置位于 orderer 节点 orderer.yaml 配置文件。

一个被 Fabric CA 服务器颁发的注册证书具有组织单元属性 ("OUs")：

1.  OU 根等于身份类型；
2.  为每个身份组织的构成部分添加一个 OU；

例如，如果身份类型是 `peer` 并且组织属性值是 "department1.team1"，该身份的 OU 层级结构 (从叶节点到根节点) 是 OU=team1, OU=department1, OU=peer。


### <span class="org-todo todo TODO">TODO</span> <span class="section-num">4.4</span> 从另外一个 CA 服务器获得一个 CA 证书链 {#从另外一个-ca-服务器获得一个-ca-证书链}


### <span class="org-todo todo TODO">TODO</span> <span class="section-num">4.5</span> 为用户获取一个 Idemix (Identity Mixer) 证书 {#为用户获取一个-idemix--identity-mixer--证书}


## <span class="org-todo todo TODO">TODO</span> <span class="section-num">5</span> 获得 Idemix CRI (证书吊销信息) {#获得-idemix-cri--证书吊销信息}


## <span class="org-todo todo TODO">TODO</span> <span class="section-num">6</span> HSM {#hsm}


## <span class="org-todo todo TODO">TODO</span> <span class="section-num">7</span> 文件格式 {#文件格式}


### <span class="section-num">7.1</span> Fabric CA 服务器配置文件格式 {#fabric-ca-服务器配置文件格式}


### <span class="section-num">7.2</span> Fabric CA 客户端配置文件格式 {#fabric-ca-客户端配置文件格式}


## <span class="org-todo todo TODO">TODO</span> <span class="section-num">8</span> 解决问题 {#解决问题}


## <span class="section-num">9</span> 参考文献 {#参考文献}

1.  Fabric CA document,  <https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#table-of-contents>.
2.  SoftHSM home, <https://www.opendnssec.org/softhsm/>.
3.  Fabric CA 1.2 API, <https://github.com/hyperledger/fabric-ca/blob/release-1.2/swagger/swagger-fabric-ca.json>.
