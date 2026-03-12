# 🤖 Building a Local AI Software Engineer: OpenClaw + Ollama Deployment Guide
This guide details the complete workflow for deploying a fully autonomous, local AI agent capable of executing system commands, reading/writing files, and assisting with codebase development and review, all running locally without internet dependency.

Phase 1: Engine & Brain Setup (Local LLM Infrastructure)
To power the agent locally, we use Ollama as the inference engine and Qwen 2.5 7B as the brain.

Install Ollama: Download and install the engine from ollama.com. It runs silently in the background (default port: 11434).

Pull the Model: Open your terminal (CMD/PowerShell) and execute:

Bash
ollama run qwen2.5:7b
Wait for the 4-5GB model weights to download. Once the >>> prompt appears, the model is active and listening.

Phase 2: Cybernetic Body (OpenClaw Core Deployment)
Next, we install the OpenClaw framework and bridge it to our local LLM.

Environment Setup: Install Node.js (LTS). Verify the installation by running node -v and npm -v in your terminal.

Install OpenClaw: Install the CLI globally via npm.

Bridge the Model (Web UI):

Start OpenClaw and open the Web UI.

Navigate to Settings -> Models.

Set Provider to ollama.

Set BaseUrl to http://127.0.0.1:11434/v1.

Add the model with ID and Name as qwen2.5:7b.

Set the Context Window to 8192 (or 16000). Save the configuration.

Define the Sandbox (Workspace):

Navigate to Agents -> Overview.

Set the Workspace absolute path (e.g., D:\PythonProject\chess-into-pgn). This physically restricts the AI's read/write access to this specific directory for security.

Phase 3: Breaking the Sandbox (Bypassing Gateway Restrictions)
By default, OpenClaw runs in a highly restricted mode. To grant the agent system-level execution capabilities, we must patch the core configuration.

Go to Settings -> Config and click Raw (bottom left).

Unlock Full Tools Profile: Locate the "tools" block and update it:

JSON
"tools": {
  "profile": "full"
}
Grant System Execution Privileges: Scroll to the bottom and locate the "gateway" -> "nodes" block. Update it to allow all commands:

JSON
"nodes": {
  "allowCommands": ["*"],
  "denyCommands": []
}
Hard Restart: Save the config in the UI. Terminate the existing OpenClaw terminal process (Ctrl+C) and restart it using the clean command:

Bash
openclaw gateway
Phase 4: Taming the AI (Prompt Engineering for Tool Calling)
Smaller models like Qwen 2.5 7B sometimes fail at native "Function Calling," outputting raw text instead of triggering background JSON APIs.

Enable Core Tools: In Agents -> Tools, ensure exec, write, read, and edit are toggled ON.

Strict System Prompting: When starting a new session, use a highly restrictive prompt to force tool usage.

Example Prompt: "System Directive: You are a low-level execution agent. Do not use natural language. You must use the exec tool to run calc.exe. Output strict JSON tool calls only. Do not explain."

Phase 5: Real-World Integration (Codebase Review & Execution)
When pointing OpenClaw to an existing complex repository (e.g., Computer Vision/Deep Learning pipelines), avoid context window overflow by guiding the AI step-by-step.

🛑 CRITICAL RULE: Never instruct the AI to read large binary files (e.g., .pth weights, .mp4 videos). It will crash the memory.

The Golden Code Review Workflow:

Map the Project: exec -> dir (or ls -la) to understand the directory structure.

Read the Blueprints: read -> README.md and requirements.txt to grasp the architecture and stack.

Deep Dive & Patch: Instruct the AI to read specific target scripts (e.g., main.py). Ask for optimization suggestions, then allow it to use the edit tool to patch bugs directly.

Run & Debug: Use exec -> python script.py. Feed any terminal tracebacks back to the AI for automated debugging.
