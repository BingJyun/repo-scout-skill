---
name: repo-scout
description: Comprehensive GitHub repo analysis covering architecture, OWASP Top 10 security scanning (with file path + line number + fix), 4-dimension strategic value assessment (cost savings, efficiency, startup opportunities, community impact), auto-competitor discovery, and optional Traditional Chinese audio review via NotebookLM. Use this skill when the user wants to analyze, audit, evaluate, scout, or assess any GitHub repo or local codebase. Also trigger when the user asks about a repo's security risks, tech stack, strategic value, or wants to compare repos. Trigger for any prompt containing /repo-scout.
---

# /repo-scout

Scout GitHub repos for security, value, and opportunity — with optional audio review.

## Usage

```
/repo-scout <url-or-path> [url2] [url3] [--discover] [--audio]
```

**Examples:**
- `/repo-scout https://github.com/user/repo` — single repo full analysis
- `/repo-scout https://github.com/a https://github.com/b` — compare repos
- `/repo-scout https://github.com/user/repo --discover` — auto-find similar repos by stars
- `/repo-scout https://github.com/user/repo --audio` — include Traditional Chinese Audio Overview
- `/repo-scout https://github.com/user/repo --discover --audio` — full power

---

## What This Skill Does

Performs a comprehensive analysis of GitHub repos, covering:
1. **Architecture & functionality** (accelerated by DeepWiki MCP)
2. **Security scanning** (OWASP Top 10, hardcoded secrets, dependency audit)
3. **Strategic value assessment** (4 dimensions: save money, boost efficiency, startup opportunities, community impact)
4. **Competitor discovery** (auto-search similar repos by stars)
5. **Audio review** (Traditional Chinese podcast-style via NotebookLM)

---

## Dependencies Check

Before starting, verify these MCP servers are available. If any are missing, guide the user to install:

```bash
# Required: DeepWiki MCP (free, no auth)
# Docs: https://docs.devin.ai/work-with-devin/deepwiki-mcp
claude mcp add -s user -t http deepwiki https://mcp.deepwiki.com/mcp

# Required for --audio: NotebookLM MCP CLI (supports create/upload/generate)
uv tool install notebooklm-mcp-cli   # uv recommended by repo
nlm login                             # browser OAuth, one-time
nlm setup add claude-code             # auto-registers MCP for Claude Code

# Optional: Trail of Bits security skills (enhanced scanning)
# Run inside Claude Code:
/plugin marketplace add trailofbits/skills
```

---

## Step-by-Step Workflow

### Step 1 — Parse Arguments

Parse the user's input:
- Extract URLs and/or local paths
- Detect flags: `--discover`, `--audio`
- If multiple URLs → multi-repo comparison mode
- Determine output directory (same directory as the repo, or working directory for URLs)

### Step 2 — Quick Reconnaissance via DeepWiki (for GitHub URLs)

For each GitHub URL, extract `owner/repo` from the URL and use DeepWiki MCP:

1. Call `read_wiki_structure` with `owner/repo` to get the full documentation structure
2. Call `read_wiki_contents` to get architecture overview and core functionality docs
3. Call `ask_question` with: "What does this project do? What's the tech stack? What are the main features and API endpoints?"

This gives you a comprehensive overview in seconds — **no clone needed yet**.

If DeepWiki MCP is not available, fall back to cloning and reading README manually.

### Step 3 — Clone Source Code

Clone to `/tmp/repo-scout/<repo-name>/` (security scanning requires actual source code).

```bash
git clone --depth 1 <url> /tmp/repo-scout/<repo-name>/
```

For local paths, use them directly.

### Step 4 — Deep Exploration (2 Agents in Parallel)

Launch 2 Explore agents simultaneously in a single message:

**Agent 1 — Architecture, Features & Ecosystem Health:**
- Use DeepWiki overview as starting context (don't explore from scratch)
- Supplement with details DeepWiki missed: implementation logic, code quality
- Verify DeepWiki's descriptions against actual source code
- Also record: GitHub stars, forks, open issues, recent commit frequency, license type and commercial use restrictions
- Output: complete technical overview with tech stack, features, API surface, and project health summary

**Agent 2 — Security Deep Scan:**
Focus on security hotspots — do NOT read every file. Target:
- Entry points and routing files
- Authentication and authorization modules
- Files that handle user input or external data
- Config and environment files (.env, config.*)
- Dependency manifests (package.json, go.mod, Cargo.toml, requirements.txt)
- Docker configs and CI pipeline files

Check for:
- OWASP Top 10 (injection, broken auth, XSS, SSRF, etc.)
- Hardcoded secrets, API keys, tokens, credentials
- Dependency vulnerabilities (check manifest file versions)
- Docker security (root user, exposed ports, secrets in image)
- Input validation (SQL injection, command injection, path traversal)
- Authentication/authorization flaws
- CORS, CSRF, security headers
- Encryption practices (TLS, password storage, token handling)
- Output: vulnerability list with severity (CRITICAL/HIGH/MEDIUM/LOW), file path, line number, and fix suggestion

If Trail of Bits skills are installed, additionally run:
- Sharp Edges scan (dangerous APIs, footgun designs)
- Audit Context Building (cross-function data flow tracing)

### Step 5 — Auto-Discover Similar Repos (if --discover)

1. Based on Step 4 classification, use WebSearch:
   - `"[category] open source alternative site:github.com stars"`
   - `"best [keyword] library github popular"`
   - `"awesome-[category] github list"`

2. Find 3-5 most popular similar repos (sort by stars)

3. For each discovered repo:
   a. Use DeepWiki MCP for instant overview (no clone needed)
   b. Use `ask_question` for security-related questions
   c. Only the top 1-2 most promising repos get full clone + source security scan

4. Produce comparison matrix

### Step 6 — Strategic Value Assessment

Launch a **general-purpose Agent** for creative ideation. Provide as context:
- Full repo overview from Step 4 (tech stack, features, category)
- User memories (role, tools, budget, goals)

Instruct the Agent to evaluate from 4 dimensions, with emphasis on concrete startup ideas:

**1. 💰 Cost Savings Potential**
- What paid services does this replace? How much saved?
- ROI calculation

**2. 🚀 Efficiency Boost**
- What processes does it automate?
- How much development time saved?

**3. 🏢 Startup Opportunities (New Projects + Revenue Potential Combined)**
This is the most important dimension. Think ambitiously:
- What specific real-world problems can this technology solve commercially?
- Products can be physical or virtual — SaaS, API services, physical hardware integration, consulting, training courses, etc.
- Use WebSearch to check if similar products already exist (avoid saturated markets)
- Each idea must have a concrete form: who pays, how much, what problem is solved
- Consider derivative products that go beyond the repo's obvious use case

**4. 🌍 Community Impact (Brief, 3 points max)**
- Key contribution opportunities (PRs, issues)
- One notable community or educational angle

Be **creative and ambitious** in dimension 3. Do not give generic advice that applies to every repo.

### Step 7 — Generate Report

Write the report as a structured MD file.

**Single repo:** `<repo-name>-scout-report.md`
**Multi-repo comparison:** `<category>-comparison-report.md`

Use the report template below. Write in **Traditional Chinese (繁體中文)**.

### Step 8 — Audio Overview (if --audio)

**This step MUST succeed. No fallback. No degradation.**

1. Verify `notebooklm-mcp-cli` MCP server is connected
   - If not connected → Stop and guide installation:
     ```bash
     uv tool install notebooklm-mcp-cli
     nlm login
     nlm setup add claude-code
     # Then restart Claude Code to load the new MCP
     ```
   - If auth failed → Guide re-authentication: `nlm login`
   - Keep retrying until connected successfully

2. Create notebook using MCP tool `notebook_create`:
   - Title: "[Repo Name] Scout Report"

3. Add the report as source using MCP tool `source_add`:
   - Type: "Text"
   - Content: full markdown content of the report generated in Step 7

4. Generate Audio Overview using MCP tool `studio_create`:
   - Type: "audio"
   - Language: "zh-TW" (Traditional Chinese)
   - Custom prompt: "As a two-host podcast conversation, discuss this technical reconnaissance report in depth. The show is divided into three acts: Act One (Security): Analyze the discovered vulnerabilities in detail, explain how attackers would actually exploit them, and prioritize fixes; Act Two (Business Opportunities): Mine startup ideas from this technology—what specific problems can be commercialized? What forms could derivative products take (physical or virtual services)? Act Three (Action): If the audience members are developers or product managers, what three actions should they take in the next two weeks? Use a lively tone, avoid simply reading the report, and make it understandable for non-technical listeners."

5. Note the notebook URL and inform the user:
   - Print the notebook URL (format: `https://notebooklm.google.com/notebook/<id>`)
   - Inform user: "Audio generation started (approximately 10–20 minutes). Once complete, you can listen at the following link: [notebook URL]"
   - Update the report's Audio Review field with the notebook URL

---

## Report Template — Single Repo

```markdown
# [Repo 名稱] 偵察報告

> 🔍 分析日期：YYYY-MM-DD
> 📦 來源：[GitHub URL]
> 🏷️ 版本：vX.X.X
> ⭐ Stars：X,XXX
> 🎙️ Audio Review：[NotebookLM 連結]（音檔生成中，約 10–20 分鐘）

---

## 一、專案概覽
### 一句話定位
### 核心原理 / 架構
### 技術棧
### 主要功能清單
### 專案健康度（stars / 維護活躍度 / license）

## 二、安全掃描
### 📊 安全評級總結（統計圖）
### CRITICAL（嚴重）— 每項含：檔案路徑、行號、說明、修復建議
### HIGH（高風險）
### MEDIUM（中風險）
### LOW（低風險）
### 🔧 修復優先序建議

## 三、戰略價值評估
### 💰 省錢潛力
### 🚀 效率提升
### 🏢 創業機會（含衍生產品：實體 / 虛擬服務）
### 🌍 社群影響力

## 四、同類工具比較（--discover 模式）
### 比較矩陣
### 推薦排名與理由

## 五、技術學習價值
### 值得研究的技術點
### 對應源碼檔案與行號

## 六、結論與行動計畫
### 一句話結論
### 🎯 立即行動（This Week）
### 📋 短期計畫（This Month）
### 🚀 長期機會（This Quarter）
```

## Report Template — Multi-Repo Comparison

```markdown
# [類別] 工具戰略比較

> 分析日期：YYYY-MM-DD
> 比較對象：repo1, repo2, repo3
> 自動發現的 repo：repo4, repo5

## 功能對照矩陣
| 功能 | Repo A (⭐ X,XXX) | Repo B (⭐ X,XXX) | Repo C (⭐ X,XXX) |

## 安全評級比較
| 漏洞等級 | Repo A | Repo B | Repo C |

## 商業價值比較
## 技術學習價值比較
## 🏆 最終推薦與理由
## 行動計畫
```

---

## Key Principles

- **Language**: Always write reports in Traditional Chinese (繁體中文)
- **Security**: Be focused — target security hotspots, report with file path + line number
- **Value**: Be creative and ambitious — startup ideas must be concrete with a clear business model, not generic suggestions
- **Speed**: Use DeepWiki first, clone only when needed for security scanning
- **Audio**: --audio must succeed, never skip or degrade; trigger generation and provide notebook URL (no polling or download)
- **Comparison**: --discover should find real competitors with high stars, not obscure repos
