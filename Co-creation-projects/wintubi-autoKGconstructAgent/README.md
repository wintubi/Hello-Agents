# wintubi-autoKGconstructAgent（多模态 KG 构建 + GraphRAG/LightRAG 脚手架）

> 目标：用“图片定位/识别 + LLM 抽取”跑通 **LPG（属性图谱）** 的全链路，并兼容 GraphRAG/LightRAG 思路：
> - 实体：团体 / 人 / 事件 / 位置 / 组织（可扩展）
> - data gleaning：结构规则补全边
> - 文本/图片 → 图谱（节点/边带属性与证据）
> - 查询：实体检索、邻域、社区
> - 社区检测 + 层级化总结 + 推理补全
> - “超越文本匹配”：图结构 + 语义相似度检索

## 目录结构

```
wintubi-autoKGconstructAgent/
  README.md
  requirements.txt
  .env.example
  main.ipynb
  data/
    sample_article.md
    image.png
    sample.csv
    sample_code.py
    test_cases.json
  src/
    app.py
    schema.py
    llm.py
    ocr.py
    extract.py
    graph_store.py
    gleaning.py
    community.py
    query.py
    integrations/
      graphrag_runner.py
      lightrag_runner.py
    agents/
    tools/
    utils/
```

## 快速开始（无 API 也可跑）

1) 安装依赖

```bash
pip install -r requirements.txt
```

2) 运行文本端到端 Demo（不调用真实 LLM，使用启发式抽取）

```bash
python -m src.app demo --input data/sample_article.md --out outputs
```

产物：
- outputs/graph.json：抽取后的属性图谱（LPG）
- outputs/communities.json：社区检测结果
- outputs/hierarchy_summary.md：层级化总结（无 LLM 时为启发式）

3) 启用 LLM 抽取（可选）

```bash
cp .env.example .env
# 填入 OPENAI_* 或 OLLAMA_* 后
python -m src.app demo --input data/sample_article.md --out outputs --llm
```

4) 图片 OCR（可选）

```bash
python -m src.app ocr --image data/image.png --out outputs
```
会输出 ocr_text.txt 与 ocr_debug.json（包含检测框/置信度，且日志会标明使用的 OCR 后端）。

5) GraphRAG / LightRAG（可选接入）

```bash
python -m src.integrations.graphrag_runner --check
python -m src.integrations.lightrag_runner --check
```
它们只做依赖检测与占位；如果要接官方实现，请按提示安装后在此处接入索引/检索逻辑。

## 设计要点
- 多模态入口：OCR（PaddleOCR 优先，失败/缺失时回退 pytesseract，带告警日志）。
- 图谱：LPG（节点/边含属性、可扩展属性），支持 rule-based gleaning 边推理。
- 社区与摘要：networkx 社区检测 + 启发式层级总结（可进一步用 LLM 强化）。
- 查询：名字子串 + TF-IDF 语义检索 + 邻域探索；对空图和超大 hops 做防护。
- 可配置：LLM 请求超时通过环境变量 LLM_TIMEOUT_SECONDS 控制，默认 120s。

## 外部资源
- 中国法书全集（百度网盘）：https://pan.baidu.com/s/1ovsluXjRIjPnjGfl6SktOw?pwd=j46i

## 作者
- GitHub: @wintubi
- Email: 1319098922@qq.com

## 致谢
- Datawhale 社区与 Hello-Agents 项目
