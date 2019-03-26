+++
title = "Fabric 入门"
author = "Junahan"
date = 2018-07-16
lastmod = 2019-03-18T18:05:14+08:00
tags = ["Blockchain", "Hyperledger", "Fabric", "超级账本", "区块链"]
categories = ["Blockchain"]
draft = false
+++

## <span class="section-num">1</span> 摘要 {#摘要}

本文介绍使用 Fabric Sample 快速在本地搭建一个基本的 Fabric 链码测试环境。


## <span class="section-num">2</span> 前提要求 {#前提要求}

开始之前，请检查是否已经安装了 Fabric 区块链应用开发和运行所需要的相关工具和平台。


### <span class="section-num">2.1</span> Doker 和 Docker Compose {#doker-和-docker-compose}

需要在你运行或者开发 Fabric 的平台上安装 Docker 以及 Docker Compose：

-   MacOSX, \*nix，或者 Windows 10: [Docker](https://www.docker.com/get-docker) 17.06.2-ce 或者更高版本。
-   老版本 Windows: [Docker Toolbox](https://docs.docker.com/toolbox/toolbox%5Finstall%5Fwindows/)，Docker 17.06.2-ce 及更高版本。

运行 `docker --version` 检查你安装的 Docker 版本。运行 `docker-compose --version` 检查 Docker Compose 版本。


### <span class="section-num">2.2</span> Go 编程语言 {#go-编程语言}

Fabric 很多组件使用 Go 编程语言。需要 Go 1.10.x 版本以上。

根据不同的安装方法，你可能需要设置两个环境变量：

```sh
# GOPATH 是必须的
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```


## <span class="section-num">3</span> 使用 Fabric Sample {#使用-fabric-sample}

Fabric Sample 提供了有关 Fabric 网络的很多实例，可以根据需要启动需要的测试网络。


### <span class="section-num">3.1</span> Clone Fabric Sample {#clone-fabric-sample}

运行如下命令克隆 Fabric Sample 项目：

```sh
git clone https://github.com/hyperledger/fabric-samples.git
```


### <span class="section-num">3.2</span> 下载二进制工具和 Docker 镜像 {#下载二进制工具和-docker-镜像}

脚本 `scripts/boostrap.sh` 用于拉取所有运行 Fabric 需要的 Docker 镜像并标记为最新版本。可以指定 fabric, fabirc-ca, 以及第三方镜像版本号，默认为当前最新版本。

```sh
cd fabric-samples
./scripts/bootstrap.sh [version] [ca version] [thirdparty_version]
```

检查 Docker 镜像：

```sh
$ docker images |grep hyperledger*
hyperledger/fabric-ca          1.2.0               66cc132bd09c        2 weeks ago         252MB
hyperledger/fabric-ca          latest              66cc132bd09c        2 weeks ago         252MB
hyperledger/fabric-tools       1.2.0               379602873003        2 weeks ago         1.51GB
hyperledger/fabric-tools       latest              379602873003        2 weeks ago         1.51GB
hyperledger/fabric-ccenv       1.2.0               6acf31e2d9a4        2 weeks ago         1.43GB
hyperledger/fabric-ccenv       latest              6acf31e2d9a4        2 weeks ago         1.43GB
hyperledger/fabric-orderer     1.2.0               4baf7789a8ec        2 weeks ago         152MB
hyperledger/fabric-orderer     latest              4baf7789a8ec        2 weeks ago         152MB
hyperledger/fabric-peer        1.2.0               82c262e65984        2 weeks ago         159MB
hyperledger/fabric-peer        latest              82c262e65984        2 weeks ago         159MB
hyperledger/fabric-zookeeper   0.4.8               1ffd64c98bad        2 months ago        1.43GB
hyperledger/fabric-zookeeper   latest              1ffd64c98bad        2 months ago        1.43GB
hyperledger/fabric-kafka       0.4.8               12d61042b176        2 months ago        1.44GB
hyperledger/fabric-kafka       latest              12d61042b176        2 months ago        1.44GB
hyperledger/fabric-couchdb     0.4.8               12eb8cf6aba1        2 months ago        1.6GB
hyperledger/fabric-couchdb     latest              12eb8cf6aba1        2 months ago        1.6GB
```

检查二进制工具：

```sh
$ ls bin
configtxgen		cryptogen		fabric-ca-client	idemixgen		peer
configtxlator		discover		get-docker-images.sh	orderer
```


### <span class="section-num">3.3</span> 运行本地链码开发环境 {#运行本地链码开发环境}

目录 `chaincode-docker-devmode` 预先配置好了一个用于开发的环境。你可以直接启动并测试链码，首先，运行 `cd chaincode-docker-devmode` 进入该目录。


### <span class="section-num">3.4</span> Terminal 1 - 启动开发网络 {#terminal-1-启动开发网络}

运行如下命令启动开发开发网络：

```sh
docker-compose -f docker-compose-simple.yaml up
```

以上命令启动使用 `SingleSampleMSPSolo` orderer profile 并且启动对等节点为 `devmode` ：

-   hyperledger/fabric-orderer - order 节点，监听端口: 7050。
-   hyperledger/fabric-peer - 一个对等节点，监听端口：7051, 7053。
-   hyperledger/fabric-ccenv - 包含开发环境的客户节点。
-   hyperledger/fabric-tools - CLI 容器。


### <span class="section-num">3.5</span> Terminal 2 - 构建和启动链码 {#terminal-2-构建和启动链码}

在另外一个终端运行如下命令：

```sh
docker exec -it chaincode bash
```

系统显示结果如下：

```sh
root@f7295468eff5:/opt/gopath/src/chaincode#
```

系统默认挂载 `../chaincode` 目录至容器，现在，可以编译例子链码：

```sh
cd chaincode_example02/go
go build -o chaincode_example02
```

现在，可以运行该链码：

```sh
CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:0 ./chaincode_example02
```

现在，链码在对等节点启动，链码日志显示链码已经成功启动。注意，在这个阶段，链码并没有关联到任何通道，随后的步骤会使用 `instantiate` 命令完成这个任务。


### <span class="section-num">3.6</span> Terminal 3 - 使用链码 {#terminal-3-使用链码}

尽管是在 `--peer-chaincodedev` 模式下，仍然需要安装链码以完成系统链码生命周期管理常规检查。这个要求可能会在随后版本去掉。

我们可以使用 CLI 容器去完成链码安装和实例化操作：

```sh
docker exec -it cli bash
```

```sh
peer chaincode install -p chaincodedev/chaincode/chaincode_example02/go -n mycc -v 0
peer chaincode instantiate -n mycc -v 0 -c '{"Args":["init","a","100","b","200"]}' -C myc
```

现在，可以调用链码从 a 转移 10 到 b：

```sh
peer chaincode invoke -n mycc -c '{"Args":["invoke","a","b","10"]}' -C myc
```

最后，查询 a，我们将看到 a 的值是 90：

```sh
peer chaincode query -n mycc -c '{"Args":["query","a"]}' -C myc
```


### <span class="section-num">3.7</span> 测试自己的链码 {#测试自己的链码}

默认情况下，我们仅挂载 `chaincode-example02` 目录。你可以容易的通过添加你自己的链码子目录或者修改 `docker-compose-simple.yaml` 挂载你自己的链码目录，并重新运行网络来测试自己的链码。

如下是 `docker-compose-simple.yaml` 文件的一部分，通过修改 `chaincode` 容器定义片段标记为 `@1` 的那行，可以挂载你自己的链码目录。

```yaml
chaincode:
  container_name: chaincode
  image: hyperledger/fabric-ccenv
  ...
  volumes:
      - /var/run/:/host/var/run/
      - ./msp:/etc/hyperledger/msp
      - ./../chaincode:/opt/gopath/src/chaincode //@1
  depends_on:
    - orderer
    - peer
```


## <span class="section-num">4</span> 参考文献 {#参考文献}

1.  Getting Started, <https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting%5Fstarted.html>.
2.  Fabric Sample, <https://github.com/hyperledger/fabric-samples>.
3.  Using dev mode, <https://github.com/hyperledger/fabric-samples/tree/release-1.2/chaincode-docker-devmode>.
