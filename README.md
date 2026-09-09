# AI Icon Studio

结合产品与名称，探索原创图标；先比较轮廓、留白和色彩，再交给 Apple Icon Composer 完成材质与平台适配。名称关联可以很轻，不必画成字母或实物。

包含一个 Codex 插件和两个可选脚本，不需要 npm 包、MCP 或常驻服务。插件负责创意方向、生成调用、小尺寸评审和交付；Icon Composer 负责原生 `.icon` 文档。可以使用内置生图，也可以在授权后通过 OpenRouter 调用图片模型。不会自动发布或替换用户尚未确认的图标。

- `plugins/ai-icon-studio/skills/icon-design/SKILL.md`：插件入口。
- `scripts/openrouter-image.mjs`：技能目录内的可选图片生成适配器，Node.js 22+，密钥通过环境变量注入。
- `scripts/icon-assets.py`：技能目录内的透明素材整理及 ICNS 打包，Python + Pillow；ICNS 需要 macOS。

MiniBridge 已采用 B2：将选中的栅格轮廓重建为两个独立 SVG 图层，完成 Icon Composer 原生 Default/Dark 导出和 ICNS 集成。MiniDock 采用 N2 Little Lookout；经过眼睛与耳朵的整体姿态修正，已生成原生 Default 预览并集成本地 App。MiniDock 使用整张栅格图层与手动 Dock 留白，不声称具备独立矢量图层或完整 Dark 适配。

品牌：ailuntz · https://www.ailuntz.com。公共插件版本：0.1.1。使用说明与脚本包含在插件内，无需 npm 安装。

已发布到官方目录，分类为 Creativity：[安装 AI Icon Studio](https://chatgpt.com/plugins/plugins_6aa19a8879108191825af4fe2523aee3) · [下载发布包](https://github.com/ailuntx/ai-icon-studio/releases/tag/v0.1.1)。

[Privacy](docs/privacy.md) · [Terms](docs/terms.md) · [Support](https://github.com/ailuntx/ai-icon-studio/issues)

开发验证：`node --test tests/generation.test.mjs`，以及在安装 Pillow 的 Python 环境中运行 `python -m unittest discover -s tests -p 'test_*.py'`。
