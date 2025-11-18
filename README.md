# 地方债相似属性筛选工具（Streamlit）

这是一个使用 Streamlit 构建的网页工具，支持上传 `.xlsx` 或 `.csv` 的地方债数据，基于剩余年限、票面利率、专项/一般、区域等级、发行年份、是否交税等属性进行分级放松的相似券匹配，并提供 Altair 可视化。

## 快速运行（本地）
- 安装依赖：`pip install -r requirements.txt`
- 启动：`streamlit run app.py`
- 浏览器访问：`http://localhost:8501`

## 云端分享部署建议

### 1) Streamlit Community Cloud（最简单，推荐）
适合直接分享 Streamlit 应用，无需改造 UI。

步骤：
1. 将本项目推送到 GitHub 仓库（包括 `app.py` 和 `requirements.txt`）。
2. 访问 `https://share.streamlit.io`，使用 GitHub 登录，创建新应用。
3. 选择你的仓库，入口文件填写 `app.py`。
4. 等待构建完成，获得一个公开访问的 URL，直接分享即可。

注意：Excel 读取 `.xlsx` 需要 `openpyxl`，读取 `.xls` 需要 `xlrd`，已在 `requirements.txt` 中加入。

### 2) Hugging Face Spaces（也很方便）
无需改代码，可获得公开 URL。

步骤：
1. 在 Hugging Face 创建一个 Space，`SDK` 选择 `Streamlit`。
2. 将代码与 `requirements.txt` 上传到 Space。
3. 在 `README.md` 顶部加上配置块（可选，明确入口文件）：

```
---
title: 地方债相似属性筛选工具
emoji: 🧮
colorFrom: blue
colorTo: green
sdk: streamlit
sdk_version: 1.27.0
app_file: app.py
pinned: false
---
```

4. 构建完成后即可获得公开 URL。

### 3) Vercel（说明）
Vercel 目前不支持直接托管需要长时间常驻的 Python/Streamlit 服务器。其 Python 运行时主要用于“按请求执行”的无状态 Serverless 函数，不支持 Streamlit 的会话与 WebSocket 机制。

如果必须使用 Vercel，请采用“前后端分离”的方案：
- 前端：用 Next.js 在 Vercel 部署一个网页（文件上传 + 参数表单 + 图表展示）。
- 后端：将本项目的核心筛选逻辑封装为 Python API（如 FastAPI），部署到支持常驻服务的平台（Render、Railway、Fly.io、Deta、或 Hugging Face Spaces）。
- 前端通过 HTTP 将文件与参数上传到后端，后端返回筛选结果与可视化所需数据（JSON），前端用 `react-vega`/`echarts` 绘制图表。

迁移要点：
- 把 `load_data`、`find_matching_bonds_by_level`、`find_matching_bonds_with_fallback` 等函数抽到独立模块，提供一个 `POST /analyze` 接口接收文件与参数并返回结果。
- 将 Altair 图表转换为前端可渲染的 Vega-Lite 规范或由后端返回可绘制的数据。

## 数据要求与列说明
- 关键列名：`发行日期`、`当前日期`、`剩余年限`、`收盘收益率`、`估值`、`票面`、`余额`、`成交量`、`债券代码`、`债券名称`、`是否交税`、`专项一般`、`区域`。
- 应用会自动生成：`区域等级`、`发行年份` 等。
- 会按最近 5 个交易日过滤数据并用于绘图与筛选。

## 常见问题
- 无法匹配：请检查省份关键词是否能匹配到 `区域` 列中的某个省份；或调整票面/年限/年份等。
- Excel 解析错误：确保上传文件为 `.xlsx`/`.xls`/`.csv`，且有上述列名。

## 许可证
内部业务工具，按需分享。