# 优选订阅生成器 WorkerVless2sub

### 这个是一个通过 Cloudflare Workers 搭建，自动生成优选线路 VLESS 节点订阅内容生成器


### 更新

- 完善分流规则
- 替换测速地址
- 支持设置多个cf workers
- 开启chromego 白嫖
- 强迫症修复


# Workers 部署方法 [视频教程](https://youtu.be/AtCF7eq0hcE)

### 1. 部署 Cloudflare Worker：

   - 在 Cloudflare Worker 控制台中创建一个新的 Worker。
   - 将 [worker.js](https://github.com/fireinrain/WorkerVless2sub/blob/main/_worker.js)  的内容粘贴到 Worker 编辑器中。


### 2. 添加你的专属优选线路：

**2.1 修改 addresses 参数示例**

 - 修改 `addresses` 参数添加本地静态的优选线路，若不带端口号默认8443，不支持生成非TLS订阅，#号后为备注别名，例如：
	```js
	let addresses = [
		'icook.tw:2053#优选域名',
		'cloudflare.cfgo.cc#优选官方线路',
		'185.221.160.203:443#电信优选IP',
	];
	```
	该方式仅推荐添加优选域名的部分，频繁变更的优选推荐通过 `addressesapi` 来实现。


 **2.2 修改 addressesapi 参数示例**
 
 - 修改 `addressesapi` 参数，在脚本中设置 `addressesapi` 变量为 **优选IP地址txt文件** 的 URL。例如：
	```js
	let addressesapi = [
		'https://raw.githubusercontent.com/fireinrain/WorkerVless2sub/main/cf-spdt-address-api.txt',
 		'https://addressesapi.090227.xyz/CloudFlareYes',
	];
	```
	可参考 [addressesapi.txt](https://raw.githubusercontent.com/fireinrain/WorkerVless2sub/main/cf-spdt-address-api.txt) 内容格式 自行搭建。


<details>
<summary><code><strong>「 我不是小白！我有IP库！我知道IPtest是什么！我也有csv测速文件！ 」</strong></code></summary>

 
  **2.3 修改 addressescsv 参数示例**
  
 - 修改 `addressescsv` 参数，在脚本中设置 `addressescsv` 变量为 **iptest测速结果csv文件地址** 的 URL。例如：
	```js
	let DLS = 4;//速度下限
	let addressescsv = [
		'https://raw.githubusercontent.com/fireinrain/WorkerVless2sub/main/cf-spdt-address.csv',
	];
	```
	`DLS` 为要求满足的最低速度，不满足改数值以上的IP将不会添加至优选订阅内容。注意：不考虑单位，只看数值，请按照您的测速结果而定。

 </details>


### 3. 修改 快速订阅入口 以及 默认内置 Vless 节点信息：

  例如您的workers项目域名为：`sub.cmliussss.workers.dev`；
   - 添加 `TOKEN` 变量，快速订阅访问入口，默认值为: `auto` ，获取订阅器默认节点订阅地址即 `/auto` ，例如 `https://sub.cmliussss.workers.dev/auto`
   - 添加 `HOST` 变量，例如 `edgetunnel-2z2.pages.dev`；
   - 添加 `UUID` 变量，例如 `30e9c5c8-ed28-4cd9-b008-dc67277f8b02`；
   - 添加 `PATH` 变量，例如 `/?ed=2048`；



# 订阅生成器 使用方法 [视频教程](https://youtu.be/OjqCKeEY7DQ)

  例如您的workers项目域名为：`sub.cmliussss.workers.dev`；
  
### 1. 快速订阅

   - 添加 `TOKEN` 变量，快速订阅访问入口，默认值为: `auto` ，获取订阅器默认节点订阅地址即 `/auto` ，例如：
     ```url
     https://sub.cmliussss.workers.dev/auto
     ```
   - 添加sos参数开启chrome-go白嫖节点例如：
	  ```url
	  https://sub.cmliussss.workers.dev/auto?sos=true
	  ```
     
### 2. 自定义订阅-单节点模式

   - **自定义订阅格式** `https://[你的Workers域名]/sub?host=[你的Vless域名]&uuid=[你的UUID]&path=[你的ws路径]`
   - **host**：您的 VLESS 伪装域名，例如 `edgetunnel-2z2.pages.dev`；
   - **uuid**：您的 VLESS 客户端 UUID，例如 `30e9c5c8-ed28-4cd9-b008-dc67277f8b02`；
   - **path**（可选）：您的 VLESS 的 WS 路径（没有可留空不填），例如 `/?ed=2048`。
   - **sos**（可选）：开启chrome-go白嫖节点，例如 `sos=true`。

   - 自定义订阅地址如下：
     ```url
     https://sub.cmliussss.workers.dev/sub?host=edgetunnel-2z2.pages.dev&uuid=30e9c5c8-ed28-4cd9-b008-dc67277f8b02&path=/?ed=2048&sos=true
     ```
   - 注意路径必须包含 "/sub"。

### 3. 自定义订阅-多节点模式

- **自定义订阅格式** `https://[你的Workers域名]/sub?host=[你的Vless域名]&uuid=[你的UUID]&path=[你的ws路径]&sos=true&cluster=true`
- **cluster**：开启多worker节点模式，例如 `cluster=true`
- **host**：您的 VLESS 伪装域名(多个)，例如 `edgetunnel-2z2.pages.dev,your-worker-host2,your-worker-host3`；
- **uuid**：您的 VLESS 客户端 UUID(多个)，例如 `30e9c5c8-ed28-4cd9-b008-dc67277f8b02,your-uuid2,your-uuid3`；
- **path**（可选）：您的 VLESS 的 WS 路径（没有可留空不填），例如 `/?ed=2048`。
- **sos**（可选）：开启chrome-go白嫖节点，例如 `sos=true`。

- 自定义订阅地址如下：
  ```url
  https://sub.cmliussss.workers.dev/sub?host=edgetunnel-2z2.pages.dev,your-worker-host2,your-worker-host3&uuid=30e9c5c8-ed28-4cd9-b008-dc67277f8b02,your-uuid2,your-uuid3&path=/?ed=2048&sos=true&cluster=true
  ```
- 注意路径必须包含 "/sub"。
- 注意多个host和多个uuid需要一一对应，不能对应错了，错了就连不上

### 4. 指定 clash、singbox 配置文件(适用于两种模式，不多赘述)

   - 添加 `format=clash` 键值，获取 clash 订阅配置，例如：
     ```url
     https://sub.cmliussss.workers.dev/auto?format=clash
     https://sub.cmliussss.workers.dev/sub?format=clash&host=edgetunnel-2z2.pages.dev&uuid=30e9c5c8-ed28-4cd9-b008-dc67277f8b02&path=/?ed=2048
     ```
     
   - 添加 `format=singbox` 键值，获取 singbox 订阅配置，例如：
     ```url
     https://sub.cmliussss.workers.dev/auto?format=singbox
     https://sub.cmliussss.workers.dev/sub?format=singbox&host=edgetunnel-2z2.pages.dev&uuid=30e9c5c8-ed28-4cd9-b008-dc67277f8b02&path=/?ed=2048
     ```
     
### 变量说明
| 变量名 | 示例 | 备注 | 
|--------|---------|-----|
| TOKEN | auto | 快速订阅内置节点的订阅路径地址 /auto | 
| HOST | edgetunnel-2z2.pages.dev | 快速订阅内置节点的伪装域名 | 
| UUID | 30e9c5c8-ed28-4cd9-b008-dc67277f8b02 | 快速订阅内置节点的UUID | 
| PATH | /?ed=2048 | 快速订阅内置节点的路径信息 | 
| TGTOKEN | 6894123456:XXXXXXXXXX0qExVsBPUhHDAbXXXXXqWXgBA | 发送TG通知的机器人token | 
| TGID | 6946912345 | 接收TG通知的账户数字ID | 
| SUBAPI | api.v1.mk | clash、singbox等 订阅转换后端 | 
| SUBCONFIG | [https://raw.github.../ACL4SSR_Online_Full_MultiMode.ini](https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online_Full_MultiMode.ini) | clash、singbox等 订阅转换配置文件 | 

## Star 星星走起
[![Stargazers over time](https://starchart.cc/fireinrain/WorkerVless2sub.svg?variant=adaptive)](https://starchart.cc/cmliu/WorkerVless2sub)

Telegram交流群：[@Euler-Share](https://t.me/eulershare) [@CMLiussss](https://t.me/CMLiussss)
# 感谢
我自己的脑洞，[cmliu](https://github.com/cmliu), [SAKURA-YUMI](https://github.com/SAKURA-YUMI)，[EzSync](https://github.com/EzSync)


