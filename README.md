# isms-document-search-agent-poc
A self-hosted n8n and Claude RAG pipeline that automates the search for ISO 27001 documents during gap assessments and auditing


## 1. What This Is

This project is a Proof of Concept (PoC) for a tool that automates the search for ISMS documents. By connecting an automated orchestration pipeline to organizational ISMS files, it acts as an intelligent assistant that verifies whether required security controls are documented/evidenced. It provides GRC and information security professionals with structured security control substantiation & evidence verification without manual folder skimming.



## 2. Demo

▶️ [**Click here to watch the full 93-control scan execution demo**](https://youtu.be/sJhKgj26Nsw)

Note: The video demonstrates a full 93-ISO-control scan. The entire automated sequence finishes in ~75 seconds and is at least an order-of-magnitude reduction in time compared to manual auditing.

## 3. How It Works
The workflow operates as a deterministic sequence of eight nodes that run after a user's query:
```text
[Chat Trigger] ➔ [Fetch ISO 27002] ➔ [Search Evidence Folder] ➔ [Download Files]
                                                                        │
[Chat Interface] 🖙 [HTTP Request (Claude)] 🖘 [Code Node (JS)] 🖘 [Merge (Append)]
```
* **When chat message received:** The entry point. A built-in n8n Chat Trigger captures the user's query from the interface. It uses "Using Response Nodes" mode to defer the final response to the end of the pipeline.
* **Fetch ISO 27001 Reference:** A Google Drive Download node that pulls the core ISO 27002:2022 standard text from a designated Reference folder. It exports it on the fly as plain text (`.txt`) via Google File Conversion, storing the binary data in a named field (`isoData`).
* **Search Evidence Folder:** A Google Drive Search node that queries the target operational folder using advanced parameters (`[folder ID] in parents and trashed = false`) to identify all compliance evidence files.
* **Download file:** A Google Drive Download node that loops through the identified evidence file IDs, downloading each sequentially as plain text. 
* **Merge (Append):** A structural node that merges the single ISO reference document (Input 1) and the multiple downloaded evidence files into a combined stream of 8 items. Positioning the ISO document at index 0 is vital for downstream processing.
* **Code in JavaScript:** A Code node that extracts the binary text of all 8 items using n8n's `getBinaryDataBuffer` helper. It decodes the bytes into UTF-8 text and structures them into a single comprehensive string variable (`combinedDocuments`), cleanly separating and labeling each document by its filename.
* **HTTP Request:** A POST request that sends the payload directly to the Anthropic API endpoint (`https://api.anthropic.com/v1/messages`). The request contains the model parameter, the strict system prompt, and the payload containing `combinedDocuments` along with the user's original query.
* **Chat (Send Message):** The termination node. It extracts the raw text response from Claude's response path (`content[0].text`) and renders it cleanly back to the chat UI.

---
