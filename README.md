# ResearchHub

> 学术文献研究与播客生成技能

## 功能特性

- 🔍 **文献搜索** - 从 arXiv、OpenAlex 搜索学术论文
- 📝 **智能摘要** - 使用 LLM 生成论文摘要
- 🎙️ **音频播客** - 将摘要转为语音播客
- 📓 **Notebook 管理** - 创建和组织研究笔记
- 🔎 **全文搜索** - 跨笔记本搜索论文

---

## 在 OpenClaw 中使用

### 方式一：直接调用 Agent 模块

在 OpenClaw 会话中可以这样使用：

```python
# 导入模块
import sys
sys.path.insert(0, '/Users/liweixie/.openclaw/skills/research-hub')

from agents.search_agent import SearchAgent
from agents.summary_agent import SummaryAgent
from core.orchestrator import Orchestrator

# 1. 搜索论文
search_agent = SearchAgent()
papers = search_agent.search_arxiv("machine learning", max_results=5)

# 2. 生成摘要
summary_agent = SummaryAgent()
for paper in papers:
    summary = summary_agent.summarize(paper)
    print(f"{paper['title']}: {summary[:100]}...")
```

### 方式二：使用 Workflow

```python
# 完整研究流程
orchestrator = Orchestrator()

result = orchestrator.run(
    query="transformer attention",  # 搜索关键词
    max_results=5,                 # 返回数量
    generate_audio=True,           # 是否生成音频
    classify_method="tfidf"        # 分类方法
)

# 查看结果
print(f"找到 {result['stats']['total_papers']} 篇论文")
print(f"主题分类: {result['stats']['topics']}")

# 获取摘要
for topic, synthesis in result['synthesis'].items():
    print(f"\n=== {topic} ===")
    print(synthesis['synthesis'][:500])
```

### 方式三：Notebook 管理

```python
from storage.database import Database

db = Database()

# 创建笔记本
nb_id = db.create_notebook("我的Transformer研究", "关于Transformer架构的论文收集")

# 添加论文
paper = {
    "title": "Attention Is All You Need",
    "authors": ["Vaswani et al."],
    "published": "2017",
    "summary": "We propose the Transformer model...",
    "link": "https://arxiv.org/abs/1706.03762"
}
db.add_paper(nb_id, paper)

# 全文搜索
results = db.search("transformer attention")
for p in results:
    print(f"- {p['title']}")
```

---

## 命令行使用

## 项目结构

```
research-hub/
├── agents/           # 智能代理
│   ├── search_agent.py
│   ├── processing_agent.py
│   ├── classification_agent.py
│   ├── summary_agent.py
│   ├── synthesis_agent.py
│   └── audio_agent.py
├── core/             # 核心模块
│   └── orchestrator.py
├── storage/          # 存储模块
│   └── database.py
├── prompts/          # 提示词模板
└── research_hub.py   # 主入口
```

## 配置

### LLM 客户端

```python
from research_hub import Orchestrator

# 使用 OpenAI
llm_client = OpenAIClient(api_key="sk-...")

# 使用 Anthropic
llm_client = AnthropicClient(api_key="sk-...")

orchestrator = Orchestrator(llm_client=llm_client)
```

### 音频引擎

```python
# 使用 gTTS (免费)
orchestrator = Orchestrator(audio_engine="gtts")

# 使用 ElevenLabs (高质量)
orchestrator = Orchestrator(
    audio_engine="elevenlabs",
    elevenlabs_api_key="your-api-key"
)
```

## 数据存储

- 数据库: `~/.openclaw/data/research-hub/research_hub.db`
- 音频: `~/.openclaw/data/research-hub/audio/`

## License

MIT
