# NapCat Go

一键部署 NapCat 并接入 AstrBot 的插件，自动完成下载、安装、启动、QQ 登录与反向 WebSocket 配置。

支持 Windows / Linux 双平台：

- Windows：使用 NapCat 的 Windows 启动器，自动定位已安装的 QQNT
- Linux：使用本地 deb 安装或 Docker 部署，需要 sudo 权限

> Linux 本地模式可能需要重装 QQ，以匹配 NapCat 要求的兼容版本。插件在检测到版本不兼容时会在操作前提示数据风险，确认后才会继续。

> 本插件只供应 **NapCat 4.18.5** 单一稳定版本，不再跟随上游更新。上游频繁发版且新版会较大概率触发 QQ 登录失效（被踢下线），因此版本固定，安装包由本仓库 Release 长期备份。

## 前置要求

1. Windows 已安装 **QQNT 9.9.27+**；Linux 已配置好 sudo 密码（安装、启动、同步会用到 apt 与提权命令）
   - Linux 本地模式可在插件页面「配置 → 运行方式」保存 sudo 密码，之后启动、安装、同步会自动提权，不再每次弹窗
   - 密码仅在本地加密保存，不会写日志或回显
2. Python 环境（AstrBot 自带），依赖见 `requirements.txt`
3. 已经在 AstrBot 中创建好 aiocqhttp 机器人
4. Linux 模式使用的 `QQ_3.2.34_Linux_amd64.deb` 与 `NapCat.Shell.zip`（NapCat 4.18.5）均来自本插件 GitHub Release 的固定版本备份，原始来源为官方安装包，仅用于保证兼容版本可复现下载

## 安装插件

1. 在 AstrBot 插件市场安装本插件，或手动放入 `data/plugins/`
2. 重载插件
3. 打开 WebUI → 插件 → NapCat Go → 打开页面

## 首次使用

1. 页面点「启动」按钮
   - 插件会自动检测 QQ 是否安装
   - 检测 NapCat 是否已下载；没有则自动从 GitHub Release 下载 `NapCat.Shell.zip` 并解压到插件数据目录
   - 未安装 NapCat 时，电源按钮会显示「安装 NapCat」，点击后进入安装流程
2. 等待日志区出现 `WebUi User Panel Url: http://127.0.0.1:6099/webui?token=xxx`
3. 点「在浏览器打开 WebUI」按钮，在浏览器完成 QQ 扫码登录
4. 登录成功后，插件会自动：
   - 读取你在 AstrBot 中已创建好的 aiocqhttp 机器人配置
   - 将反向 WS 地址写入 NapCat 的 `onebot11_*.json`
   - 重启 NapCat 使配置生效
5. 下载源均使用官方或镜像站点，并会做 SHA256 / ZIP 完整性校验

## 指令

- `/napcat`：输出中文运行状态，包括运行平台、部署方式、QQ 版本与兼容性、NapCat 状态、登录状态和配置目录

## 常见问题

- **启动后一直显示「未安装 QQ」**：请从 https://im.qq.com 安装最新版 QQNT
- **端口被占用**：修改配置里的 `napcat_port`，或在启动前用「清理残留进程」按钮清理
- **插件重载卡住**：点「清理残留进程」按钮，或命令行执行 `taskkill /F /IM NapCatWinBootMain.exe`
- **出现 NapCat 下载失败时**，请到 releases 界面手动下载并放入插件数据目录：

```text
%USERPROFILE%\.astrbot\data\plugins_data\pulid_napcat_go_to_astrbot
```

```text
https://github.com/NapNeko/NapCatQQ/releases
```

## 数据与权限说明

- 插件配置与 NapCat 文件保存在 AstrBot 的 `data/plugin_data/pulid_napcat_go_to_astrbot` 下，更新或重装插件不会覆盖
- Linux 本地模式需要执行 `apt`、`dpkg`、`chown` 等命令，因此会请求 sudo 权限
- sudo 密码可选保存，保存时使用机器相关密钥加密后写入插件配置，仅在执行提权命令时解密使用

## 致谢

- 界面布局与交互参考同作者的 [Snowluma Go](https://github.com/Shou-Lu/Snowluma-Go) 插件，样式表复用自该项目
- NapCat 项目：https://github.com/NapNeko/NapCatQQ

## 许可

AGPL-3.0，详见 [LICENSE](LICENSE)。
