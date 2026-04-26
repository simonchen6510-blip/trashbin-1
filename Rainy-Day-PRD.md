# EP07 听雨集

制作阶段: PRD
技术方案: 录音 + Web Audio API 音频特征提取 → AI 图像生成（DALL·E / Midjourney API）→ 浏览器 Geolocation API 自动记录 → React / Next.js 前端时间线展示。1-2 天可出可演示 Demo。
核心创意: 录下每一场雨的声音，AI 根据雨的特征自动生成专属画面，配上时间、地点和心情，变成一本「雨的相册」。用声音收藏雨，用画面记住雨。
灵感来源: 听雨集 (https://www.notion.so/da473253cf474a9a86f8333ae78a7543?pvs=21)

## 📋 PRD

### 1. 产品概述

<aside>
🌧️

**听雨集** —— 录下每一场雨的声音，AI 生成专属水彩小卡，帮你记住每一场雨。

</aside>

**一句话定位**：用声音收藏雨，用画面记住雨——口袋里的雨天绘本。

**目标用户**：对日常诗意有感知力的年轻人，喜欢用小工具记录生活碎片。

**核心价值**：每场雨的声音都不一样，但从来没有人记住过任何一场。听雨集让每场雨变成一枚独一无二的水彩小卡，积攒成只属于你的「雨的相册」。

---

### 2. 核心功能

#### 2.1 录雨

| **维度** | **说明** |
| --- | --- |
| 用户看到什么 | 屏幕中央一个水滴形按钮，静止时有微弱呼吸感脉动 |
| 交互 | 按下按钮 → 涟漪从按钮中心向外扩散 → 开始录制 30s~数分钟雨声 → 再次按下或自动截止结束录制 |
| 后台处理 | ① 录制雨声音频 ② 自动记录时间戳 ③ 浏览器 Geolocation 获取 GPS ④ 可选：调用天气 API 获取当前天气 |
| 输出 | 一段雨声音频 + 元数据（时间、经纬度、天气、季节） |

#### 2.2 AI 生成画面——「千雨千面」

| **维度** | **说明** |
| --- | --- |
| 用户看到什么 | 录制结束后，屏幕渐入雨雾弥散的等待动画（5-15s），随后一枚水彩小卡从雾中缓缓浮现 |
| 交互 | 全自动触发，无需操作；生成后可左右滑动查看细节、长按保存到相册 |
| 输出 | 一枚治愈系水彩插画小卡——圆角色块底 + 水彩晕染 + 手绘线描，画的是雨天日常小场景 |
| 审美基调 | 蓝-靛-薰衣草-薄荷色系，低饱和，绘本感。线条松弛手绘，不追求精确，追求治愈 |

**画面示例**（不同雨声 → 不同小卡）：

- 🏠 低频隆隆的大雨 → 小房子屋顶淋雨，窗户透暖光
- 🐸 高频密集的急雨 → 水坑涟漪满屏，小青蛙撑伞
- 👕 轻柔的细雨 → 晾衣绳上滴水的衣服，冒热气的茶壶
- 🐱 安静的夜雨 → 猫趴窗台，窗外雨丝，台灯微光
- ⛈️ 雷阵雨 → 圆滚滚的乌云表情包 + 大水滴 + 小青蛙穿雨靴
- 🌸 春天的小雨 → 嫩绿小芽探出头，蛇蛋花，水珠挂在叶尖

#### 2.3 标记心情

| **维度** | **说明** |
| --- | --- |
| 用户看到什么 | 画面生成后，底部浮现一行淡灰提示「写点什么，或者不写」 |
| 交互 | 可输入一句话（限 50 字），也可直接跳过；输入后文字以手写体叠在小卡下方 |
| 对生成的影响 | 如果在录制前/中写了文字，做情感分析后微调色调与小物意象（如「想你了」→ 偏暖调、加窗台热饮） |

#### 2.4 雨的时间线

| **维度** | **说明** |
| --- | --- |
| 用户看到什么 | 纵向滚动的卡片流，每张卡片是一枚水彩小卡，标注日期与地点，排列成一本「雨的相册」 |
| 交互 | 上下滑动浏览；点击卡片 → 进入详情页：回放雨声 + 查看大图 + 查看心情文字 |
| 视觉 | 卡片间距适中、留白呼吸感强，整体像翻一本手工 zine |

---

### 3.「千雨千面」生成逻辑

<aside>
🎨

核心思路：不是「雨型→风格」的离散映射，而是 **「音频特征向量 × 时空上下文 → 连续风格空间」** 的参数化生成。每一场雨都是独一无二的坐标点，对应独一无二的画面。

</aside>

#### 3.1 多维音频特征提取

通过 Web Audio API 从雨声录音中实时提取以下特征向量：

| **音频维度** | **提取方式** | **影响画面的什么** | **示例映射** |
| --- | --- | --- | --- |
| 振幅/能量 | RMS 均值 | 笔触力度 | 低能量 → 轻柔淡彩留白；高能量 → 浓郁水彩铺满 |
| 频率重心 | Spectral Centroid | 色温倾向 | 低频主导 → 暖棕/琥珀调；高频主导 → 冷蓝/银调 |
| 节奏密度 | Onset Detection | 画面元素疏密 | 稀疏 → 一两个小物 + 大面积留白；密集 → 多个小物铺满画面 |
| 频谱宽度 | Spectral Bandwidth | 色彩丰富度 | 窄频 → 单色系（纯蓝或纯靛）；宽频 → 蓝紫薄荷多色渐变 |
| 瞬态变化 | Transient Sharpness | 线描风格 | 平缓 → 圆润晕染无明显边缘；尖锐 → 细线条清晰可见 |
| 低频隆隆声 | Sub-bass Energy | 天气元素叠加 | 高 → 叠加乌云层、闪电小图标；低 → 无额外天气元素 |

#### 3.2 环境上下文加权

同样的雨声 + 不同上下文 = 截然不同的画面：

| **上下文维度** | **数据来源** | **画面影响** |
| --- | --- | --- |
| 时间 | 系统时钟 | 凌晨 → 深沉暗调，窗口透微光；午后 → 明亮透光，衣服滴水；傍晚 → 橙粉暮色，路灯初亮 |
| 季节 | 日期推算 | 春雨 → 嫩绿小芽 + 花苞；夏雨 → 荷叶 + 蝉；秋雨 → 落叶金棕 + 热可可；冬雨 → 雾气 + 围巾 |
| 地理 | GPS + 逆地理编码 | 市区 → 屋檐/雨伞/街灯/鱼缸；郊外 → 池塘涟漪/荷叶/青蛙/小桥 |
| 心情文字 | 用户输入（可选） | 情感分析 → 微调色调与意象。如「困了」→ 加被子/猫；「开心」→ 加彩虹小元素 |

#### 3.3 Prompt 拼装策略

Prompt 不写死风格名，而是用参数动态拼接，保证每张小卡独一无二：

```
watercolor illustration on rounded pastel card,
{brushstroke_force} strokes,
{color_temp} tones,
{object_combo} scene,
{density} composition,
soft hand-drawn linework overlay,
{time_mood} atmosphere
```

各参数由音频特征数值区间 + 环境上下文共同决定：

- `brushstroke_force`：RMS → `gentle translucent` / `medium soft` / `bold saturated`
- `color_temp`：Spectral Centroid → `warm amber and brown` / `cool blue and indigo` / `mixed lavender and mint`
- `object_combo`：节奏密度 + 地理 + 季节 → 从预定义小物库中组合（如 `a cat on a windowsill, a steaming teapot, raindrops on glass`）
- `density`：Onset Detection → `minimal with white space` / `moderately filled` / `richly packed`
- `time_mood`：时间 + 心情文字 → `late night cozy glow` / `bright afternoon drizzle` / `misty dawn silence`

---

### 4. 视觉设计规范

#### 4.1 画风定义

<aside>
🖼️

**治愈系水彩绘本风**——不是美术馆里的大作，而是口袋里的一本雨天绘本。

</aside>

- **底色**：每张画面为一个圆角色块，底色是不均匀的水彩晕染
- **色系**：蓝-靛-薰衣草-薄荷为主色调，低饱和，偶尔点缀暖棕/琥珀
- **线条**：在水彩底上叠加简笔手绘线描，线条松弛自然，不追求工整
- **内容**：雨天生活的小切片——小物叙事而非宏大风景
- **质感**：类似手工印章/邮票/绘本页的收藏感
- 

![image.png](EP07%20%E5%90%AC%E9%9B%A8%E9%9B%86/image.png)

#### 4.2 UI 关键页面

- **录制页**：深色背景，中央水滴按钮带呼吸脉动，录制中涟漪持续扩散，底部显示录制时长
- **等待页**：雨雾弥散动画（水彩色块缓慢流动），文案「正在画这场雨…」
- **生成结果页**：白色/浅灰纸质纹理背景上，水彩小卡居中浮现，底部心情输入区
- **时间线页**：纵向卡片流，卡片间距留白，日期与地点标注使用细体灰色字，整体像翻 zine

---

### 5. 技术架构

<aside>
⚡

**推荐方案：React / Next.js 单页应用**（1-2 天可出 Demo）

</aside>

| **模块** | **技术选型** | **说明** |
| --- | --- | --- |
| 录音 + 音频分析 | Web Audio API + AudioContext | 实时提取 RMS / Spectral Centroid / Onset / Bandwidth / Transient / Sub-bass 六维特征 |
| AI 图像生成 | Nano Banana 2（Gemini 3.1 Flash Image API） | 与 AI Studio 同一个 Gemini API Key，无需额外申请；免费檔约 25-50 次/天，Demo 阶段足够；动态拼装 prompt，输出 1024×1024 水彩小卡 |
| 地理/天气 | 浏览器 Geolocation API + OpenWeather API | 自动获取经纬度、逆地理编码城市名、当前天气/气温 |
| 前端框架 | React / Next.js + TailwindCSS | 响应式布局，移动端优先 |
| 数据存储 | IndexedDB（Demo）/ Supabase（云端） | 存储音频文件、生成图片、元数据、心情文字 |
| 动效 | Framer Motion / Lottie | 涟漪扩散、雨雾弥散 loading、卡片浮现渐显 |
- 备选方案
    - **方案 B：纯静态 HTML + TailwindCSS + JS 单文件** — 更极简，但时间线交互体验受限，适合最小演示
    - **方案 C：Streamlit / Gradio** — 快速搭建后端 AI 管线，但前端美感难以达到诗意水准，不推荐本期使用

#### 5.1 API Key 安全方案

<aside>
🔐

**原则：API Key 绝不能出现在前端代码中**。前端只负责发请求到自己的代理层，代理层持有 Key 并转发请求到 Gemini / OpenWeather 等 API。

**为什么选 Nano Banana（Gemini Image API）：**

- ✅ 与 AI Studio 开发环境同一个 API Key，无需额外申请
- ✅ 免费模式无需绑卡，免费额度约 25-50 次/天，Demo 阶段足够
- ✅ 支持对话式图片编辑和迭代调整
- ✅ 对自然语言 prompt 理解能力强，水彩插画风格表现良好
- 付费模式参考：1024px 约 $0.067/张（后续扩展时再考虑）
</aside>

**推荐方案：Cloudflare Pages Functions**

- 在项目 `/functions` 目录下创建 Pages Function（如 `/functions/api/generate-image.ts`）
- API Key 存在 Cloudflare Dashboard 的环境变量中（Settings → Environment Variables）
- 前端调用 `/api/generate-image`，Pages Function 负责拼装 prompt 并调用 Gemini Image API，返回生成的图片
- 这样 Key 永远只存在于服务端，不会暴露给浏览器

**调用链路**：

`前端（音频特征 + 上下文参数） → /api/generate-image（Cloudflare Pages Function） → Gemini Image API (Nano Banana) → 返回图片 → 前端渲染小卡`

**需要代理的 API 调用：**

- Gemini Image API / Nano Banana 图像生成（必须代理）
- OpenWeather 天气查询（建议代理）
- 地理编码 / 逆地理编码（如用付费服务，需代理）

**不需要代理的：**

- Web Audio API 音频分析（纯前端）
- 浏览器 Geolocation API（纯前端）
- IndexedDB 本地存储（纯前端）

#### 5.2 部署方案

<aside>
🚀

**Google AI Studio 开发 → Save to GitHub → Cloudflare Pages 部署**（全程免费，无需绑卡，国内可直接访问）

</aside>

**为什么选 Cloudflare Pages：**

- ✅ 国内可直接访问，无需翻墙
- ✅ 无限带宽，完全免费
- ✅ Pages Functions 支持 Serverless，可安全代理 API Key
- ✅ 全球 CDN 加速，访问速度快

**部署流程：**

1. 在 Google AI Studio 中完成开发与调试
2. 点击「Save to GitHub」按钮，代码自动推到 GitHub 仓库
3. 在 Cloudflare Dashboard → Pages → 连接 GitHub 仓库，一键部署
4. 在 Cloudflare Dashboard 配置环境变量（Gemini API Key 等，免费模式无需绑卡）
5. 获得 `xxx.pages.dev` 公开链接（国内可直接访问）

**备选部署平台：**

- Vercel：对 Next.js 支持更原生，但 `vercel.app` 域名在国内被屏蔽，需绑定自定义域名才能国内访问

**拍视频演示时：**

- Cloudflare Pages 部署的网站国内可直达，无需额外网络工具
- Gemini API 调用走的是 Cloudflare 服务端 → Google AI，不受国内网络影响
- 仍建议提前缓存几张生成好的小卡作为 backup，以防 API 响应延迟

---

### 6. 用户流程

1. 打开 App → 看到水滴按钮 + 「录下这场雨」
2. 按下按钮 → 涟漪扩散，开始录制雨声（30s-3min）
3. 录制结束 → 雨雾弥散等待动画（5-15s），文案「正在画这场雨…」
4. 水彩小卡从雾中浮现 → 用户看到这场雨的专属画面
5. 底部出现「写点什么，或者不写」→ 输入心情或跳过
6. 小卡自动保存到「雨的时间线」→ 回到首页或继续浏览
7. 时间线页 → 上下滑动浏览所有雨的记忆，点击任意卡片回放雨声

---

### 7. Demo 范围界定（MVP）

本期 Demo 聚焦**可演示的核心闭环**，服务于视频拍摄：

- [x]  录雨按钮 + 涟漪动效
- [x]  音频特征提取（至少 RMS + Spectral Centroid + Onset 三维）
- [x]  动态 prompt 拼装 + Nano Banana (Gemini Image API) 生成水彩小卡
- [x]  雨雾弥散等待动画
- [x]  卡片浮现效果
- [x]  心情文字输入（可选）
- [x]  时间线页面展示多张小卡
- [ ]  GPS 自动记录（Nice to have，可用固定地点文案替代）
- [ ]  天气 API 集成（Nice to have）
- [ ]  云端存储（Demo 阶段用 IndexedDB 即可）
- [x]  开发测试模式（内置雨声素材 + 特征可视化面板）

#### 开发测试模式说明

<aside>
🧪

开发期间不可能随时有雨，需要一个 **内置测试面板**，用预录雨声素材模拟实时录音，方便调试音频分析 → prompt 拼装 → 图片生成的完整链路。

</aside>

**测试面板功能：**

**① 雨声素材快捷按钮**

页面底部/侧边栏显示 4-6 个预录雨声按钮，点击即播放并触发音频分析管线：

- 🌧️ 细雨（轻柔、高频为主、稀疏节奏）
- 🌊 中雨（中等能量、均衡频谱）
- ⛈️ 暴雨（高能量、宽频、密集节奏）
- ⚡ 雷阵雨（强低频 sub-bass + 瞬态变化）
- 🌙 夜雨（低能量、平缓、稀疏）
- 🌿 林间雨（中频为主、叶片滴水声）

每个按钮点击后的流程与实时录音完全一致：播放素材 → Web Audio API 分析 → 提取特征 → 拼装 prompt → 调用 Nano Banana → 生成小卡

**② 音频特征可视化面板**

在测试模式下，实时显示当前音频的六维特征值：

- RMS / Spectral Centroid / Onset / Bandwidth / Transient / Sub-bass 的数值 + 柱状图
- 对应映射到的 prompt 参数（如 `brushstroke_force: gentle translucent`）
- 最终拼装出的完整 prompt 文本

这样可以直观看到「雨声 → 特征 → prompt → 画面」的每一步，方便调参。

**③ 上下文模拟开关**

可手动设置时间/季节/地理等上下文参数，不依赖真实 GPS 和系统时钟：

- 时间下拉菜单：凌晨 / 早晨 / 午后 / 傍晚 / 深夜
- 季节下拉菜单：春 / 夏 / 秋 / 冬
- 地理开关：城市 / 郊外
- 心情文字输入框

**实现要点：**

- 测试面板通过 URL 参数或键盘快捷键激活（如 `?dev=true` 或按 `D` 键），正式版不显示
- 雨声素材从免费音效网站下载（如 [Freesound.org](http://Freesound.org) / Pixabay Audio），打包在项目 `/public/samples/` 目录
- 素材名称格式：`rain-light.mp3` / `rain-heavy.mp3` / `rain-thunder.mp3` 等

---

### 8. 关键问题与风险

| **风险项** | **影响** | **应对方案** |
| --- | --- | --- |
| AI 图像生成延迟（5-15s） | 用户等待焦虑 | 设计优雅的雨雾弥散等待动画 + 文案「正在画这场雨…」，让等待本身成为体验 |
| 音频特征→画面映射调试 | 生成的画面风格不一致或不够美 | 预定义 prompt 模板库 + 小物组合库，提前用 Nano Banana 批量测试，确保绘本感稳定 |
| GPS 权限被拒 | 缺少地理上下文 | 降级方案：跳过地理加权，仅用时间 + 音频特征生成；或让用户手动选择「在城市/在自然」 |
| 手机录音质量 | 雨声混杂环境噪音，特征提取不准 | ① 前端简单降噪 ② 特征提取加权偏向中高频雨滴声 ③ 接受「不完美」——噪音也是这场雨的一部分 |
| 视频演示当天无雨 | 无法实景实测 | 提前准备 4 种预录雨声素材作为 backup，支持从本地文件导入 |
| Nano Banana 生成质量不稳定 | 部分小卡偏离治愈水彩风 | prompt 中强化风格锚点词；利用 Gemini 的对话式编辑能力迭代调整；保留翻车画面作为视频彩蛋 |

---

##

###
