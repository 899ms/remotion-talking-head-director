# Workflow Reference

## 目标

将口播视频/录音转化为 Remotion 可维护项目，而不是一次性生成不可控成片。

## 标准路径

```text
原始口播或配音
  → 素材检测
  → 精确转写与时间码
  → 720p/480p 代理素材
  → 视觉规范与关键帧
  → 语义分镜
  → Remotion 开发
  → Studio 预览
  → 帧级修改
  → 只读评审
  → 用户确认
  → 原片最终渲染
```

## 推荐文件结构

```text
production/
  production-state.md
  media-manifest.json
  transcript/
    transcript.txt
    captions.srt
    transcript.json
  design/
    style-guide.md
    keyframes/
  storyboard/
    storyboard.json
    storyboard.md
  reviews/
    review-report.md
public/
  media/
    proxy/
    source/
  references/
out/
```

## 素材检测命令参考

Windows PowerShell：

```powershell
ffprobe -v error -show_format -show_streams -of json "输入文件.mp4"
```

生成 720p 代理的通用思路：

```powershell
ffmpeg -i "输入文件.mp4" `
  -vf "scale=-2:720" `
  -c:v libx264 -preset veryfast -crf 23 `
  -c:a aac -b:a 128k `
  -movflags +faststart `
  "public\media\proxy\input-720p.mp4"
```

命令只是起点。生成后必须用 `ffprobe` 比较原片与代理的时长、帧率和音轨。旋转、HDR、可变帧率、多音轨等情况需要按实际素材调整，不能机械套用。

## 生产状态

Agent 每完成一个阶段，更新 `production-state.md`：

- 当前阶段；
- 阶段状态；
- 输入文件；
- 生成物；
- 验证结果；
- 用户确认记录；
- 下一步；
- 阻塞问题。

这样即使 Codex 会话中断，也能从项目文件恢复上下文。

## 参考图使用

参考图用于提取可执行的设计变量：

- 构图；
- 人物占比；
- 字幕位置；
- 字体层级；
- 卡片尺寸；
- 背景与色板；
- 转场方向；
- 信息密度。

不要照搬水印、Logo、受版权保护的品牌资产或无法确认授权的素材。

## 代理与原片切换

项目应集中管理素材模式：

```ts
type MediaMode = "proxy" | "source";
```

不要在多个组件中手工替换路径。建议由 Composition props、配置文件或单一媒体解析函数决定实际路径。

## 评审模式

评审时先记录问题，再集中修改。避免“发现一个问题就改一个”，否则容易引入新的时间轴回归。

按严重度：

- P0：无法渲染、素材丢失、音画严重不同步；
- P1：黑场、人物被遮挡、字幕事实错误、关键内容不可读；
- P2：动画节奏、局部版式、视觉一致性问题；
- P3：可选优化、细节润色。
