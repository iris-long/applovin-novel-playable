# AppLovin Novel Playable

将双段视频、书籍 ID 和用户提供的 HTTPS 小说落地页构建为单文件互动广告。支持 TapRead 与 NovelSpark 样式。视频、图标和代码均内嵌，构建检查 5 MB 上限。

## 安装

在 Codex 中发送下面这句话即可请求安装：

> 请使用 skill-installer 安装 https://github.com/iris-long/applovin-novel-playable 中的 skill。

安装后发送 `$applovin-novel-playable`，附上视频 1、视频 2、书籍 ID、渠道和完整落地页链接。

把此文件夹放入 Codex 的 skills 目录：

- macOS / Linux：`~/.codex/skills/applovin-novel-playable`
- Windows：`%USERPROFILE%\.codex\skills\applovin-novel-playable`

重开 Codex 或新建聊天，输入 `$applovin-novel-playable`。发布到 GitHub 后，也可以把实际仓库 URL 发给 Codex，请它安装其中的这个 skill。本项目不提供自动执行远程 shell 命令的安装器。

## 依赖与使用

需要 Node.js 18 或更新版本；检查、转码和压缩视频需要 FFmpeg / ffprobe。构建器没有 npm 依赖。Codex 负责根据视频生成剧情选择文案；命令行构建器仅应用配置，不自行理解视频。

向 Codex 提供渠道、书籍 ID、开场视频、开声视频、完整 HTTPS 落地页链接。配置结构见 SKILL.md；执行：

```sh
node scripts/build.mjs /absolute/path/to/config.json
```

输出 HTML 和同名 manifest.json。构建成功不等于 AppLovin 审核通过，仍需官方预览和手机验证。不会自动上传或部署。

## 发布内容与品牌素材

包内不包含投放视频、真实书籍 URL 或追踪参数。附带的 TapRead / NovelSpark 标识属于对应品牌；使用、再分发或公开发布前请确认拥有相应权限。本仓库不代表与 AppLovin 或上述品牌存在官方关联。用户可通过配置 logo 更换图标。

不要把包含客户素材、真实投放参数的配置和生成成品提交到公开仓库。本包不附加未经用户选择的开源许可证；公开可见不等于已授予所有内容的任意使用权。
