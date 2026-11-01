# Changelog

本项目的所有重要变更都会记录在此文件。
格式参考 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)，版本号遵循 [语义化版本](https://semver.org/lang/zh-CN/)。

## [Unreleased]

（暂无）

## [0.1.0] - <YEAR>-<MM>-<DD>

首个公开版本。

### Added

- `SKILL.md`：Skill 本体。英文 YAML frontmatter（`name` / `description` / `version` / `author` / `trigger`），
  正文为完整渲染指令：舞台与缩放、设计令牌、字阶、栅格、图版处理、四个动效原语、
  十种逐页版式原型、书眉与进度、缺图占位、`prefers-reduced-motion` 终态，
  以及六步执行流程与 11 项交付前检查。
- 运行时参数契约：标题、页数、内容路径、图片目录、输出路径、强调色、动效档位，
  全部由使用者运行时传入；仓库内不保存任何地址、ID、sharecode 或 token。
- 内容输入契约：Markdown 约定与 JSON 结构两种，统一归一化为页对象。
- 交付形态：单文件自包含 HTML（零子资源请求，`file://` 可开、断网可开）。
- 开源配套：`README.md`（中文，含免责声明与能力/触发词）、MIT `LICENSE`、`.gitignore`、
  `CONTRIBUTING.md`、`CODE_OF_CONDUCT.md`、`SECURITY.md`、Issue/PR 模板。
- `assets/preview.png`：效果预览图（1600×900）。画面为被复刻页面的第三方截图，仅作效果引用，
  权利归原方，不在本项目 MIT 授权范围内；可随时替换为自己产出的截图。

### Known limitations

- 固定 16:9 横版，竖屏不重排版式。
- 不生成图片、不做复杂图表、不导出 pptx/pdf。
- 依赖本地思源系字体，缺失时降级为系统衬线/无衬线。
