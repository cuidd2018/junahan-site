<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. 摘要</a></li>
<li><a href="#sec-2">2. 前提要求</a>
<ul>
<li><a href="#sec-2-1">2.1. Doker 和 Docker Compose</a></li>
<li><a href="#sec-2-2">2.2. Go 编程语言</a></li>
</ul>
</li>
<li><a href="#sec-3">3. 使用 Fabric Sample</a>
<ul>
<li><a href="#sec-3-1">3.1. Clone Fabric Sample</a></li>
<li><a href="#sec-3-2">3.2. 下载二进制工具和 Docker 镜像</a></li>
<li><a href="#sec-3-3">3.3. 运行本地链码开发环境</a></li>
<li><a href="#sec-3-4">3.4. Terminal 1 - 启动开发网络</a></li>
<li><a href="#sec-3-5">3.5. Terminal 2 - 构建和启动链码</a></li>
<li><a href="#sec-3-6">3.6. Terminal 3 - 使用链码</a></li>
<li><a href="#sec-3-7">3.7. 测试自己的链码</a></li>
</ul>
</li>
<li><a href="#sec-4">4. 参考文献</a></li>
</ul>
</div>
</div>



# 摘要<a id="sec-1" name="sec-1"></a>

本文介绍使用 Fabric Sample 快速在本地搭建一个基本的 Fabric 链码测试环境。

# 前提要求<a id="sec-2" name="sec-2"></a>

开始之前，请检查是否已经安装了 Fabric 区块链应用开发和运行所需要的相关工具和平台。

## Doker 和 Docker Compose<a id="sec-2-1" name="sec-2-1"></a>

需要在你运行或者开发 Fabric 的平台上安装 Docker 以及 Docker Compose：
-   MacOSX, \*nix，或者 Windows 10: [Docker](https://www.docker.com/get-docker) 17.06.2-ce 或者更高版本。
-   老版本 Windows: [Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/)，Docker 17.06.2-ce 及更高版本。

运行 `docker --version` 检查你安装的 Docker 版本。运行 `docker-compose --version` 检查 Docker Compose 版本。

## Go 编程语言<a id="sec-2-2" name="sec-2-2"></a>

Fabric 很多组件使用 Go 编程语言。需要 Go 1.10.x 版本以上。

根据不同的安装方法，你可能需要设置两个环境变量：

    # GOPATH 是必须的
    export GOPATH=$HOME/go
    export PATH=$PATH:$GOPATH/bin

# 使用 Fabric Sample<a id="sec-3" name="sec-3"></a>

Fabric Sample 提供了有关 Fabric 网络的很多实例，可以根据需要启动需要的测试网络。

## Clone Fabric Sample<a id="sec-3-1" name="sec-3-1"></a>

运行如下命令克隆 Fabric Sample 项目：

    git clone https://github.com/hyperledger/fabric-samples.git

## 下载二进制工具和 Docker 镜像<a id="sec-3-2" name="sec-3-2"></a>

脚本 `scripts/boostrap.sh` 用于拉取所有运行 Fabric 需要的 Docker 镜像并标记为最新版本。可以指定 fabric, fabirc-ca, 以及第三方镜像版本号，默认为当前最新版本。

    cd fabric-samples
    ./scripts/bootstrap.sh [version] [ca version] [thirdparty_version]

检查 Docker 镜像：

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

检查二进制工具：

    $ ls bin
    configtxgen     cryptogen       fabric-ca-client    idemixgen       peer
    configtxlator       discover        get-docker-images.sh    orderer

## 运行本地链码开发环境<a id="sec-3-3" name="sec-3-3"></a>

目录 `chaincode-docker-devmode` 预先配置好了一个用于开发的环境。你可以直接启动并测试链码，首先，运行 `cd chaincode-docker-devmode` 进入该目录。

## Terminal 1 - 启动开发网络<a id="sec-3-4" name="sec-3-4"></a>

运行如下命令启动开发开发网络：

    docker-compose -f docker-compose-simple.yaml up

以上命令启动使用 `SingleSampleMSPSolo` orderer profile 并且启动对等节点为 `devmode` ：
-   hyperledger/fabric-orderer - order 节点，监听端口: 7050。
-   hyperledger/fabric-peer - 一个对等节点，监听端口：7051, 7053。
-   hyperledger/fabric-ccenv - 包含开发环境的客户节点。
-   hyperledger/fabric-tools - CLI 容器。

## Terminal 2 - 构建和启动链码<a id="sec-3-5" name="sec-3-5"></a>

在另外一个终端运行如下命令：

    docker exec -it chaincode bash

系统显示结果如下：

    root@f7295468eff5:/opt/gopath/src/chaincode#

系统默认挂载 `../chaincode` 目录至容器，现在，可以编译例子链码：

    cd chaincode_example02/go
    go build -o chaincode_example02

现在，可以运行该链码：

    CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:0 ./chaincode_example02

现在，链码在对等节点启动，链码日志显示链码已经成功启动。注意，在这个阶段，链码并没有关联到任何通道，随后的步骤会使用 `instantiate` 命令完成这个任务。

## Terminal 3 - 使用链码<a id="sec-3-6" name="sec-3-6"></a>

尽管是在 `--peer-chaincodedev` 模式下，仍然需要安装链码以完成系统链码生命周期管理常规检查。这个要求可能会在随后版本去掉。

我们可以使用 CLI 容器去完成链码安装和实例化操作：

    docker exec -it cli bash

    peer chaincode install -p chaincodedev/chaincode/chaincode_example02/go -n mycc -v 0
    peer chaincode instantiate -n mycc -v 0 -c '{"Args":["init","a","100","b","200"]}' -C myc

现在，可以调用链码从 a 转移 10 到 b：

    peer chaincode invoke -n mycc -c '{"Args":["invoke","a","b","10"]}' -C myc

最后，查询 a，我们将看到 a 的值是 90：

    peer chaincode query -n mycc -c '{"Args":["query","a"]}' -C myc

## 测试自己的链码<a id="sec-3-7" name="sec-3-7"></a>

默认情况下，我们仅挂载 `chaincode-example02` 目录。你可以容易的通过添加你自己的链码子目录或者修改 `docker-compose-simple.yaml` 挂载你自己的链码目录，并重新运行网络来测试自己的链码。

如下是 `docker-compose-simple.yaml` 文件的一部分，通过修改 `chaincode` 容器定义片段标记为 `@1` 的那行，可以挂载你自己的链码目录。

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

# 参考文献<a id="sec-4" name="sec-4"></a>

1.  Getting Started, <https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html>.
2.  Fabric Sample, <https://github.com/hyperledger/fabric-samples>.
3.  Using dev mode, <https://github.com/hyperledger/fabric-samples/tree/release-1.2/chaincode-docker-devmode>.
