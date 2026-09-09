# MiniDock 实测记录

日期：2026-09-10。此文件按时间记录迭代；早期方案状态仅描述当时结果。最新发布检查见文末。

- 生图：OpenRouter `openai/gpt-image-2`，3 次 low / 1:1，实际均为 1024 × 1024。费用分别 $0.00734、$0.007245、$0.00726，总计 $0.021845。精确提示词及安全费用记录保存在 MiniDock-exploration。
- Node 自动测试 3 项通过：单次低质量调用、密钥仅用于认证头、错误不读取原始正文、缺失密钥和非 PNG 响应拒绝。
- Python 自动测试 3 项通过：外部白底去除保留封闭白色细节、原文件及透明度不变并禁止覆盖、10 个 iconset 文件和 ICNS 解码回读。
- Composer 实测：创建、导入一个透明前景、关闭该层 glass、设置 System Light 背景、保存 `.icon`、关闭后重新打开，1 layer / 1 group，图层存在。
- 已看默认、Dark、Mono 原生效果。System Light 背景在 Dark 预览会自动适配；没有测试所有 Clear/Tinted 组合，不能标为全外观验收。
- 已从 Composer 导出默认 1024 PNG，以及 macOS pre-Tahoe 1024 PNG；后者打包成 MiniDock.icns 并解码回读成功。
- 生图源本身是白底 RGB，透明 PNG 是显式近白底处理结果，不是模型原生透明；深色背景检查过，微小边缘仍应在最终精修时复核。
- 黑白方向深色对比不足，已加入插件的评审要求。彩色方案有轻微笑脸联想，是否符合 MiniDock 品牌由用户选择。
- 本次只增加候选目录；CapDock 正式图标、构建脚本和应用标识均未替换。

插件实现采用技能自带脚本，不需要 npm 包。保留独立 CLI 包作为以后确有跨工具用户需求时的选择。公共发布前仍需用户认可视觉方向、插件自身展示图和提交材料。

## 第二轮：MiniDock + Bridge

- 首轮共同问题：三个方案沿用托盘加两个竖块，轮廓变化不足，名称关联过于直白。
- 本轮用八条独立提示生成八张 1024 × 1024 图，OpenRouter openai/gpt-image-2 / low；无参考图输入，费用总计 $0.055360。两组各 $0.027680。
- 实际检查全部源图及小尺寸对比；MiniDock M2 有吃豆人联想，M3 仍有通用旋涡感；Bridge B2 有咖啡豆联想。保留这些结果便于评审，不把生图成功记为审美通过。
- 对比页采用模拟圆角；深色周边不是原生 Dark 适配。第二轮尚未进入 Composer 或替换正式资源。
- 插件调整：轻度名称联想、不同轮廓探索、完整图标构图与后续分层分开；补充公开提示词来源和模型适用范围。
- 本轮未改执行脚本；沿用已有六项通过的自动测试。新的 manifest 和 skill 校验通过，八次实际调用均成功。

## 参考图与正式集成验证

- 用户选择 Bridge B2，并将项目改名 MiniBridge。B2 轮廓重建为两个独立 SVG，经 Icon Composer 创建、保存、重新打开验证；Default/Dark 的 macOS pre-Tahoe 导出已用于实际 App。
- 发现并修正导出差异：本机 Icon Composer 1.6 的 ictool --platform macOS 输出贴边图形，UI 的 macOS pre-Tahoe 包含 Dock 留白和阴影；不能混用。
- MiniDock 四次真实调用均传入用户提供的参考图，receipt reference_count 为 1、SHA-256 相同，总费用 $0.050294。四张均经过 ictool 原生 Default 预览；这些是整张栅格预览，不代表独立编辑图层或完整 Dark 适配。
- 生成脚本新增 reference 参数：PNG/JPEG 检查、10 MiB 文件限制、inline data URL、引用摘要；不暴露凭据、响应原文或引用图片字节到日志。Node 五项测试通过，包括未指定质量时的 CLI 参考图解析与无付费请求下拒绝非图片。
- MiniBridge Node 61 项、Swift 69 项测试通过，Release 构建与签名校验通过，应用进程启动；ICNS 解码为 10 个 PNG。读取 MiniBridge 窗口时 Computer Use 的 native pipe 关闭，故不将窗口交互检查记为通过。


## 0.1.0 发布检查

- Node 5 项、Python 3 项自动测试通过；未为重复检查产生新的付费生成请求。
- MiniDock 已选 N2 Little Lookout。第一次双眼修改被用户否决：在侧脸轮廓上补眼睛，造成眼线、耳朵和头部角度不一致。
- v4 将眼睛、耳朵、嘴部与头骨作为一个整体重画，保留墨绿配色和右下探头构图。原生 Default 预览检查包含 16/32/64px；App 构建成功，10 个 iconset 文件存在，打包资源与源 ICNS 一致。
- v4 调用费用 $0.016732。采用单张栅格图层及手动 824/1024 Dock 留白；不是 UI macOS pre-Tahoe 导出，不代表完整 Dark 适配。
- MiniBridge npm 0.1.3 已从公开 registry 下载验证；Node 61 项、Swift 70 项测试通过，新旧 CLI 兼容和全局包共存验证通过。这些是示例项目验证，不算作插件自动测试数量。
- 插件新增角色图标检查：眼睛、耳朵、嘴部和头骨服从同一姿态；视角变化时连带重画，避免只粘贴局部特征。
