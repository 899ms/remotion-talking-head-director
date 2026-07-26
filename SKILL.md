---
name: remotion-talking-head-director
description: Create and refine talking-head, voice-over, knowledge, tutorial, and creator videos with Remotion through a gated Codex workflow: source inspection, accurate transcript and timecodes, low-resolution proxy media, style frames, semantic storyboard, Studio preview, frame-specific revisions, quality review, and final high-resolution render only after explicit approval. Use when the user asks Codex to make, package, polish, automate, or batch-produce a Remotion video from recorded speech, audio, subtitles, screenshots, or reference videos.
---

# Remotion Talking-Head Director

把 Codex 当作“视频工程师”，把用户当作“导演”。目标不是一次性赌出成片，而是通过可验证的阶段门，把口播/配音素材稳定制作成可预览、可逐帧修改、可最终导出的 Remotion 项目。

## 与其他 Skill 的关系

本 Skill 管理**制作流程、阶段门、交付物和验收**。

编写或修改 Remotion 代码时，同时遵循已安装的 `remotion-best-practices`、`remotion-captions` 和 `remotion-render`。若它们与本 Skill 在 API 细节上冲突，以当前 Remotion 官方 Skill 为准；本 Skill 的“未经确认不得最终渲染”“不得改写原始字幕”“先代理后原片”等流程约束仍然有效。

## 触发条件

用户提出以下任一需求时使用：

- 给口播视频加字幕、卡片、数字动效、转场或信息图；
- 把录音/配音制作成知识短视频；
- 用 Remotion 模仿参考博主的视觉表达；
- 用 Codex 自动剪辑、包装或批量生成短视频；
- 修复人物黑场、遮罩、图层、字幕不同步、转场不自然；
- 根据某个时间码或帧数精确修改 Remotion 视频。

## 核心原则

1. **先分析，后设计，再开发，最后渲染。**
2. **代理素材用于开发，原始高分辨率素材只用于最终导出。**
3. **原始口播和转写文本是字幕事实源，不得擅自改写。**
4. **视觉效果必须服务于当前语义，不为炫技堆特效。**
5. **参考截图、关键帧和具体帧反馈，优先级高于模糊审美形容词。**
6. **Remotion Studio 是主要验收界面；未获明确确认，不得自动导出最终视频。**
7. **任何阶段不得删除、覆盖或破坏用户原始素材。**
8. **先复用官方 API/组件，再考虑许可明确的第三方组件。**

## 强制阶段门

默认按以下阶段执行，并把状态记录到 `production/production-state.md`。

- `P0` 工作区与素材检查
- `P1` 转写与时间码
- `P2` 代理素材与素材清单
- `P3` 视觉规范与关键帧风格稿
- `P4` 语义分镜
- `P5` Remotion 开发与 Studio 预览
- `P6` 帧级修改
- `P7` 质量评审
- `P8` 原片替换与最终渲染

**默认一次只推进到用户要求的阶段。**

若用户只说“帮我做这条视频”，首次运行最多完成 `P0-P2`，然后给出检测结果、生成文件和下一阶段计划。不要未经确认直接生成整条成片。

进入 `P5` 前，必须得到用户对视觉方向的明确确认，例如“风格确认，继续制作”。

进入 `P8` 前，必须得到用户对预览的明确确认，例如“确认，可以导出”。同义表达也可，但必须是显式授权，不能自行推断。

## P0：工作区与素材检查

先检查，不要立刻改代码：

1. 确认当前目录、Git 状态、Remotion 项目结构、包管理器和可用脚本。
2. 定位用户提供的视频、音频、SRT、截图和参考素材。
3. 检测 `ffmpeg`、`ffprobe`、转写工具和 Remotion CLI 是否可用。
4. 使用 `ffprobe` 获取：容器、视频编码、音频编码、宽高、帧率、时长、旋转信息、音频采样率。
5. 判断输入类型：
   - talking-head：有人物口播画面；
   - voice-over：仅配音或无需保留人物；
   - mixed：人物口播 + 屏录/B-roll/图片。
6. 检查是否已有 Composition、字幕系统、品牌规范和可复用组件。
7. 不得移动、删除或覆盖原始文件。

输出 `production/media-manifest.json` 和 `production/production-state.md`。

若缺少必要工具，不要伪造结果；列出缺失项、影响和最小安装方案，等待用户决定。

## P1：转写与时间码

目标是得到可验证的字幕事实源。

1. 优先复用项目已有的本地转写脚本或用户已安装的 Whisper/faster-whisper/WhisperX。
2. 生成：
   - `production/transcript/transcript.txt`
   - `production/transcript/captions.srt`
   - `production/transcript/transcript.json`
3. `transcript.json` 至少包含：
   - `startMs`
   - `endMs`
   - `text`
   - `words`（工具支持时）
   - `keywords`
   - `numbers`
   - `semanticRole`：hook / context / evidence / transition / conclusion / cta
4. 对明显错字只做“候选修正标记”，不得静默替换专有名词、数字和产品名。
5. 已有 SRT 时，先核对而不是重新杜撰字幕。
6. 记录无法确认的词，交给用户确认。

字幕文本必须来自真实转写或用户提供的稿件。禁止为了画面效果自行概括成另一套字幕。

## P2：代理素材与素材清单

高分辨率原片不得直接作为日常 Studio 预览的默认素材。

1. 保留原始素材不变。
2. 为视频生成 720p 代理；机器较弱或用户要求时生成 480p。
3. 代理素材必须尽量保持原片：
   - 起始时间一致；
   - 总时长一致；
   - 音轨同步；
   - 旋转方向正确；
   - 可验证的帧率/时间基准。
4. 生成后用 `ffprobe` 对比原片与代理，记录时长误差。
5. 默认开发使用代理，最终渲染再切换原片。
6. 大型源视频、代理视频和临时渲染文件应加入 `.gitignore`，但不得从磁盘删除。

推荐目录：

```text
production/
  media-manifest.json
  production-state.md
  transcript/
  design/
  storyboard/
  reviews/
public/
  media/
    proxy/
    source/
  references/
```

只在项目需要 `staticFile()` 时复制素材到 `public/`；复制不能覆盖源文件。

## P3：视觉规范与关键帧风格稿

正式制作前先做视觉方向，不要直接铺完整时间轴。

1. 分析用户参考截图、已有品牌风格和视频内容。
2. 输出 `production/design/style-guide.md`，至少定义：
   - 画幅、分辨率、安全区；
   - 色板与背景；
   - 中文字体、字号、字重、行高；
   - 普通字幕与强调字幕；
   - 卡片圆角、边框、阴影和间距；
   - 人物全屏、缩小卡片和画中画布局；
   - 数字、图表、转场和 CTA 规范；
   - 禁止使用的视觉效果。
3. 先实现或渲染 3 个低成本关键帧：
   - 开场钩子；
   - 中段数据/解释；
   - 结尾 CTA。
4. 优先使用 Studio 或低分辨率 still 检查，不得启动完整 4K 渲染。
5. 报告关键帧文件位置并等待视觉确认。

没有参考图时，可以提出 2-3 个清晰可比较的方向，但不要假装知道用户偏好。

## P4：语义分镜

根据逐字稿逐句映射视觉表达，输出 `production/storyboard/storyboard.json` 和可读版 `storyboard.md`。

每个镜头至少包含：

- `startMs` / `endMs`
- `startFrame` / `endFrame`
- 原始口播文本
- 语义目标
- 人物状态：fullscreen / card / hidden / none
- 字幕内容与强调词
- 视觉组件：card / number / chart / icon / screenshot / broll / code
- 入场、停留、退场方式
- 图层顺序
- 素材需求
- 与下一场的衔接

语义映射示例：

- 数字或增长 → 计数器、进度或对比图；
- “第一/第二/第三” → 步骤节点；
- 两个问题 → 双栏问题卡；
- 前后变化 → before/after；
- 方法流程 → 纵向步骤或流程图；
- 明确结论 → 大字总结；
- CTA → 单一行动，不堆多个按钮。

避免每句话都换场。以信息段落为单位组织场景，保证阅读时间和视觉连续性。

## P5：Remotion 开发与 Studio 预览

1. 先读取并遵循当前 Remotion 官方 Skill。
2. 使用项目现有结构；空项目才创建新 Remotion 项目。
3. 优先级：
   - Remotion 官方 API/包；
   - 项目已有组件；
   - 许可明确的第三方组件；
   - 最后自行实现。
4. 动画由帧驱动：`useCurrentFrame()`、`interpolate()`、必要时 `spring()`。
5. 禁止依赖 CSS transition、CSS keyframes 或 Tailwind animation 完成需要渲染的动画。
6. 使用 `Sequence`、`Series` 或官方转场组件组织时间轴。
7. 图片用 `Img`；音视频优先使用当前官方媒体组件；本地素材通过 `staticFile()`。
8. 逐字稿、分镜、配色和内容通过 props/数据文件驱动，避免散落硬编码。
9. 代理/原片切换必须集中在一个配置点，例如 `mediaMode: "proxy" | "source"`。
10. 人物视频在缩放转场前后保持同一连续实例或可验证的无缝衔接，避免卸载导致黑场。
11. 为人物、背景、蒙层、文字、卡片明确图层规则。
12. 启动 `npx remotion studio`，给出 Composition ID 和本地预览方式。
13. 当前阶段不得自动渲染最终 MP4。

若 Agent 发现自己已启动不必要的完整渲染，应立即停止渲染进程，清理由本次任务生成的**未完成临时文件**，但不得删除源素材、已确认成片或项目代码。

## P6：帧级修改

用户反馈优先转换成精确的帧级变更单。

每条修改记录：

- 目标时间码/帧；
- 元素；
- 起始状态；
- 结束状态；
- 持续帧数；
- 缓动；
- 图层；
- 不允许影响的范围。

示例：

```text
第118帧开始人物由全屏缩小；第138帧结束，宽度360px，移动到右下角；
使用克制的 spring 或 bezier，不明显过冲；人物层高于背景和遮罩；
不得遮挡面部；第138帧后保持稳定；不要修改其他场景；只更新 Studio 预览。
```

Agent 可以在执行过程中接收新增需求，但必须：

1. 把新需求追加到变更单；
2. 判断是否与当前改动冲突；
3. 只修改目标区间；
4. 报告受影响文件和帧范围；
5. 不因局部修改触发最终渲染。

## P7：质量评审

预览完成后进行只读评审，不要边评边改。

至少评估：

1. 字幕准确度和同步；
2. 前 3 秒钩子；
3. 版式、字号、安全区和阅读时长；
4. 语义与画面匹配；
5. 动画节奏和转场连续性；
6. 人物遮挡、黑场、闪烁和明暗突变；
7. 音频同步和音量；
8. 参考风格一致性；
9. 性能与素材加载；
10. 最终导出风险。

输出 `production/reviews/review-report.md`：每个问题必须包含时间码、帧数、严重级别、证据和建议。

若环境支持子代理且用户允许较高 Token 消耗，可以让不同只读角色分别评审字幕、视觉、动画、图层、留存，再由主 Agent 汇总。子代理不得同时修改代码。

### 常见故障定位

人物缩小后黑场或变暗时，检查：

- `AbsoluteFill` 渲染顺序；
- `zIndex`；
- 黑色背景/半透明蒙层；
- `opacity` 动画；
- `Sequence` 或转场重叠；
- 人物组件是否被卸载或重新挂载；
- `overflow: hidden` 和裁剪容器；
- 两个视频实例是否在切换点重叠或断开。

禁止用硬切掩盖应当连续的转场，除非用户明确要求硬切。

## P8：最终渲染

只有用户明确确认预览后执行。

1. 保存当前确认版本；有 Git 时先记录状态，避免把未确认改动混入。
2. 将 `mediaMode` 从代理切换到原始素材。
3. 验证原片与代理的时长、时间轴、旋转和音频同步。
4. 列出 Composition，确认目标 ID、FPS、分辨率、总帧数。
5. 先做必要的低成本抽帧检查；确认无误后再完整渲染。
6. 输出到明确文件，例如 `out/final-4k.mp4`，不得覆盖源视频。
7. 渲染后验证：文件存在、大小合理、编码、分辨率、帧率、时长和音轨。
8. 若渲染失败，保留日志并定位原因，不要反复盲目重试。

最终报告必须包含：

- 完成阶段；
- Composition ID；
- 分辨率和 FPS；
- 总帧数/时长；
- 代理与原片路径；
- 输出路径和文件大小；
- 修改文件；
- 已执行验证；
- 未解决风险。

## 停止条件

遇到以下情况必须停止推进并说明：

- 找不到输入素材；
- 无法可靠转写且用户未提供稿件；
- 原片和代理时长不一致且无法解释；
- 参考要求相互冲突；
- 真实字幕与用户要求的改写字幕未明确谁是事实源；
- 缺少商业字体、授权素材或第三方组件许可证；
- 最终渲染尚未得到明确确认；
- 改动可能删除或覆盖原始素材。

## 输出风格

每个阶段完成后用中文简洁报告：

```text
阶段：P2 代理素材
状态：完成 / 阻塞 / 等待确认
已生成：...
验证：...
发现问题：...
下一步：...
需要用户确认：...
```

不要只说“已完成”。必须给出可检查的文件、命令或 Studio 预览入口。

## 参考资料加载规则

- 执行完整流程前，读取 `references/workflow.md`。
- 需要给用户形成可复制指令时，读取 `references/prompt-patterns.md`。
- 预览评审或最终导出前，读取 `references/review-checklist.md`。
- 创建生产记录文件时，复用 `templates/` 下的模板。
