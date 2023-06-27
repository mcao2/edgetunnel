# V2ray Edge

众所周知，V2ray 是基于 `go` 的，导致原版 V2ray 无法部署到基于 `javaScript (V8)` 的平台上。

本项目通过，使用 `js` 实现 `VLESS`协议， 使得 **V2ray** 可以部署到一些 Edge 或者 Serverless 平台上。

## Deno 部署

由于 deno deploy 的[限制](https://deno.com/deploy/docs/pricing-and-limits#tls-proxying)，免费用户目前无法部署。
[Deno deploy Install](./doc/edge-tunnel-deno.md)

## Cloudflare Worker 部署

参考这份[图文教程](https://www.igfw.net/archives/14264)，但是需要 fork 本项目。

> 这个不是利用 Worker 进行反代， 而是直接部署 V2ray （js 版本）到 Worker 上。

## Node.js 平台或 VPS 部署

很多 Node.js 的平台都是支持 docker 的，所以可以直接部署原版。但是既然很多人要，我就写一个。我目前仅仅维护 render 平台的文档。理论上其他平台都一样。

### ~~render.com~~

> 貌似 Render 封号。
> Seems Render also banned this project..

~~[render](./doc/render.md)~~

### Docker

```bash
docker run -d -p 4600:4100 -e UUID=ce6d9073-7085-4cb1-a64d-382489a2af94 zizifn/node-vless:latest
```

> 如果你想让 DNS IPV4 优先， 请设置环境变量 DNSORDER=ipv4first

### Command

```bash
export UUID=ce6d9073-7085-4cb1-a64d-382489a2af94 PORT=4100 node  ./dist/apps/node-vless/main.js
```

小内存：

```bash
export UUID=ce6d9073-7085-4cb1-a64d-382489a2af94 PORT=4100 SMALLRAM=true node  ./dist/apps/node-vless/main.js
```

> 如果你想让 DNS IPV4 优先， 请设置环境变量 DNSORDER=ipv4first

## 客户端 v2rayN 配置

> ⚠️ 由于 edge 平台限制，无法转发 UDP 包。请在配置时候，把 DNS 的策略改成 "Asis", 否则会影响速度。
> 请不要开启 ipv6 优先。

> [ DNS 科普文章](https://tachyondevel.medium.com/%E6%BC%AB%E8%B0%88%E5%90%84%E7%A7%8D%E9%BB%91%E7%A7%91%E6%8A%80%E5%BC%8F-dns-%E6%8A%80%E6%9C%AF%E5%9C%A8%E4%BB%A3%E7%90%86%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8-62c50e58cbd0)

### Windows 版本

https://github.com/2dust/v2rayN
别人的配置教程参考，https://v2raytech.com/v2rayn-config-tutorial/.

具体配置，请参考部署服务的主页。

### 安卓

[v2rayNG](https://github.com/2dust/v2rayNG)

[SagerNet](https://github.com/SagerNet/SagerNet)

如果遇到安卓无法使用, 请参考如下配置，多尝试下 DNS 设置。

v2rayNG 设置。
![andriod-v2ray](./doc/andriod_v2rayn.jpg)

### iOS

> 需要非中国区 Apple ID

* [Shadowrocket](https://apps.apple.com/us/app/shadowrocket/id932747118)
* [V2Box](https://apps.apple.com/us/app/v2box-v2ray-client/id6446814690)

## 建立 cloudflare worker 反代 （可选）

```js
const targetHost = 'xxx.xxxx.dev'; //你的 edge function 的hostname
addEventListener('fetch', (event) => {
  let url = new URL(event.request.url);
  url.hostname = targetHost;
  // url.protocol = 'http';
  // url.pathname = '/index';
  // url.port = '443';
  let request = new Request(url, event.request);
  event.respondWith(fetch(request));
});
```

优选 IP https://github.com/XIU2/CloudflareSpeedTest

# FAQ

## 那些平台可以使用？

判断一个平台是否可以支持的，有 2 个必要条件，

1. 是否支持 websocket？
   - 或者支持，HTTP request stream 也是可以的。https://developer.chrome.com/articles/fetch-streaming-requests/
2. 可以创建 raw tcp socket？

> Cloudflare Worker 虽然支持 websocket，但是 Worker 的 runtime 没有支持 创建 raw tcp socket 的 API。

## 不支持 UDP

由于 edge 平台限制，无法转发 UDP 包。所以 DNS 策略请设置成 `Asis`.

## 不支持 VMESS

VMESS 协议过于复杂，并且所有 edge 平台都支持 HTTPS， 所以无需 VMESS.

# 反馈与交流

如果有问题，请使用 https://t.me/+QP9HBh7W3sF0Iow4 进行交流。
