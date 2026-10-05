[README.md](https://github.com/user-attachments/files/33057710/README.md)
# -GOmoneyFX-support
FAQ,Contact：QQ276953059. Email: goqt35@163.com
# 口袋汇率 · PocketFX 1.1

一个简洁的中文 SwiftUI 离线货币换算应用，包含 iOS App、交互式主屏幕小组件、灵动岛 / Live Activities，以及可独立离线运行的 watchOS App。1.1 新增常用货币、可选的位置识别、最近使用币种排序和自定义增减按钮。

[查看 1.1 新功能说明](Docs/CHANGELOG-1.1.md)。定位识别按用户选择采用苹果系统服务，可能需要网络；汇率与手动换算仍完全离线。

**双击 `PocketFX.xcodeproj` 即可打开。无需 XcodeGen、CocoaPods、API Key 或远程 Swift 包。** 请保持整个目录结构完整；`CurrencyCore` 是项目内的本地 Swift Package。

## 功能与平台

| 组件 | 功能 | 最低系统 |
| --- | --- | --- |
| iPhone / iPad | 金额输入、币种搜索、最近使用、常用货币、可选定位识别、自动保存 | iOS 17 |
| 小组件 | 小号、中号；可配置增减按钮、交换、清零 | iOS 17 |
| Live Activity | 锁屏与灵动岛紧凑、最小、展开布局；展开后使用可配置增减按钮换算 | iOS 17 |
| Apple Watch | 独立数字键盘、最近使用币种、换算、保存 | watchOS 10 |

界面适配浅色与深色模式。所有计算使用 `Decimal`；接受最多 12 位整数及 4 位小数，在输出时按币种的小数位数进行四舍五入。交换币种会**保留输入金额**。无效输入显示空结果，不会覆盖小组件上一次有效的金额。

## 汇率快照

- 内置日期：**2026-10-04**，当天取得并按来源日期记录。
- 来源：[fawazahmed0/exchange-api](https://github.com/fawazahmed0/exchange-api)，CC0 1.0。
- 原始快照：[2026-10-04 USD 数据](https://cdn.jsdelivr.net/npm/@fawazahmed0/currency-api@2026-10-04/v1/currencies/usd.json)。项目 `Docs/source-usd-2026-10-04.json` 保存原始响应，`Docs/SOURCE-SHA256.txt` 保存校验值。
- 应用实际资源：`Packages/CurrencyCore/Sources/CurrencyCore/Resources/rates.json`。汇率使用十进制字符串，表示 **1 USD 对应多少该货币**。
- 公式：`目标金额 = 输入金额 ÷ 原币种每美元汇率 × 目标币种每美元汇率`。
- 这是静态参考快照，不是实时行情或银行成交报价。应用不会自动联网更新，今后打开仍使用该日期。部分受管制市场的官方、现金和市场价格可能不同。

例如：100 USD → 670.77 CNY；100 USD → 6,256.30 PHP。内部保留完整精度，界面只舍入显示结果。

共 50 种：CNY、EUR、GBP、USD、HKD、MOP、TWD、JPY、KRW、MNT、SGD、MYR、THB、VND、IDR、PHP、BND、KHR、LAK、MMK、INR、PKR、BDT、LKR、NPR、BTN、MVR、AFN、KZT、UZS、KGS、TJS、TMT、AED、SAR、QAR、KWD、BHD、OMR、JOD、ILS、TRY、IRR、IQD、LBP、YER、AMD、AZN、GEL、RUB。覆盖要求的六种货币以及东亚、东南亚、南亚、中亚和西亚主要货币，未宣称包含每一个亚洲国家的全部货币。

## 用 Xcode 运行

需要安装带 iOS 和 watchOS SDK 的 Xcode。源码使用 Swift 5.9 / Xcode 15 起提供的 API；本次实际验证环境和结果见 `Docs/TESTING.md`，未在所有旧版 Xcode 上验证。

1. 双击 `PocketFX.xcodeproj`。
2. 等待本地 `CurrencyCore` 包解析完成，它不需要下载远程依赖。
3. 顶部选择 **PocketFX** scheme，再选择已安装的 iPhone 模拟器，按 **⌘R**。
4. 首次启动默认是 `100 CNY → HKD`。点击币种打开选择列表，点击金额输入。
5. 测试手表时，选择 **PocketFXWatch** scheme 和 Apple Watch 模拟器，按 **⌘R**。Xcode 中若没有手表设备，请在 Settings → Components / Platforms 安装 watchOS 模拟器运行环境。
6. `PocketFX` 的构建会自动嵌入 Widget Extension 与 Watch App。无需手工重新添加目标或复制源码。

模拟器不需要填写开发者 Team。普通运行让 Xcode 使用其默认的模拟器签名；文末构建命令中的 `CODE_SIGNING_ALLOWED=NO` 仅适用于无需签名的构建验证，不能用于真机。

### 添加主屏幕小组件

先运行一次 iOS App，然后回到主屏幕，长按空白处 → 添加小组件 → 搜索「口袋汇率」→ 选择小号或中号。

- 默认 +10 / +100，可在首页齿轮设置为 ±1、±5、±10、±50、±100、±500、±1K、±5K、±10K。小号显示前 2 项，中号显示最多 4 项；减额最低为 0。
- 双向箭头交换币种且保持金额；回转箭头清零。
- 点按金额打开 App，可输入任意金额或选择其他币种。
- WidgetKit 提供 Button / Toggle 交互，不提供常驻可编辑 TextField。因此完整输入在 App 中完成。
- 所有小组件共用手机上的同一份换算状态，未实现每个小组件独立配置。
- App 和小组件通过 App Group 共享一个小 JSON 文件，保存时请求 WidgetKit 刷新。系统控制实际刷新时机，不能保证每次刷新零延迟。

### 使用灵动岛 / Live Activities

在 App 中点「在灵动岛与锁屏中使用」。返回主屏幕查看；长按灵动岛展开后，使用设置中的增减按钮、交换和清零。修改按钮配置会更新正在运行的实时活动。点击结果返回 App。在 App 中点「结束实时活动」可立即结束。

- 需要支持灵动岛的 iPhone；其他 iPhone 可查看锁屏实时活动。
- 如系统关闭实时活动，App 会显示错误提示。检查系统设置 → App → 口袋汇率 → 实时活动。
- 使用 `ActivityKit` 本地 request / update / end，`pushType: nil`，无需服务器或推送权限。
- 系统决定展示和生命周期，标准实时活动最长活动 8 小时，锁屏可能再保留最多 4 小时。它不是永久悬浮窗，也不能在紧凑灵动岛中放完整数字键盘。
- 锁屏按钮是否可操作取决于设备解锁/认证状态；系统结束后重新进入 App 可以再次开始。

### 使用手表

手表内置相同汇率。点金额打开数字键盘；小屏可上下滚动查看键盘底部的「清零」「完成」。点击原/目标币种打开列表，使用 Digital Crown 滚动。

Watch App 在安装后可独立运行，不需要手机在线。金额与币种保存在**手表自己的本地空间**；本版不实现 WatchConnectivity，不自动同步手机的金额或选择。App Groups 本身也不会跨设备同步。

## 真机签名

项目不包含证书。此次升级保留了你已经在 Xcode 中选择的 Team；如果换用其他账号，请在各 target 中重新选择。

1. 在 Xcode → Settings → Accounts 登录自己的 Apple ID。
2. 编辑 `Config/Project.xcconfig`，将 `BUNDLE_ID_PREFIX` 改为自己独有的标识，例如 `com.yourname.PocketFX`。三个应用标识和 App Group 会随之更新：

   | 用途 | 示例 |
   | --- | --- |
   | iOS App | `com.yourname.PocketFX` |
   | Widget Extension | `com.yourname.PocketFX.widgets` |
   | Watch App | `com.yourname.PocketFX.watchkitapp` |
   | App Group | `group.com.yourname.PocketFX` |

3. 在该配置文件填写 `DEVELOPMENT_TEAM`，或分别在 `PocketFX`、`PocketFXWidgets`、`PocketFXWatch` 的 Signing & Capabilities 中选**同一 Team**并勾选 Automatically manage signing。不要在多个位置填写不同 Team。
4. 为 `PocketFX` 和 `PocketFXWidgets` 启用 **App Groups**，创建并勾选同一个 `group.com.yourname.PocketFX`。它应与配置文件和两个 entitlements 文件的展开值一致。
5. 检查 watchOS 的 `WKCompanionAppBundleIdentifier`，它已引用 `$(BUNDLE_ID_PREFIX)`，应等于 iOS 的 Bundle Identifier。Watch 不需要加入手机 App Group。
6. 连接并信任 iPhone，启用 Developer Mode。手表需与手机配对，并启用相应开发模式。手机选择 PocketFX scheme + iPhone；手表选择 PocketFXWatch scheme + 汤奇的Apple Watch，然后 ⌘R。

App Groups 的可用性取决于开发者账号与配置。如果 Personal Team 报不支持此能力，应使用支持 App Groups 的开发者团队，或先在模拟器运行。不要仅删除 entitlement，否则共享数据与小组件将无法正常工作。无需添加 Push Notifications、Background Fetch 或网络权限。

项目包含三个 target 的隐私清单，没有广告/分析 SDK、后台定位或自建服务器。开启定位后，苹果地理编码服务可能接收本次坐标用于即时识别国家；App 不保存坐标，仅在本地保存设置和国家代码。本次沿用已有开发签名，在你的 Apple Watch 上完成安装、启动和首页检查；没有归档上传或提交 App Store。详见 [Watch 部署检查](Docs/WATCH-DEPLOYMENT.md)。

## 项目结构

```text
PocketFX/
├── PocketFX.xcodeproj/              已配置的 Xcode 工程与共享 schemes
├── Config/Project.xcconfig          集中管理标识、签名与版本
├── Packages/CurrencyCore/
│   ├── Package.swift               仅本地代码，无外部依赖
│   ├── Sources/CurrencyCore/
│   │   ├── RateBook.swift           币种、十进制换算、显示精度
│   │   ├── Conversion.swift         金额校验、状态、存储、深链接
│   │   ├── AppSettings.swift        偏好、最近使用、增减选项
│   │   ├── CountryCurrency.swift    国家/地区到支持币种的映射
│   │   └── Resources/rates.json     单一内置汇率快照
│   └── Tests/CurrencyCoreTests/     换算与持久化测试
├── Shared/
│   ├── ConversionAttributes.swift  App 与 Widget 共用活动状态
│   └── PhoneSession.swift          本地状态、活动生命周期、App Intents
├── iOS/                            手机界面、设置、一次性定位与币种列表
├── Widgets/                        主屏幕小组件和 Live Activity 布局
├── Watch/                          独立 SwiftUI 手表界面与数字键盘
├── Tests/                          iOS 界面自动化测试
└── Docs/                           测试说明、原始数据与许可
```

只有 `Shared` 中的两个文件同时参与 iOS App 与 Widget 编译。Watch 只依赖跨平台 `CurrencyCore`，不引入 ActivityKit 或手机的共享容器。

所有 iPhone 上的快捷操作采用 `LiveActivityIntent`，由系统在主 App 进程执行，因此即使界面不在前台，也能在 MainActor 上串行修改状态、更新活动。它们不会自动启动新的活动。JSON 原子写入避免小组件读到半份内容；Live Activity 更新按顺序提交。

深链接支持 `pocketfx://convert`，以及 `pocketfx://convert?amount=100&from=USD&to=CNY`。代码只接受已知币种、有效非负金额及完整参数，拒绝重复或未知参数。

## 测试命令

以下命令在本目录运行。构建目录使用 `DerivedData`，可自行改为其他临时目录。

```sh
# 不启动模拟器即可测试共享模型
swift test --package-path Packages/CurrencyCore

# 一次构建 iOS App、Widget Extension 和嵌入的 Watch App
xcodebuild -project PocketFX.xcodeproj -scheme PocketFX \
  -configuration Debug -destination 'generic/platform=iOS Simulator' \
  -derivedDataPath DerivedData CODE_SIGNING_ALLOWED=NO build

# 单独构建 Watch scheme
xcodebuild -project PocketFX.xcodeproj -scheme PocketFXWatch \
  -destination 'generic/platform=watchOS Simulator' \
  -derivedDataPath DerivedData CODE_SIGNING_ALLOWED=NO build

# 先查询本机模拟器名称
xcrun simctl list devices available

# 将名称替换为自己安装的 iPhone 模拟器；也可直接在 Xcode 按 ⌘U
xcodebuild -project PocketFX.xcodeproj -scheme PocketFX \
  -destination 'platform=iOS Simulator,name=iPhone 18 Pro Max' \
  -derivedDataPath DerivedData test
```

核心单元测试验证 50 × 50 个币种组合的往返精度、零金额、同币种、输入限制、不同小数分隔符、不同币种显示位数、共享文件保存、损坏偏好恢复、深链接与汇率文件校验。界面测试验证 100 USD → CNY、重启后保留、交换/清零、实时活动开始与结束，以及常用货币、可配置按钮、最近使用排序和定位拒绝后的回退。

`Docs/TESTING.md` 列出实测结果与需要真机检查的项目。截图位于 `Docs/Screenshots`，包含手机首页、设置、最近使用币种及真实手表首页。小组件及 Live Activity 文件中也包含 `#Preview`，可在 Xcode Canvas 预览小号、中号与锁屏布局。

## 将来手动更新汇率

1. 在开发电脑取得有明确日期和使用许可的完整快照。
2. 更新 `rates.json` 的 `asOf`、来源信息、每种货币的 `unitsPerUSD`；保留 USD = 1 和已有币种代码。不要把抓取日期冒充为数据日期。
3. 更新 `Docs` 内原始文件、校验值、来源说明，以及单元测试里的日期/已知样例金额。
4. 运行测试并重新构建、发布应用。iOS、小组件和 Watch 都会使用同一个包内资源。

汇率下载只属于开发阶段的手动操作；工程无联网构建步骤，也无运行时汇率请求。可选的位置识别会调用苹果系统服务，可能需要网络。

## 实现依据

- [Apple：为 Widgets 与 Live Activities 添加交互](https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities)
- [Apple：使用 Live Activities 展示数据与生命周期限制](https://developer.apple.com/documentation/activitykit/displaying-live-data-with-live-activities)
- [Apple：配置 App Groups](https://developer.apple.com/documentation/xcode/configuring-app-groups)
- [Apple：设置 watchOS 工程](https://developer.apple.com/documentation/watchos-apps/setting-up-a-watchos-project)
- [汇率数据项目及许可](https://github.com/fawazahmed0/exchange-api)
