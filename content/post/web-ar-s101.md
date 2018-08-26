+++
title = "Web AR 101"
author = "Junahan"
date = 2018-08-12
keywords = ["web", "ar"]
lastmod = 2018-08-26T09:38:08+08:00
tags = ["WebAR"]
categories = ["AR"]
draft = false
+++

## <span class="section-num">1</span> 摘要 {#摘要}

通过 Web 浏览器实现 VR/AR (虚拟显示/增强现实) 具有如此强大的吸引力，Web 标准组织 W3C 和工业界（如 Google）分别在标准和框架实践两个方面推动项技术的发展。相关标准和 AR 框架的介绍，请参阅由 [GeekPlux](https://geekplux.com/2018/01/18/augmented-reality-development-tech-in-web-frontend.html) 撰写的文章 - [Web 前端中的增强现实 (AR) 开发技术](https://geekplux.com/2018/01/18/augmented-reality-development-tech-in-web-frontend.html)。


## <span class="section-num">2</span> 技术框架 {#技术框架}


### <span class="section-num">2.1</span> WebARonARKit 和 WebARonARCore {#webaronarkit-和-webaronarcore}

-   [WebARonARKit](https://github.com/google-ar/WebARonARKit) - 是一个实验性的 iOS 应用，用于开发者使用 web 技术创建 AR 应用实验。
-   [WebARonARCore](https://github.com/google-ar/WebARonARCore) - 是一个实验性的 Android 应用, 用于开发者使用 web 技术创建 AR 应用实验。


### <span class="section-num">2.2</span> Three.ar.js {#three-dot-ar-dot-js}

[Thee.ar.js](https://github.com/google-ar/three.ar.js) 是一个 [three.js](https://threejs.org/) 库的辅助类库，其运行于 [WebARonARKit](https://github.com/google-ar/WebARonARKit) 和 [WebARonARCore](https://github.com/google-ar/WebARonARCore)，用于构建 Web AR 应用。


### <span class="section-num">2.3</span> AR.js {#ar-dot-js}

[AR.js](https://jeromeetienne.github.io/AR.js/) 是一个 Web AR 框架，作者 [Jerome Etienne](https://twitter.com/jerome%5Fetienne)。该框架特点：

-   快速：可以高效的运行在智能手机上 (60 fps) ；
-   基于 Web：完全基于 Web，无需安装。 纯 javascrip 实现，基于 three.js + jsartoolkit5；
-   开源：完全开源且免费；
-   标准：工作于任何支持 [webgl](http://caniuse.com/#feat=webgl) 和 [webrtc](http://caniuse.com/#feat=stream) 的智能手机；


## <span class="section-num">3</span> 推荐阅读 {#推荐阅读}

1.  [Web 前端中的增强现实 (AR) 开发技术](https://geekplux.com/2018/01/18/augmented-reality-development-tech-in-web-frontend.html) by [GeekpPlux](https://geekplux.com/2018/01/18/augmented-reality-development-tech-in-web-frontend.html) - 该文详细的介绍了 Web AR 相关标准 (W3C [WebVR  Group](https://www.w3.org/community/webvr/)) 和两个重要框架 ([three.ar.js](https://github.com/google-ar/three.ar.js) 和 [ar.js](https://github.com/jeromeetienne/AR.js)) 的进展。
2.  [WebAR for Augmented Reality](https://medium.com/arjs/webvr-for-augmented-reality-f1e69a505902) by [Jerome Etienne](https://medium.com/@jerome%5Fetienne) - 该文介绍了为什么 WebVR 相关标准和技术也适用于 WebAR 并倡导融合标准和框架。
3.  [Web 前端也能做的 AR 互动](http://tgideas.qq.com/webplat/info/news%5Fversion3/804/7104/7106/m5723/201612/537832.shtml) by [Tgideas](http://tgideas.qq.com/webplat/info/news%5Fversion3/804/7104/7106/m5723/201612/537832.shtml) - 该文结合一个实际的项目介绍了在浏览器上实现 AR 互动的经验和遇到的问题。


## <span class="section-num">4</span> 参考文献 {#参考文献}

1.  WebVR，<https://webvr.info/>.
2.  W3 WebVR 工作组, <https://www.w3.org/community/immersive-web/>.
3.  Web 前端中的增强现实（AR）开发技术，<https://geekplux.com/2018/01/18/augmented-reality-development-tech-in-web-frontend.html>.
4.  WebVR for Argmented Reality, <https://medium.com/arjs/webvr-for-augmented-reality-f1e69a505902>.
5.  WebVR API extendsion for smartphone AR, <https://github.com/google-ar/three.ar.js/blob/master/webvr%5Far%5Fextension.md>.
6.  AR.js - Augmented Reality for the Web, <https://jeromeetienne.github.io/AR.js/>.
7.  WebRTC, <https://webrtc.org>.
8.  Web 前端也能做的 AR 互动, <http://tgideas.qq.com/webplat/info/news%5Fversion3/804/7104/7106/m5723/201612/537832.shtml>.
9.  WebARonARKit, <https://github.com/google-ar/WebARonARKit>.
10. WebARonARCore, <https://github.com/google-ar/WebARonARCore>.
