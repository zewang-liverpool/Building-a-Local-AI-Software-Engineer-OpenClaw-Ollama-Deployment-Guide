<div align="center">
  <h1>🤖 Building a Local AI Software Engineer</h1>
  <p><i>A complete zero-to-hero guide on deploying OpenClaw + Ollama for local, internet-free codebase automation.</i></p>
</div>

---

Welcome to my workspace! Below is my hardcore deployment guide for setting up a fully autonomous AI agent. It can execute system commands, read/write files, and perform codebase reviews—all running 100% locally on your own GPU.

## ⚙️ Phase 1: Engine & Brain Setup (Local LLM Infrastructure)

To power the agent locally, we use **Ollama** as the inference engine and **Qwen 2.5 7B** (or Llama 3.1) as the brain.

1. **Install the Engine:** Download and install [Ollama](https://ollama.com/). It runs silently in the background on port `11434`.
2. **Pull the Model Weights:** Open your terminal (CMD/PowerShell) and fetch the AI model:

```bash
ollama run qwen2.5:7b
```

> 💡 **Pro Tip:** Wait for the 4-5GB download to finish. Once the `>>>` prompt appears, the model is active and listening.

---

## 🦾 Phase 2: Cybernetic Body (OpenClaw Core Deployment)

Next, we install the OpenClaw framework and bridge it to our local LLM.

1. **Environment Setup:** Install **Node.js (LTS)**. Verify the installation in your terminal:
```bash
node -v
npm -v
```
2. **Bridge the Model (Web UI):**
   * Launch OpenClaw and navigate to **Settings -> Models**.
   * Set Provider to `ollama`.
   * Set **BaseUrl** to `http://127.0.0.1:11434/v1`.
   * Configure the model with **ID** and **Name** as `qwen2.5:7b`.
   * Set the **Context Window** to `8192` (or higher depending on your VRAM). 
3. **Define the Sandbox (Workspace):**
   * Navigate to **Agents -> Overview**.
   * Set the **Workspace** to an absolute path (e.g., `D:\PythonProject\chess-into-pgn`). 
   * *This restricts the AI's read/write access to this specific directory for security.*

---

## 🔓 Phase 3: Breaking the Sandbox (Bypassing Restrictions)

By default, OpenClaw runs in a highly restricted mode. To grant the agent system-level execution capabilities, we must patch the core configuration JSON.

1. Go to **Settings -> Config** and click **Raw** (bottom left).
2. **Unlock Full Tools Profile:** Locate the `"tools"` block and update it:
```json
"tools": {
  "profile": "full"
}
```
3. **Grant System Execution Privileges:** Scroll to the bottom and locate the `"gateway"` -> `"nodes"` block. Update it to allow all system commands:
```json
"nodes": {
  "allowCommands": ["*"],
  "denyCommands": []
}
```
4. **Hard Restart:** Save the config in the UI. Terminate the existing OpenClaw terminal process (`Ctrl+C`) and restart it using the clean command:
```bash
openclaw gateway
```

---

## 🧠 Phase 4: Taming the AI (Prompt Engineering)

Smaller models like Qwen 2.5 7B sometimes fail at native "Function Calling" and output raw text instead of triggering background JSON APIs. 

* **Enable Core Tools:** In **Agents -> Tools**, ensure the following are toggled **ON**:
  * `exec` (Execute terminal commands)
  * `write` (Create/overwrite files)
  * `read` (Read file contents)
  * `edit` (Make precise edits)

* **Strict System Prompting:** When starting a new session, use a highly restrictive prompt to force tool usage:
> **System Directive:** You are a low-level execution agent. Do not use natural language. You must use the `exec` tool to run commands. Output strict JSON tool calls only. Do not explain.

---

## 💼 Phase 5: Real-World Integration (Codebase Review Workflow)

When pointing OpenClaw to an existing complex repository (e.g., Computer Vision/Deep Learning pipelines), avoid context window overflow by guiding the AI step-by-step.

> 🛑 **CRITICAL WARNING:** Never instruct the AI to `read` large binary files (e.g., `.pth` weights, `.mp4` videos). It will cause severe memory crashes.

**The Golden Code Review Workflow:**
1. **Map the Project:** `exec` -> `dir` (or `ls -la`) to understand the directory structure.
2. **Read the Blueprints:** `read` -> `README.md` and `requirements.txt` to grasp the architecture and stack.
3. **Deep Dive & Patch:** Instruct the AI to `read` specific target scripts (e.g., `main.py`). Ask for optimization suggestions, then allow it to use the `edit` tool to patch bugs directly.
4. **Run & Debug:** Use `exec` -> `python script.py`. Feed any terminal tracebacks back to the AI for automated debugging.
