# isms-document-search-agent-poc
A self-hosted n8n and Claude RAG pipeline that automates the search for ISO 27001 documents during gap assessments and auditing


## 1. What This Is

This project is a Proof of Concept (PoC) for a tool that automates the search for ISMS documents. By connecting an automated orchestration pipeline to organizational ISMS files, it acts as an intelligent assistant that verifies whether required security controls are documented/evidenced. It provides GRC and information security professionals with structured security control substantiation & evidence verification without manual folder skimming.

## 2. Demo

## 3. How It Works
The workflow operates locally as a deterministic sequence of eight operational nodes running on every incoming user query:

```text
[Chat Trigger] ➔ [Fetch ISO 27002] ➔ [Search Evidence Folder] ➔ [Download Files]
                                                                        │
[Chat Interface] 🖙 [HTTP Request (Claude)] 🖘 [Code Node (JS)] 🖘 [Merge (Append)]
