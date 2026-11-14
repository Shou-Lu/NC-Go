# 7.6.1
1. 版本号更新为 7.6.1
2. 修复安全提示：`webui.json` 不再写入固定默认 token，首次创建时随机生成 32 位十六进制 token；检测到旧固定值 `5503af8581de` 会自动轮换（沿用用户自己改过的 token）
3. 收紧残留进程清理范围：不再使用 `napcat|NapCat|Xvfb|launcher.sh|/opt/QQ` 这类宽泛的 `pkill -f`，改为先按精确模式扫描 PID（限定本插件安装目录、`napcat.mjs`、插件生成的 `launcher.sh`、`/opt/QQ/`、插件 launcher 启动的 Xvfb 屏幕参数），再对命中 PID 执行 kill
4. QQ 进程识别改为精确进程名匹配（`pgrep -x qq`），避免命令行里含 `qq` 的其他进程被误判
5. 清理 `napcat_manager.py` 中未使用的导入

# 7.6.0
1. 版本号更新为 7.6.0
2. NapCat 改为单一稳定版本供应：固定使用 4.18.5，不再查询上游 GitHub API，也不再提供 `latest` 选项；上游频繁发版且新版较大概率触发 QQ 登录失效（被踢下线）
3. 安装包改由本仓库 Release（`napcat-v4.18.5`）长期备份，下载后按固定 SHA256 校验，镜像下载同样受该校验约束
4. 版本选择器只显示 `NapCat 4.18.5（稳定版）`，后端忽略任何非 4.18.5 的安装请求，并把旧配置里的版本自动改回 4.18.5
5. 下载与安装流程不再依赖 GitHub API；API 不可达时也能直接安装固定版本
6. 为避免混淆，页面与 README 明确说明「只供应 4.18.5 稳定版，不跟随上游更新」
7. 旧默认加速镜像 `gh.zwy.one` 已失效，默认镜像改为 `githubproxy.cc` 并调整镜像回退顺序；仍在使用旧默认值的配置会自动切换
8. README 补充说明：安装包内的原生库覆盖 Windows x64 与 Linux x64，与插件支持的平台一致

# 7.5.1
1. 版本号更新为 7.5.1
2. `/napcat` 指令状态输出改为中文，包含平台、部署方式、QQ 版本与兼容性、NapCat 状态、登录状态和配置目录
3. 插件包改为带顶层目录的 ZIP 结构，兼容 AstrBot 本地上传安装
4. 按 AstrBot 插件发布规范整理：metadata 描述与 README 更新、补充界面复用来源与数据/权限说明，代码使用 ruff 格式化
5. 仓库地址更新为 https://github.com/Shou-Lu/NC-Go，插件作者名称由 SATA-F5 更改为 守禄；插件标识符 `pulid_napcat_go_to_astrbot` 保持不变，旧仓库地址会自动重定向
6. 插件许可证由 GPL-3.0 更改为 GNU Affero General Public License v3.0（AGPL-3.0）

# 7.5.0
1. 版本号更新为 7.5.0
2. 保存 sudo 密码时增加显式二次确认；LinuxQQ 版本不兼容时，启动、重启、安装前提示可能自动重装 QQ 及数据丢失风险
3. README 补充 LinuxQQ deb 来源说明，明确其为官方安装包的固定兼容版本备份
4. 修复 Windows 下载 NapCat 时 `ssl_ctx` 未定义导致的 `Download failed`；下载器统一直接创建 SSL 上下文，并在日志中记录当前平台与目标目录

# 7.4.2
1. QQ Linux deb 下载源固定为 Shou-Lu/NC-Go 仓库（QQ_3.2.34_Linux_amd64.deb），保留多镜像加速回退
2. 版本选择器移到「配置」弹窗内的「版本管理」分组，替换为自定义玻璃风下拉（带搜索框）
3. 安装新版本前自动清理旧版本文件（保留 config 配置目录），不再需要单独的卸载按钮
4. 所有危险操作（停止/启动/同步/安装/重启/清理/保存配置）改为插件内置弹窗确认，不再依赖浏览器原生 confirm
5. 移除了每次启动自动检测并自动下载安装 NapCat 的逻辑，改为由用户在网页手动选择版本后安装
6. 启动和安装时自动 apt install 全套 Linux 运行依赖（libatspi2.0-0 libgtk-3-0 libgbm1 libasound2t64 libnss3 libnotify4 libsecret-1-0 libxss1 libxtst6 libxkbfile1 xvfb x11vnc openbox dbus-user-session fonts-wqy-zenhei）
7. NapCat 运行中禁止安装新版本（前后端双重拦截，弹提示要求先停止）
8. 新增后端 API：GET /releases；POST /install 支持传入指定版本
9. launcher.sh 过滤 QQ 的 GLX/EGL GPU 噪音日志，日志面板只保留 NapCat 有效输出
10. Linux 原生安装改为与官方 NapCat-Installer 一致：写入 QQ 的 `resources/app/loadNapCat.js` 并把 `package.json` 的 `main` 指向它，不再下载/编译 launcher.cpp，也不再使用 LD_PRELOAD 注入
11. 删除 `.so` 硬编码路径带来的 `napcat/napcat.mjs` 软链接、`NAPCAT_BOOTMAIN` 补丁等重复兼容代码；启动统一改用 `xvfb-run` + 24 位虚拟屏 + `--disable-gpu`
12. 卸载 NapCat 时自动还原 QQ 的 `package.json` 并删除 `loadNapCat.js`
13. WebUI 全面改版为 SnowLuma-Go 同款玻璃拟态控制台：左侧状态/操作，右侧实时日志，配置与下载拆分为独立弹窗
14. sudo 体系统一：全部命令走 `linux_tools.run_sudo(-S -p "")`；WebUI 用 RSA 公钥加密密码，后端一次性解密，不再写入配置或日志
15. 配置区新增「界面」浅色模式左右拨动开关：默认跟随 AstrBot 主题，拨动后固定为所选主题，立即生效并记忆
16. 下载页新增内置镜像下拉、版本列表、即时保存下载设置；配置页新增 Linux 依赖安装入口
17. 插件显示名统一为「NapCat Go」；版本号更新为 7.4.2
18. 修复浅色模式主按钮白底白字、标题颜色和整体背景；浅色统一为 AstrBot 风格白卡片 + 浅灰背景 + 黑字
19. 首次引导扩展为 23 步：欢迎、创建 AstrBot 机器人、关闭 SSL、完整界面导览和最终提醒；支持配置里「使用引导」重播
20. 引导完成状态增加版本号，旧版本已跳过引导的用户升级后会重新看到一次新版引导
21. 配置新增「NapCat WebUI 端口」，保存后写入 webui.json；移除「服务器浏览器打开 WebUI」按钮
22. 配置区「界面」改为与 SnowLuma Go 一致的左右拨动开关，主题选择保存到插件配置（ui_theme），支持恢复跟随 AstrBot
23. 自定义页面整体对齐 SnowLuma Go 的布局与浅色 / 深色样式；插件显示名与页面标题统一为「NapCat Go」
24. 自动检测到 NapCat 未安装时，主按钮改为「安装 NapCat」并直接打开下载与安装，不再执行无效的启动
25. 安装 / 更新完成后默认自动启动 NapCat（可在配置中关闭），启动接口增加未安装防护并引导到下载页
26. 恢复 7.4.1 的配置页 sudo 密码保存：在「运行方式」填写一次，经本机密钥加密落盘；启动、安装、同步、清理等操作自动提权，不再反复弹出 sudo 输入框
