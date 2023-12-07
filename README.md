# 巨魔商店

TrollStore 是一个永久签名的监禁应用程序，可以永久安装您在其中打开的任何 IPA。

它之所以有效，是因为 AMFI/CoreTrust 错误，其中 iOS 不会验证用于签署二进制文件的根证书是否合法。

## 安装 TrollStore

### 安装指南

| 版本/设备 | ARM64（A8 - A11）| ARM64E（A12-A17，M1-M2）|
| ---| ---| ---|
| 13.7 及以下 | 不支持（这两个 CT Bug 仅在 14.0 中引入）|
| 14.0 - 14.8.1 | [checkra1n + TrollHelper](./install_trollhelper.md) | [TrollHelperOTA (arm64e)](./install_trollhelperota_arm64e.md) |
| 15.0 - 15.4.1 | [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md)  [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md) |
| 15.5 测试版 1 - 4 | [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md)  [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md) |
| 15.5 | 15.5 即将推出 | 即将推出 |
| 15.6 测试版 1 - 5 | [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md) | [TrollHelperOTA (iOS 15+)](./install_trollhelperota_ios15.md) |
| 15.6 - 16.5 | 15.6 - 16.5 即将推出 | 即将推出 |
| 16.5.1 - 16.6.1 | 即将推出 | 无安装方法 |
| 16.7 - 16.7.2 | 不支持（已修复两个 CT 错误）| 不支持（已修复两个 CT 错误）|
| 17.0 | 17.0 即将推出 | 无安装方法 |
| 17.0.1 及更新版本 | 不支持（已修复两个 CT 错误）| 不支持（已修复两个 CT 错误）|

由于发现了新的 CoreTrust 漏洞，未来将添加对 15.5 - 16.6.1 和 17.0 的支持。如果您需要 TrollStore，请继续使用这些版本。16.7 和 17.0.1+ 将永远不受支持（除非 Apple 第三次搞砸 CoreTrust...）。

## 更新 TrollStore

当新的 TrollStore 更新可用时，安装该更新的按钮将出现在 TrollStore 设置的顶部。点击按钮后，TrollStore 将自动下载更新、安装并重新启动。

或者（如果出现任何问题），您可以在 Releases 下下载 TrollStore.tar 文件并在 TrollStore 中打开它，TrollStore 将安装更新并重新启动。

## 卸载应用程序

从 TrollStore 安装的应用程序只能从 TrollStore 本身卸载，点击应用程序或在“应用程序”选项卡中向右滑动即可将其删除。

## 持久化助手

TrollStore 中使用的 CoreTrust 错误仅足以安装“系统”应用程序，这是因为 FrontBoard 每次在启动用户应用程序之前都会进行额外的安全检查（它调用 libmis）。不幸的是，无法安装通过图标缓存重新加载而保留的新“系统”应用程序。因此，当 iOS 重新加载图标缓存时，所有 TrollStore 安装的应用程序（包括 TrollStore 本身）都将恢复为“用户”状态，并且将不再启动。

解决此问题的唯一方法是将持久性助手安装到系统应用程序中，然后可以使用该助手将 TrollStore 及其安装的应用程序重新注册为“系统”，以便它们再次可启动，TrollStore 中提供了此选项设置。

在越狱的 iOS 14 上，当使用 TrollHelper 进行安装时，它位于 /Applications 中，并将通过图标缓存重新加载作为“系统”应用程序持续存在，因此 TrollHelper 被用作 iOS 14 上的持久性助手。

## URL方案

从版本 1.3 开始，TrollStore 取代了系统 URL 方案“apple-magnifier”（这样做是为了“越狱”检测无法像 TrollStore 具有唯一的 URL 方案那样检测到 TrollStore）。此 URL 方案可用于直接从浏览器安装应用程序，格式如下：

`apple-magnifier://install?url=<URL_to_IPA>`

在未安装 TrollStore (1.3+) 的设备上，这只会打开放大镜应用程序。

＃＃ 特征

IPA 内的二进制文件可以具有任意权利，使用 ldid 和您想要的权利（`ldid -S<path/to/entitlements.plist> <path/to/binary>`）对它们进行伪造，并且 TrollStore 将在退出时保留权利他们在安装时使用假根证书。这为您提供了很多可能性，其中一些将在下面进行解释。

### 禁止的权利

A12+ 上的 iOS 15 禁止了以下三项与运行未签名代码相关的权利，如果没有 PPL 绕过，这些权利是不可能获得的，使用它们签名的应用程序将在启动时崩溃。

`com.apple.private.cs.debugger`

`动态代码设计`

`com.apple.private.skip-library-validation`

### 取消沙箱

您的应用程序可以使用以下权利之一以非沙盒方式运行：

```xml
<key>com.apple.private.security.container-required</key>
<假/>
````

```xml
<key>com.apple.private.security.no-container</key>
<真/>
````

```xml
<key>com.apple.private.security.no-sandbox</key>
<真/>
````

如果您仍然需要为应用程序使用沙箱容器，建议使用第三种。

您可能还需要平台应用程序权利才能使它们正常工作：

```xml
<key>平台应用</key>
<真/>
````

请注意，平台应用程序权限会导致副作用，例如沙箱的某些部分变得更严格，因此您可能需要额外的私有权限来规避这种情况。（例如，之后您需要为要访问的每个 IOKit 用户客户端类提供例外权利）。

为了使具有“com.apple.private.security.no-sandbox”和“platform-application”的应用程序能够访问其自己的数据容器，您可能需要额外的权利：

```xml
<key>com.apple.private.security.storage.AppDataContainers</key>
<真/>
````

### 根助手

当您的应用程序未沙盒时，您可以使用 posix_spawn 生成其他二进制文件，您还可以使用以下权利以 root 身份生成二进制文件：

```xml
<key>com.apple.private.persona-mgmt</key>
<真/>
````

您还可以将自己的二进制文件添加到应用程序包中。

之后，您可以使用 TSUtil.m 中的 [spawnRoot 函数](./Shared/TSUtil.m#L77) 以 root 身份生成二进制文件。

### 使用 TrollStore 无法实现的事情

- 获得适当的平台化（`TF_PLATFORM`/`CS_PLATFORMIZED`）
- 生成启动守护进程（需要“CS_PLATFORMIZED”）
- 将调整注入系统进程（需要“TF_PLATFORM”、用户态 PAC 旁路和 PMAP 信任级别旁路）

## 学分和延伸阅读

[@LinusHenze](https://twitter.com/LinusHenze/) - 发现了允许 TrollStore 工作的 CoreTrust 错误。

[Fugu15 演示](https://youtu.be/rPTifU1lG7Q)

[撰写有关 CoreTrust 错误的更多信息](https://worthdoingbadly.com/coretrust/)。
