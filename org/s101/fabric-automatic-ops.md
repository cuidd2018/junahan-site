
# Table of Contents

1.  [摘要](#org79400d3)
2.  [基本思路](#orgfc233c9)
3.  [Hyperledger Cello](#org9b83e54)
4.  [MISC](#orgf38dda4)



<a id="org79400d3"></a>

# 摘要

本文主要关注在 k8s<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup> 环境下 Fabric<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup> 的自动化运维。[Hyperledger Cello](https://github.com/hyperledger/cello)<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup> 支持在多种基础设施上（包括 k8s）部署和自动化运维 Fabric。其中项目 fabric-chart<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup> (by LordGoodman) 支持使用 Helm<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup> 来简化 Fabric 网络的部署和运维。


<a id="orgfc233c9"></a>

# 基本思路

参阅 [Hyperledger Cello](https://github.com/hyperledger/cello) 的方案，我们倾向于基于 k8s 做自动化运维，初步选择两种实现方案：

-   使用 yaml 模版生成相应的 k8s 调度配置，并通过 k8s 原始 API 进行调度和管理；
-   使用 Helm 简化配置，并通过 Helm API 进行调度和管理，好处是可能更加简单一些；


<a id="org9b83e54"></a>

# Hyperledger Cello

[Hyperledger Cello](https://github.com/hyperledger/cello) 是超级账本的一个工具项目。Hyperledger Cello is a blockchain provision and operation system, which helps manage blockchain networks in an efficient way. 


<a id="orgf38dda4"></a>

# MISC

-   一篇介绍使用 Helm 部署 Fabric 的文章 - [使用 Helm Chart 在 Kubernetes 上部署 Fabric](https://github.com/hainingzhang/articles/blob/master/fabric_helm/helmChartForFabric.pdf)。
-   Kubernetes 中文指南 - <https://jimmysong.io/kubernetes-handbook/>.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Kubernetes - <https://kubernetes.io/>.

<sup><a id="fn.2" href="#fnr.2">2</a></sup> Fabric - <https://www.hyperledger.org/projects/fabric>.

<sup><a id="fn.3" href="#fnr.3">3</a></sup> Hyperledger Cello - <https://github.com/hyperledger/cello>.

<sup><a id="fn.4" href="#fnr.4">4</a></sup> fabric-chart - <https://github.com/LordGoodman/fabric-chart>.

<sup><a id="fn.5" href="#fnr.5">5</a></sup> Helm - <https://github.com/helm/helm>
