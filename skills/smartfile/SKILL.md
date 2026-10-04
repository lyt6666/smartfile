---
name: smartfile
description: 用智能文件归类助手（smartfile MCP）按内容归类、建索引、检索本地文件；组织散乱文件、找相关文档时使用。
---

# 智能文件归类助手

用 `smartfile` MCP 工具（已注册为 MCP server "smartfile"）做文件归类与检索。

## 何时用

- 用户说「整理/归类/分类」某个文件夹的文件
- 用户问「找一下跟 X 相关的文件/文档」
- 用户要「重建索引」让检索变快

## 可用工具（MCP）

- `smartfile_organize` — 按内容归类。参数：`dir`（必填，绝对路径）、`config`（配置路径，可选）、`dry_run`（默认 true，先预览）、`action`（copy/move，默认 copy）
- `smartfile_search` — 检索相关文件。参数：`dir`（必填）、`query`（必填，多词空格分隔）、`limit`
- `smartfile_index` — 重建索引（倒排索引 + BM25），之后 search 变快

## 使用纪律

1. **归类前先 dry_run 预览**，把结果给用户看，别直接 move/copy 破坏文件。
2. **规则来自用户的 config.json**，不要自己臆造分类规则。如果用户没给 config 路径，先用 `smartfile_organize` 不传 config 跑一次（走默认「未分类」兜底），或询问用户 config 在哪。
3. **检索前可先 `smartfile_index` 建索引**（若目录文件多），让 search 走倒排索引毫秒级返回；小目录可直接 search。
4. **`action` 默认 copy 不动原件**，只有用户明确要求移动才用 `move`。
5. 归类/检索结果都要**结构化汇报**：列出「文件 → 归类到哪个文件夹 / 检索相关度」，不要含糊。

## 常见流程

**整理文件夹**：
1. `smartfile_organize`（`dry_run: true`）→ 把预览结果给用户确认
2. 用户确认后，再用 `dry_run: false` 真正执行

**找文件**：
1. （可选）`smartfile_index` 建索引
2. `smartfile_search` 检索，报告命中的文件和相关度排序

## 备注

- 工具本体在 `/Users/liuyangtao/.openclaw/workspace/file-tools/organizer/`，CLI 也可直接用 `python3 smartfile.py ...`。
- config 示例：`config.example.json`（分类规则 + AI 接口，AI 支持 Ollama 和 OpenAI 兼容 API）。
