[TOC] 

# VPN

## 常用的VPN加密传输协议

加密协议是客户端和服务器之间的桥梁。

1. shadowsocks

流量很容易被识别，已经废弃。

2. VMESS

3. VLESS

   相对VMESS，VLESS 协议的主要优势是：1. 不需要客户端和服务器时间一致； 2. VLESS 协议不自带加密，使用 TLS 的情况下性能比 VMESS 更好。

   TLS 将是接下来三到五年内躲避封锁的主流方式，例如 V2ray 伪装、trojan、SSRoT 都是基于 TLS。V2ray 之前因性能不好而被一些人唾弃，VLESS 协议的出现，让 V2ray 的便利性和性能达到 trojan 的高度（使用 TLS 的情况下）。

   仅有 VLESS 还不够，套了一层 TLS，V2ray 的性能依然不如 SS、SSR。但随 VLESS 协议而来的还有XTLS，将 V2ray 的性能提升到、甚至超越 SS/SSR 的水平。

4. Trojan

   区别于其他协议的强加密和随机混淆，Trojan它通过模仿https隐藏自身。
   

## XRay 和V2Ray

Xray和V2ray主要负责网络协议、路由等网络通信功能的实现

Xray是V2ray的一个分支(Fork)。Xray项目基于V2ray而来，其支持并且兼容V2ray的配置；

Xray是V2ray的超集。虽然最新版V2ray删除了XTLS，但仍保留VLESS协议。Xray提供完整的VLESS和XTLS支持，目前是V2ray的超集，但后续Xray可能会有会有自己的发展方向；

**XRay的由来**

一个Debian包维护者发现XTLS库的LICENSE不是BSD许可，提了一个issue希望作者 @rprx 能修改方便打包，详见 https://github.com/XTLS/Go/issues/9。由这个issue引发了广泛讨论，rprx认为目前许可不是问题，也有不少人认为协议是立场的体现，各执一词。

最终V2ray(V2fly社区)维护者经过投票确认XTLS不符合V2ray的MIT协议，并在V2ray-core 4.33.0版本移除了XTLS。rprx和其拥护者行动起来，很快就创建了Project X项目和Xray子项目（Xray取名来自XTLS和V2ray的结合），并发布了Xray-core的多个版本。这便是Xray的大致由来。

## XTLS

当我们使用基于 TLS 的代理浏览 HTTPS 网站时，其实是两层 TLS：外层是代理的 TLS，内层是网站、APP 的 TLS。

 XTLS 就是无缝拼接了两条货真价实的 TLS，因此绝大多数流量无需二次加解密了。 

## 如何选择

- VLESS+XTLS
- trojan，轻量级的伪装协议
- trojan+XTLS，trojan加强版，使用XTLS技术提升性能

