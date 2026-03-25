# 🏛️ FactGrid to Wikibase Data Migration Pipeline

> **Automated ETL pipeline for migrating museum data from FactGrid to a local Wikibase.**

---

## 🎓 Project Context
Developed by **Aaron (Rong-Guei Shen)**, a Data Science student at **UC Berkeley**. This project demonstrates proficiency in managing complex ETL workflows, resolving API metadata conflicts, and bridging cloud-to-local infrastructure.

---

## 📊 Dataset
The migration is based on the following structured museum data:
* **Source Data**: [📁 LOD Tablet Dictionary (FG Cuneiform) - museum_FG](https://docs.google.com/spreadsheets/d/1UvH8UWmQtic7mKF_mKT4p-0bMUfywZA1ZWziD1vSEEk/edit#gid=227100401)
* **Content**: Contains 109 museum entries with metadata including FactGrid QIDs, external IDs (P771), and official websites.

---

## 🛠️ Technical Challenges & Direct Fixes

### 🛡️ Critical Bug: Metadata Sanitization
**The Problem:**
When fetching entities from **FactGrid** using `wbi.item.get()`, the data includes **Sitelinks** (links to external Wikipedia pages like `enwiki`). Since local Wikibase instances do not recognize these sites by default, the script crashes with `MWApiError: 'Unknown site: enwiki'` during the `.write()` operation.

**The Fix:**
You must manually clear the `sitelinks` object before writing to the local instance. This strips incompatible metadata while preserving Labels, Descriptions, and Claims.

```python
# 1. Fetch original entity from FactGrid
entity = wbi.item.get(qid, mediawiki_api_url='[https://database.factgrid.de/w/api.php](https://database.factgrid.de/w/api.php)')

# 🌟 THE CORE FIX: Wipe incompatible sitelinks to ensure successful local write
entity.sitelinks = Sitelinks() 

# 2. Execute write to local Wikibase (Instruction below)
entity.write(mediawiki_api_url=f'{my_ngrok_url}/w/api.php', login=login_testwikidata)


🌐 Instruction: Connecting Colab to Localhost (ngrok)
Google Colab runs in the cloud and cannot "see" your computer's localhost. You MUST use ngrok to bridge the connection.

1. Launch the Tunnel
In your local terminal, run:
bash
ngrok http 8181
2. Copy the URL (Most Common Failure Point)
Look for the Forwarding line in your terminal output.

Copy ONLY the address text: (e.g., https://a1b2-c3d4.ngrok-free.dev)

⚠️ DO NOT click the link.

⚠️ DO NOT copy the -> localhost:8181 part or any extra spaces.

3. Update Colab Variable
Paste the clean URL into the my_ngrok_url variable in your notebook:

Python
# ✅ Correct: Just the string inside quotes, no extra characters
my_ngrok_url = '[https://a1b2-c3d4-e5f6.ngrok-free.dev](https://a1b2-c3d4-e5f6.ngrok-free.dev)'
🔄 Mandatory Session Refresh
Important: Every time you restart your terminal or the ngrok command, the URL changes.

You MUST copy the new URL and update the variable in Colab every time you start a new session.

Failure to update this will result in a "Connection Error."

🚀 Execution Workflow
Property Mapping: Aligning FactGrid P-numbers with local Wikibase property IDs.

Entity Mapping: Building a lookup dictionary for dependencies like Cities and Organizations.

Data Import: Automated migration of 109 museum entries with full claim sets.

🧰 Tech Stack
Language: Python 3.x

Libraries: WikibaseIntegrator, Pandas, Requests

Tools: ngrok, Docker, Google Colab
