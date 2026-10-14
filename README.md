# TraceLens — NEWTOWN Insurance Assistant

> A conversational developer and support engineer assistant for NEWTOWN Insurance Company. TraceLens triages production incidents against known resolutions, provides technical architecture workflows, monitors website service status, executes code sandboxes, and renders interactive UI components.

📋 **[🌐 Open Live Interactive Technical Specification Webpage](https://tracelens-frontend-1086733222406.us-east1.run.app/spec.html)** • **[⚡ Alternative Web Render Mirror](https://htmlpreview.github.io/?https://github.com/jayanthe21/tracelens/blob/main/demo/spec.html)** • **[📁 View Raw GitHub File](demo/spec.html)**

![TraceLens Demo](documents/demo.gif)

---

## Key Capabilities & Implemented Features

TraceLens is built on the **Google Agent Development Kit (ADK)** using `gemini-2.5-flash` and the **A2A (Agent-to-Agent)** protocol. Below are the specific features and tools wired up in `app/agent.py`:

### 🧠 Long-Term Memory (Vertex AI Memory Bank)
* **`remember_user_fact` & `remember_user_allergy`**: Stores user statements, policy rules, preferences, and dietary/medical sensitivities into long-term Vertex AI Memory Bank.
* **`search_user_memory_bank`**: Searches past session history and stored memories to retrieve context across conversations.

### 🛠️ Incident Triage & Knowledge Base (Google Cloud Firestore)
* **`lookup_known_issue`**: Queries the Firestore `known_issues` collection for existing production bug reports, root causes, and resolutions.
* **`store_resolved_issue`**: Persists newly diagnosed and fixed incidents directly into Firestore for future triage reuse.

### 🎨 Enterprise Asset Generation (Vertex AI & Cloud Storage)
* **`generate_domain_image`**: Uses `gemini-3.1-flash-lite-image` to create custom policy graphics, topology icons, and architecture diagrams, saving them to Google Cloud Storage (`tracelens-assets-*`).
* **`generate_domain_video`**: Uses `gemini-omni-flash-preview` to generate domain video clips and animations, serving public Cloud Storage URLs.

### 🧪 Secure Code Execution (Agent Engine Sandbox)
* **`execute_python_code`**: Runs Python code snippets inside an isolated Agent Engine Sandbox execution environment (`AgentEngineSandboxCodeExecutor`).

### 🌐 System Health & Diagnostics
* **`check_website_status`**: Performs live HTTP health checks and measures latency for target domains and API endpoints.
* **`lookup_ip_network_info`**: Retrieves ISP provider, organization, AS network details, and geolocation info for target IP addresses or domain names.
* **`query_architecture_docs`**: Retrieves architecture specifications and diagrams for NEWTOWN Insurance services (Web UI, REST APIs, MDB SMS Flow).
* **`analyze_log_trace`**: Parses raw log lines and stack traces to extract exception types, HTTP status codes, and failing source code line numbers.

### 🎛️ Dynamic UI (A2UI Catalog)
* Uses `a2ui_callback` and `A2uiSchemaManager` to render rich interactive cards, option chips, and structured UI components directly in the chat interface.

---

## Feature Implementation Status

| Feature / Service | Status | Description / Wiring |
| :--- | :--- | :--- |
| **Vertex AI Memory Bank** | ✅ Implemented | Wired via `VertexAiMemoryBankService` & ADK memory tools |
| **Google Cloud Firestore** | ✅ Implemented | Firestore client bound to `known_issues` collection |
| **Google Cloud Storage** | ✅ Implemented | Asset bucket uploads via `google-cloud-storage` |
| **Vertex AI Image Gen** | ✅ Implemented | `gemini-3.1-flash-lite-image` model |
| **Vertex AI Video Gen** | ✅ Implemented | `gemini-omni-flash-preview` model |
| **Agent Engine Sandbox** | ✅ Implemented | `AgentEngineSandboxCodeExecutor` integration |
| **A2UI Dynamic Catalog** | ✅ Implemented | `a2ui` package with custom FastAPI proxy |
| **Cloud Trace Observability** | ⏳ Planned | *Planned for future telemetry integration* |

---

## Project Structure

```
tracelens/
├── app/                        # Main ADK Agent Package
│   ├── agent.py                # Core agent, tools, system prompt & A2UI callback
│   ├── a2ui_utils.py           # A2UI schema manager & formatting utilities
│   ├── fast_api_app.py         # FastAPI application entrypoint
│   └── app_utils/              # Services, A2A adapters & telemetry modules
├── frontend/                   # Web Chat Frontend & Proxy
│   ├── main.py                 # FastAPI proxy forwarding A2A requests to Agent Runtime
│   ├── requirements.txt        # Frontend dependencies
│   └── static/                 # Single-page web UI (HTML, CSS, JS)
├── documents/                  # Architecture diagrams, exports & demo media
│   ├── demo.gif                # Looping agent demo recording
│   ├── firestore_export.json   # Known issues dataset export
│   └── architecture_*.png      # NEWTOWN system architecture diagrams
├── agents-cli-manifest.yaml    # Deployment manifest for agents-cli
├── Dockerfile                  # Container build instructions
└── pyproject.toml              # Python project configuration & dependencies
```

---

## Local Setup & Running Instructions

### Prerequisites
* Python 3.11+
* Google Cloud SDK (`gcloud`) authenticated with Application Default Credentials (`gcloud auth application-default login`)

### 1. Installation
Clone the repository and install dependencies:
```bash
git clone https://github.com/jayanthe21/tracelens.git
cd tracelens
pip install -r frontend/requirements.txt
```

### 2. Environment Configuration
Set required environment variables for your Google Cloud project and Agent Engine instance:
```bash
export GOOGLE_CLOUD_PROJECT="<your-gcp-project-id>"
export GOOGLE_CLOUD_LOCATION="us-east1"
export AGENT_ENGINE_RESOURCE_NAME="projects/<project-id>/locations/us-east1/reasoningEngines/<engine-id>"
export AGENT_DIRECTORY="app"
```

### 3. Run Local Proxy & Frontend
Start the local FastAPI proxy server:
```bash
python frontend/main.py
```
*The proxy will start on port `8080` (or the `$PORT` environment variable).*
