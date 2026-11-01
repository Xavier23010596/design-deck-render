# 贡献指南

感谢你愿意改进这个项目。请先花两分钟读完下面四节，绝大多数 PR 被退回都是因为踩了 §4 的任一条。

---

## 1. 欢迎什么 / 不接受什么

**欢迎**

- 修正 `SKILL.md` 里表述含糊、执行时会产生歧义的指令。
- 新增**可复用的版式原型**（需附一个能跑的最小示例，并说明适用内容类型）。
- 改进动效时序、字阶、栅格的**可读性问题**（不是"我觉得不好看"）。
- 无障碍与兼容性修复（`prefers-reduced-motion`、键盘可达、窄屏可读）。
- 文档纠错、示例补充、中文措辞优化。

**不接受**

- 引入前端框架、CSS 库、字体 CDN、统计脚本。本项目的卖点之一是"零依赖、离线可用"。
- 把某个具体网站的源码、截图、样式表、字体搬进仓库，或"照着页面抄一份"。
- 任何真实 sharecode、shareId、内部接口地址、token、账号信息（含测试数据里的）。
- 纯风格偏好类改动（"标题再大一点"）——除非能给出可读性依据（对比度、行长、字号实测）。

---

## 2. 开发环境

项目本身**没有构建系统、没有 npm 依赖**，一个文本编辑器就能改。

```bash
git clone https://github.com/Xavier23010596/design-deck-render.git
cd design-deck-render
```

| 工具 | 版本 | 用途 |
|---|---|---|
| 任意文本编辑器 | — | 改 `SKILL.md` / 文档 |
| 现代浏览器 | Chrome / Edge / Safari 近两年 | 打开生成的 `index.html` 核对 |
| Node.js | ≥ 18（可选） | 仅用于自动化截图校验，不参与产物构建 |
| 思源宋体 / 思源黑体（可选） | — | 复现字阶效果；缺失时会降级 |

目录约定：

```
SKILL.md              技能本体（唯一的功能性文件）
README.md             项目说明（中文）
assets/preview.png    效果预览（视觉类 PR 需同步更新）
.github/              Issue / PR 模板
```

---

## 3. 本地验证（提交前必须做）

1. 按 `SKILL.md` 的输入契约，自己造一份**最小内容**（3–5 页，含一次缺图、一次长文案）。
2. 让技能按流程产出 `index.html`。
3. 逐条对照 `SKILL.md` §6 的 11 项质量门，全过。
4. 用 `file://` 双击打开；再**断网**打开一次。
5. 截图两张：`1600×900` 与 `844×390`（手机横屏，确认字号没有缩到读不了）。
6. 检查控制台无报错；检查"减少动态效果"（系统设置或 DevTools Rendering 面板）下页面为终态。

> 视觉类改动**必须**附 before / after 两张同尺寸截图，否则评审无法判断。

---

## 4. 四条红线

1. **不含第三方资源**：源码、图片、样式、字体、图标一律不得入库；只在许可明确（MIT/Apache/CC0 等）
   且有必要时引入，并在 PR 描述里写明来源与许可证。
2. **不含凭证与地址**：真实 sharecode、shareId、tenant、token、私有接口、内网地址一律不得出现在
   代码、文档、示例、截图里。示例一律用 `<YOUR_...>` 占位符。
3. **不提交产物**：`index.html`、`dist/`、截图草稿都在 `.gitignore` 里。要展示效果请更新
   `assets/preview.png`（压缩过的，≤ 1 MB）。
4. **不改写用途**：不把项目改造成"抓取/镜像某个站点"的工具。参考对象的视觉语言已在本项目
   `SKILL.md` 里以规格形式描述，实现只依据该规格。

---

## 5. 提交规范

采用 [Conventional Commits](https://www.conventionalcommits.org/zh-hans/)：

```
<type>(<scope>): <subject>
```

- `type`：`feat` / `fix` / `docs` / `style` / `refactor` / `chore`
- `scope`：`skill` / `readme` / `preview` / `ci` / `meta`
- `subject`：中文或英文均可，一句话说清"改了什么、为什么"

示例：

```
fix(skill): 修正缺图占位在深色底上不可见的问题
docs(readme): 补充断网验收步骤
feat(skill): 新增 range-bar 原型的两端点同时可见约束
```

一个 PR 只做一件事。混着改三件事的 PR 会被要求拆分。

---

## 6. PR 流程

1. Fork → 从 `main` 切分支：`git checkout -b fix/placeholder-contrast`
2. 小步提交，每个提交可独立解释。
3. 填完 PR 模板里的自检清单（不填等于没提交）。
4. 维护者在 7 个工作日内给出首次反馈。
5. 合并方式：Squash merge，合并信息沿用 PR 标题。

### PR 自检清单

- [ ] 已跑 §3 的六步本地验证
- [ ] 11 项质量门全过
- [ ] 视觉改动附 before / after 截图（同尺寸）
- [ ] 无第三方源码 / 图片 / 字体
- [ ] 无真实 sharecode / shareId / token / 内部地址
- [ ] 未提交构建产物与截图草稿
- [ ] 更新了 `CHANGELOG.md` 的 `[Unreleased]`
- [ ] 文档改动已自查错别字与失效链接

---

## 7. 发布（维护者）

1. 将 `[Unreleased]` 内容归入新版本小节，更新 `SKILL.md` 的 `version`。
2. 打 tag：`git tag -a v0.2.0 -m "v0.2.0"`，推送 tag。
3. Release 说明直接引用 `CHANGELOG.md` 对应小节。

---

## 8. 许可证

提交即表示你同意以 [MIT](LICENSE) 协议授权你的贡献。
