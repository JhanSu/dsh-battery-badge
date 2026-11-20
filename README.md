# dsh-battery-badge

DeepSeek Harness 侧栏设备徽标：**笔记本显示电量，台式机和 NAS 显示设备图标。**

装在侧栏底部账号那一行的右侧，一眼就能看出这台终端要不要充电。

```
[头像] 森度          MacBook Air M1  94%  ⚡🔋
```

## 它做什么

| 检测到的设备 | 显示内容 |
|---|---|
| **笔记本**（MacBook / Windows 笔记本） | 机型 + 电量百分比 + 电池图标（充电时显示闪电，满电/接电显示插头） |
| **iMac** | 机型 + iMac 图标 |
| **Mac mini / Mac Studio / Mac Pro** | 机型 + Mac mini 图标 |
| **Windows 台式机** | 机型 + 台式机图标 |
| **NAS**（群晖 / 极空间 / 绿联 / 飞牛） | 机型 + 对应品牌图标 |

只有检测到电池的设备才显示百分比——台式机和 NAS 不会出现「—%」这种噪音。

## 安装

### 从 GitHub 安装

```sh
dsh plugin --profile <你的profile> add github:JhanSu/dsh-battery-badge
```

桌面端请在 **设置 → 插件** 里通过插件管理器安装，或使用 dshmarket 市场。

### 本地 tgz 安装

```sh
npm pack
dsh plugin --profile <你的profile> add ./dsh-battery-badge-1.0.0.tgz
```

装好后刷新页面（或重启 DSH）即可看到。

## 外观

- **机型名**在前，**电量百分比**居中，**电池图标**在最后
- 电池图标按 macOS 菜单栏样式绘制：随电量填充，充电时叠加闪电，满电时显示插头
- 浅色/深色主题自动适配：线条用 `currentColor`，跟随界面主色
- 侧栏收起为图标栏时自动隐藏（56px 放不下文字）
- 徽标区域 `pointer-events: none`，不挡账号菜单的点击

## 数据来源与隐私

**完全本地，不联网、不上传、不写盘。**

| 平台 | 读取内容 |
|---|---|
| macOS | `pmset -g batt`（电量/充电状态/剩余时间）、`sysctl -n hw.model`（机型代号） |
| Linux | `/sys/class/power_supply/*`（电池）、`/sys/class/dmi/id/*`（厂商/型号）、`/etc/os-release` |
| Windows | `Win32_Battery`（电量）、`Win32_ComputerSystem`（型号） |

**不读取**：序列号、硬件 UUID、Provisioning UDID、账号信息、任何文件内容。

宿主路由 `/api/battery-badge/status` 只监听回环地址，并要求有效的 DSH 登录会话（通过 `connection.requestRejection` 校验）。任何人都无法从外部访问。

## 刷新机制

1. **浏览器 Battery API 事件（主）**：插拔电源、电量变化时立即触发
2. **20 秒兜底轮询**：用于不支持 Battery API 的浏览器，以及剩余时间的文字递减

宿主侧缓存 1 秒，并支持 `?fresh=1` 强制刷新。

## 许可证

MIT
