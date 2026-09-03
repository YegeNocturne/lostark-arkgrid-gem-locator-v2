# 失落的方舟 - 方舟棋盘战斗力优化器

一个玩家自制的工具，用于优化失落的方舟中的方舟棋盘护石配置，以最大化战斗力。

## 关于本分支

本分支基于 [原始项目](https://github.com/Airplaner/lostark-arkgrid-gem-locator-v2) 及其[中文支持版](https://github.com/symnetry/lostark-arkgrid-gem-locator-v2)修改而来。

### ✍️ 本分支新增改动

1. **支持部署到 Cloudflare Pages**：
   - `vite.config.ts` 依据 `CF_PAGES` 环境变量自动选择资源基础路径（Cloudflare 根路径 `/`，GitHub Pages 子路径 `/lostark-arkgrid-gem-locator-v2/`），两种平台均可一键部署而无需手动改配置
   - 修复了根路径部署下精灵图资源 URL 拼接错误（`//` 双斜杠导致资源加载失败）的问题
   - 详细部署步骤见 [部署到 Cloudflare Pages](#部署到-cloudflare-pages)

### ⭐ 继承自上游的改进

以下改进来自中文本地化上游分支，并非本分支新增：

1. **新增服务器支持**：
   - 🇨🇳 **国服支持**（简体中文）
   - 🇷🇺 **俄服汉化支持**（中文界面的俄服）

2. **重要 Bug 修复**：
   - 修复了**护石重复提取**的问题
   - 优化了护石识别逻辑，防止在有多个相同护石时的误判

3. **完整中文本地化**：
   - 所有界面文本、说明、FAQ 都已翻译成中文
   - 游戏环境选择器支持所有服务器的中文显示

---

## 功能

1. **护石识别**：通过屏幕共享自动识别游戏中的护石
2. **战斗力优化**：智能计算最佳护石配置
3. **多服务器支持**：
   - 🇰🇷 韩服（韩语）
   - 🇺🇸 美服/国际服（英语）
   - 🇷🇺 俄服（俄语）
   - 🇷🇺 俄服汉化（中文界面）
   - 🇨🇳 国服（简体中文）
4. **配置管理**：保存和管理多个角色配置
5. **结果预览**：查看优化后的战斗力提升和护石配置

---

## 使用方法

### 1. 准备工作
- 打开失落的方舟游戏
- 进入角色的方舟棋盘界面
- 切换到未使用的预设以卸下所有护石

### 2. 选择服务器环境
在开始屏幕共享前，先选择正确的游戏环境：
- **国服玩家**：选择"国服（简体中文）"
- **俄服汉化玩家**：选择"俄服汉化"
- **其他服务器**：选择对应的服务器环境

### 3. 开始使用
1. 在浏览器中打开此工具
2. 点击 [🖥️ 开始屏幕共享] 按钮，选择失落的方舟游戏窗口
3. 向下滚动游戏中的护石列表，工具会自动识别所有护石
4. 确认所有护石都已识别后，点击 [✅ 应用到当前配置]
5. 进入优化设置页面，调整核心最小点数
6. 点击 [执行优化] 按钮，等待计算完成
7. 查看优化结果和建议

---

## 常见问题

### Q: 屏幕共享失败或被拒绝
A: 请使用 Chrome 或 Edge 浏览器，并确保已授予屏幕共享权限。

### Q: 护石没有被识别
A: 请检查以下内容：
- 游戏分辨率设置为 1920x1080 (16:9)
- 游戏窗口处于窗口模式
- 没有使用强制 21:9 宽高比
- 护石列表完全可见，没有被其他 UI 遮挡
- **确认已选择正确的游戏环境**（国服/韩服/美服等）

### Q: 护石被重复识别了
A: 这个问题已经在本分支中修复！
- 优化了识别逻辑，只选择最佳匹配
- 添加了重复检测机制
- 保留了对多个完全相同护石的支持（玩家确实可能有多个相同护石）

### Q: 优化结果不理想
A: 尝试调整核心最小点数设置，或确保所有护石都已正确识别。

---

## 技术栈

- **求解器**: 带有上界剪枝的自定义回溯算法 (TypeScript)
- **前端**: Svelte 5 (基于组件的 UI，本地状态持久化)
- **图像处理**: OpenCV.js (模板匹配，Web Worker)
- **部署**: GitHub Pages / Cloudflare Pages (纯客户端)

---

## 技术细节

- [求解算法](docs/algorithm.md)
- [屏幕识别](docs/opencv.md)

---

## 如何运行

### 安装

使用 pnpm 安装依赖：

```bash
pnpm install
```

### 开发

启动开发服务器：

```bash
pnpm dev
```

### 构建

构建生产版本：

```bash
pnpm build
```

### 生成 OpenCV 精灵图

OpenCV 模板位于 `opencv-templates/` 目录下。运行以下命令将它们合并为一个精灵图：

```bash
pnpm run generate:sprite
```

生成的精灵图坐标将保存在 `lib/opencv-template-coords/` 目录中：

```typescript
// THIS FILE IS AUTO-GENERATED. DO NOT MODIFY ITSELF
export const zhCnCoords = {
  ...
  'anchor.png': { x: 0, y: 0, w: 148, h: 36 },
  ...
}
```

---

## 部署到 Cloudflare Pages

本项目是纯客户端应用，构建产物为 `dist/` 静态目录，可直接部署到 Cloudflare Pages。

> ⚙️ **路径适配说明**：`vite.config.ts` 会依据 `CF_PAGES` 环境变量自动选择资源基础路径。Cloudflare Pages 构建时默认注入 `CF_PAGES=1`，产物将使用根路径 `/`；本地构建或 GitHub Pages 构建则采用仓库子路径 `/lostark-arkgrid-gem-locator-v2/`。因此无需手动修改配置。

### 方式一：通过 Git 集成部署（推荐）

1. 将本仓库推送到你的 GitHub 账号（如 `yourname/lostark-arkgrid-gem-locator-v2`）
2. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/) → **Workers & Pages** → **创建** → **Pages** → **连接到 Git**
3. 授权并选择该仓库，填写构建设置：
   - Framework preset：选择 **Vite**
   - Build command：`pnpm install && pnpm build`
   - Build output directory：`dist`
4. 点击 **保存并部署**，稍等片刻即可通过 `https://<项目名>.pages.dev` 访问

### 方式二：使用 Wrangler CLI 部署

```bash
# 安装依赖并构建（CLI 部署时需手动设置 CF_PAGES=1）
pnpm install
npx wrangler login

# Windows (PowerShell)
$env:CF_PAGES = "1"
pnpm build

# macOS / Linux
# CF_PAGES=1 pnpm build

npx wrangler pages deploy dist --project-name=<你的项目名>
```

### 部署后验证

部署完成后建议检查以下资源是否可正常访问（将域名替换为你的实际地址）：

- `https://<你的域名>/` → 返回 HTML 页面
- `https://<你的域名>/assets/index-*.js` → 返回 `application/javascript`
- `https://<你的域名>/opencv_template_ko_kr_*.png` → 返回精灵图

若页面空白或提示"분석 엔진을 준비하는데 실패하였습니다"，请确认：
1. 构建环境中 `CF_PAGES` 已被设置为 `1`（Git 集成部署会自动注入）
2. 所有资源路径均以 `/` 开头，而非项目子路径

---

## 许可证

本项目遵循 [MIT License](license.txt)。

> 注意：根据 `license.txt` 的约定，本许可证仅适用于作者创建的源代码与原创内容；所有与游戏相关的素材（图片、图标等）版权归其各自所有者所有。

---

## 原项目

本项目是基于以下上游项目修改而来：

- 原始项目（韩服）: [Airplaner/lostark-arkgrid-gem-locator-v2](https://github.com/Airplaner/lostark-arkgrid-gem-locator-v2)
- 中文支持版: [symnetry/lostark-arkgrid-gem-locator-v2](https://github.com/symnetry/lostark-arkgrid-gem-locator-v2)

---

## 联系方式

如有问题或建议，请通过以下方式联系：
- Discord: [链接](https://discord.gg/Zk4K3xt9ub)
- KakaoTalk: [链接](https://open.kakao.com/o/s5bTYodi)

---

## 贡献

欢迎提交 Issue 和 Pull Request！

---

**注意**: 这是一个非官方工具，与 Smilegate RPG 或 Amazon Games 无关。