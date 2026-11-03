# Generative AI & RAG Notes

See also: `prompt_principles.md` (prompting) and `mlops_notes.md` (deployment/monitoring of models).

## RAG (Retrieval-Augmented Generation)

RAG gives an LLM access to external knowledge at query time: relevant documents are **retrieved** and injected into the **prompt** so the model **generates** an answer grounded in them. Reduces hallucinations and avoids retraining when data changes.

Base building blocks shared by all variants:
- **Data sources** → chunked and converted to vectors by an **embedding model**
- **Vector DB** → stores embeddings, does similarity search
- **Prompt template** → combines user query + retrieved context
- **LLM** → generates the output

## 8 RAG Architectures

### 1. Naive RAG
Query → embed → similarity search in the vector DB → top-k chunks + query in a prompt template → LLM → output.
- Simplest baseline. Weak when the query wording differs from the documents or retrieval returns irrelevant chunks.

### 2. Multimodal RAG
Same flow as naive RAG, but data sources include **images, video, audio, PDFs**. A multimodal embedding model (e.g. CLIP-style) puts text and media in the same vector space, and a multimodal LLM consumes the retrieved items.
- Use case: searching product photos, diagrams, scanned documents.

### 3. HyDE (Hypothetical Document Embeddings)
The LLM first writes a **hypothetical answer** to the query; that hypothetical document is embedded and used for the search instead of the raw query. The real retrieved documents + original query go to the LLM.
- Works because an answer-shaped text is closer in embedding space to real answers than a short question is.

### 4. Corrective RAG (CRAG)
After retrieval, a **grader** scores the relevance of each retrieved chunk. If they are poor/ambiguous, a **query analyzer** rewrites the query and the system **searches the web** for correct info, which is added to the context.
- Self-checking retrieval; improves robustness when the knowledge base is incomplete.

### 5. Graph RAG
A **graph generator** (LLM) extracts entities and relationships from the data sources into a **knowledge graph (graph DB)**. Retrieval combines vector search with **graph traversal** to bring in connected facts.
- Good for multi-hop questions ("which suppliers of X also work with Y?") and global summaries over a corpus.

### 6. Hybrid RAG
Retrieves from **both** a vector DB (context 1, semantic similarity) and a graph DB (context 2, structured relationships), then merges both contexts in the prompt.
- "Hybrid" is also often used for vector + keyword (BM25) search combined with re-ranking.

### 7. Adaptive RAG
A **query analyzer** classifies the query complexity and picks a strategy:
- simple → answer **directly** (no retrieval)
- normal → single-step retrieval
- complex → **multi-step reasoning chain** with several retrievals
- Saves cost/latency on easy questions while handling hard ones.

### 8. Agentic RAG
An **agent** with **memory** (short-term & long-term) and **planning** (ReAct, Chain-of-Thought) decides which tools to use. It can delegate to sub-agents, each connected through **MCP servers** to local data sources, search engines and cloud services. Retrieval becomes one tool among many, called iteratively until the agent is satisfied.
- Most flexible and powerful; also the most expensive and hardest to evaluate.

| Architecture | Key addition over naive RAG | When to use |
|--------------|----------------------------|-------------|
| Naive | — | Prototype, simple FAQ/doc QA |
| Multimodal | Non-text data + multimodal embeddings | Images, video, scanned docs |
| HyDE | Hypothetical answer used for search | Short/vague queries |
| Corrective | Relevance grading + web search fallback | Incomplete knowledge base |
| Graph | Knowledge graph | Relationship-heavy, multi-hop questions |
| Hybrid | Vector + graph (or keyword) contexts | Need both semantics and structure |
| Adaptive | Routing by query complexity | Mixed workloads, cost control |
| Agentic | Agents, memory, planning, tools (MCP) | Complex, multi-step tasks |

## Generative AI Project Structure

Recommended layout for an LLM application:

```
generative_ai_project/
├── config/                     # configuration separate from code
│   ├── __init__.py
│   ├── model_config.yaml
│   ├── prompt_templates.yaml
│   └── logging_config.yaml
├── src/
│   ├── llm/                    # one client per provider behind a common base
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── claude_client.py
│   │   ├── gpt_client.py
│   │   └── utils.py
│   ├── prompt_engineering/
│   │   ├── __init__.py
│   │   ├── templates.py
│   │   ├── few_shot.py
│   │   └── chainer.py
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── rate_limiter.py
│   │   ├── token_counter.py
│   │   ├── cache.py
│   │   └── logger.py
│   └── handlers/
│       ├── __init__.py
│       └── error_handler.py
├── data/
│   ├── cache/
│   ├── prompts/
│   ├── outputs/
│   └── embeddings/
├── examples/                   # implementation references
│   ├── basic_completion.py
│   ├── chat_session.py
│   └── chain_prompts.py
├── notebooks/                  # experimentation and analysis
│   ├── prompt_testing.ipynb
│   ├── response_analysis.ipynb
│   └── model_experimentation.ipynb
├── requirements.txt
├── setup.py
├── README.md
└── Dockerfile
```

**Best practices**
1. Use YAML for configuration files (models, prompts, logging).
2. Implement proper error handling (retries, timeouts, provider errors).
3. Use rate limiting for API calls.
4. Separate model clients behind a common interface (`base.py`) so providers are swappable.
5. Cache results appropriately (same prompt → cached response).
6. Maintain documentation.
7. Use notebooks for testing/experimentation, not for production code.

**Development tips:** modular design, write component tests, use version control, keep docs updated, **monitor API usage** (tokens and cost).

**Getting started:** clone repo → install `requirements.txt` → configure model settings → review example code → start with the notebooks.

## Claude Skills: "One Model, Every Department"

Idea from an infographic: one model (Claude) acting as orchestrator, extended with **installable skills** (packaged instructions + scripts) to cover the work of different departments.

| Department | Skills / sources |
|------------|------------------|
| Developers | Superpowers (`github.com/obra/superpowers`), Context7 (`github.com/upstash/context7`), Skill Creator, MCP Builder, Webapp Testing (`github.com/anthropics/skills`), Claude-Mem (`github.com/thedotmack/claude-mem`) |
| Designers | UI UX Pro Max (`github.com/nextlevelbuilder/ui-ux-pro-max-skill`), Taste / Frontend Design (`github.com/Leonxlnx/taste-skill`), Transitions (`github.com/Jakubantalik/transitions.dev`), Web Artifacts, Brand Guidelines (`github.com/anthropics/skills`) |
| Marketing | ~45 skills for copywriting, SEO, lead magnets, campaigns (`github.com/coreyhaines31/marketingskills`) |
| Social media | ~17 skills for posts, Reels, thumbnails, content ops (`github.com/charlie947/social-media-skills`) |
| Finance | Statements, reconciliation, audits (`claude.com/plugins/finance`) |
| Small business | Cash flow, payroll, invoicing, operations (`claude.com/plugins/small-business`) |
| Legal | Contract review, NDAs, compliance (`claude.com/plugins/legal`) |

**Takeaway:** skills/plugins are the reusable unit for specialized workflows — similar in spirit to the tools/MCP servers in Agentic RAG. Review third-party skills before installing them, since they run code and instructions in your environment.
