---
name: applovin-novel-playable
description: 根据带书籍 ID 的视频和对应小说落地页生成 AppLovin 单文件互动广告，支持 TapRead 与 NovelSpark 渠道模板、双视频点击开声、剧情按钮及 MRAID 跳转。
---

# 小说互动广告生成

使用本技能把用户提供的视频、书籍 ID、渠道及落地页整理成可交付的单文件 HTML。复用 assets/template.html 和渠道图标，不重新设计已确认的模板。创建新输出目录，不覆盖其他渠道或历史成品。

## 输入和映射

- 渠道：tapread 或 novelspark。用户未明确但链接/图标唯一对应渠道时可推断并说明。
- 视频：开场 intro 和点击开声后的 dialogue。优先按用户标注的 1/2、intro/dialogue 分配；书籍 ID 只标识书，不能证明视频先后，无法区分才询问。
- 从文件名/用户信息中提取书籍 ID，与落地页 HTML、URL 对应核对。文件名冲突或一书多链接无法唯一匹配时询问，不凭相似标题猜测。
- 用户提供完整 HTTPS URL：完整保留路径和查询参数，不去掉、改写或自动把 fb 参数换成 AppLovin 参数。
- 用户只提供落地页 HTML：读取它查找明确的原始页面 URL/canonical/用户配置。商店链接、统计请求、素材 URL 都不是小说阅读页。无法确认公开阅读 URL 时，先生成不含伪造跳转的待配置原型，并明确请求 URL；不能把本地 HTML 路径当作远程落地页。
- 不把整个远程小说网站嵌入 iframe。若用户明确要求广告内部试读，另外提取其提供/授权使用的片段，最终 CTA 才打开外部 URL。
- 默认两个按钮都指向同一本书的同一落地页；明确提供两书两路线时分别映射。输出清单写明按钮 → 书籍 ID → URL，方便核对。

## 已确认模板

### 每条素材单独生成选择区文案

截图所示的引导短句、主标题和两个按钮文案必须根据本次视频内容制作，不固定套用 YOUR STORY. YOUR CHOICE. / WHAT WILL YOU DO? / 逆袭与救赎。先实际查看视频关键帧、读取字幕并听取可获得的对白，概括人物关系、当前冲突、结束时未解决的问题；再推测两个合理且有区分度的后续行动，写成简短自然的英文选择文案。默认无需用户另提供文案。用户指定语言或原文优先。

推测是广告创意方向，不是已证实的小说剧情：不凭空承诺落地页里没有依据的身份、奖励或结局。可结合对应落地页的简介核对，但不能只看书籍 ID 就编剧情。无法读取音频、字幕或关键画面时明确说明，采用可观察画面能支持的文案；信息完全不足时补问剧情摘要，不假装看过视频。两个按钮即使同指一本书，也要体现不同的行动选择，不能暗示点击后一定进入两个实际不存在的小说分支。

将选择区文案写入 config.json 的 choiceEyebrow、choiceTitle 和 routeA.label / routeB.label；交付清单同时记录视频依据和推测方向。仅更换文案，不擅自改变渠道样式、布局或链接。

开场视频静音播放，结束保持最后一帧；TAP TO HEAR 循环轻微缩放吸引点击。点击后换成第二段视频，从头带声音播放；结束定格，显示按本次视频生成的引导、标题和两个行动选项。不加 Replay scene，不自行补写小说试读。按钮主动点击后 MRAID 环境 mraid.open，普通浏览器当前页导航。TapRead 黄黑配色、深色开声按钮；NovelSpark 橙白配色。图标默认使用随技能附带的已确认图片，用户新图优先。

## 执行

1. 用 ffprobe 检查两段时长、尺寸、编码和音轨。保留完整素材，不擅自截成 8 秒。必要时 ffmpeg 转 H.264/AAC 或压缩；保留原件，先降低码率再适度降低分辨率，不随意裁画面。无音轨需明确说明无法开声。
2. 在输出目录写 config.json（schema 见下），调用 `node <技能目录>/scripts/build.mjs /绝对路径/config.json`。全部资源以 Base64 内嵌；最终 ≤ 5,000,000 字节，目标 < 4.5 MB。超限时压缩生成派生视频并更新配置，重新构建。
3. 查看生成的 manifest.json，核对路由和书籍 ID。构建检查仅证明静态约束，不能声称审核通过。
4. 有浏览器工具时验证开场、点击换源后声音、第二段结束定格、按钮、横竖屏。隐藏/返回不自动出声；用 AppLovin Preview 检查 ready/viewableChange 和 mraid.open。未实际测试的项目逐项标注。上传/发布不是默认授权，不自动上传到平台或部署公网。
5. 最终给可点击 HTML 文件和简短检查结果。只需发送成品 HTML，不需发送视频或图标。iOS 本地文件预览可能限制跳转，分享预览建议 HTTPS 托管，不能承诺本地所有浏览器可用。

## config.json

路径相对 config 文件目录解析，output 也是。routeB 省略则与 A 相同。label 可省略。输入 URL 必须真实由用户提供或明确验证，脚本不抓取小说或发布文件。

```json
{
  "channel": "tapread",
  "intro": "book123-1.mp4",
  "dialogue": "book123-2.mp4",
  "output": "book123-tapread.html",
  "routeA": {"bookId": "123", "url": "https://用户实际提供的阅读地址", "label": "LEAVE HIM. BECOME POWERFUL."},
  "routeB": {"bookId": "123", "url": "https://用户实际提供的阅读地址", "label": "LET SOMEONE SAVE ME."}
}
```

可选 logo 为图片路径。官方预览入口 https://p.applov.in/playablePreview?create=1 ，不是 p.applovin.com。看到 You have successfully clicked 表示预览拦截并接收了调用，不是实际落地页验证。平台规范可能变化，提交前按需要核对当前官方规范；不要保证上传即能投放。
