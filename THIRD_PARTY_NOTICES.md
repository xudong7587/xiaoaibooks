# 第三方许可与源码入口

本文件随镜像保存于 `/app/licenses/THIRD_PARTY_NOTICES.md`。前端对应源码包为 `/app/licenses/frontend-source.tar.gz`；完整提取及构建说明是 `/app/licenses/BUILDING-FRONTEND.md`。这里记录软件许可，不授予任何第三方媒体内容、商标、账号或书源的使用权。项目不提供媒体源、音频或版权资源文件，用户导入材料由用户自行确认授权。

| 软件 | 固定版本／许可 | 随镜像保留的文本和来源 |
| --- | --- | --- |
| SunnyUI design system | 2.1.0，GPL-3.0-only | `GPL-3.0-only.txt`；固定提交 `e44fc8823599c7158a43d6952d1bd521b2895b0c`，对应源码内 `web/SUNNY-UI-SOURCE.json`。[上游](https://github.com/xudong7587/sunny-ui-design-system/tree/e44fc8823599c7158a43d6952d1bd521b2895b0c) |
| SunnyUI 液态玻璃几何 | Apache-2.0 | `Apache-2.0.txt`、`SunnyUI-liquid-glass-NOTICE.txt`；Copyright 2026 compose-miuix-ui contributors，原始透镜来源 Kyant0/AndroidLiquidGlass。[固定上游](https://github.com/compose-miuix-ui/miuix/tree/153920c7e196de1f1c8188e16162f3ce4864b016)。网页移植范围和修改见 NOTICE，未复制 Android 渲染管线。 |
| React / ReactDOM | 19.2.7，MIT | `react-19.2.7-LICENSE.txt`、`react-dom-19.2.7-LICENSE.txt`；[上游](https://github.com/facebook/react)。 |
| Phosphor React icons | 2.1.10，MIT | `phosphor-icons-react-2.1.10-LICENSE.txt`；[上游](https://github.com/phosphor-icons/react)。 |
| HLS.js | 1.7.4，Apache-2.0 | `hls.js-1.7.4-LICENSE.txt`；[版本源码](https://github.com/video-dev/hls.js/tree/v1.7.4)。 |
| 其它实际安装的 npm 包 | 由前端 lockfile 固定 | `npm/MANIFEST.json` 和各包目录内原 LICENSE/NOTICE；包含 scheduler、构建工具及其已安装依赖，不移除原版权说明。源码下载与完整性见对应源码的 `web/package-lock.json`。 |
| Rhino | 1.8.0，MPL-2.0 等（以完整文本为准） | `rhino-1.8.0-LICENSE.txt`；未修改的运行库。[版本源码](https://github.com/mozilla/rhino/tree/Rhino1_8_0_Release)、[该版本源码包](https://repo.maven.apache.org/maven2/org/mozilla/rhino/1.8.0/rhino-1.8.0-sources.jar)。MPL 授权材料及其修改仍按其原许可证提供。 |
| Jsoup | 1.18.3，MIT | `jsoup-1.18.3-LICENSE.txt`；未修改的运行库。[该版本源码包](https://repo.maven.apache.org/maven2/org/jsoup/jsoup/1.18.3/jsoup-1.18.3-sources.jar)。 |

Python 包保持安装包随附的 LICENSE/NOTICE（`/usr/local/lib/python3.12/site-packages/*dist-info/`，部分位于其 `licenses/` 子目录）。Playwright 的额外文本也在 `playwright/driver/LICENSE`、`playwright/driver/package/NOTICE` 和 `playwright/driver/package/ThirdPartyNotices.txt`。本次已锁定包与版本见镜像 `/app/requirements-p0.txt`；这些组件未被本项目重新许可。

Debian、Python、OpenJDK、Chromium、字体及其系统依赖保留镜像内 `/usr/share/doc/*/copyright` 和各软件自带许可。发布构建会记录已安装 Debian 二进制/源码包名与版本到 `debian-packages.tsv`；相应源包可按记录的 Source/Version 从 [Debian Sources](https://sources.debian.org/) 或 [Debian Snapshot](https://snapshot.debian.org/) 获取。OpenJDK 的 Classpath 例外不取消其自身发行条件。镜像发布者仍须对最终镜像中实际版本的源代码可得性负责，不能用本项目许可证覆盖系统软件的原有权利。

仅供本地使用的账号、Cookies、书源、历史、封面和验证状态位于用户数据卷，不属于本软件的对应源码，也不随本发行镜像提供。
