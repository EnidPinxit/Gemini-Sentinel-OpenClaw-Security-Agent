# Gemini Sentinel: Agentic Security Boundary Prototype

Gemini Sentinel is a local-first AI security analysis framework designed to protect the boundary between raw source code and Large Language Model (LLM) analysis. 

Rather than acting as a generic linter, Sentinel implements a **trust-boundary architecture** that filters, redacts, and structures security audits to ensure that sensitive data never leaves the local environment and that the resulting findings are grounded in real-world threat models.

## License Notice

This project is currently source-available for review, research discussion, and
partnership evaluation. It is not licensed for commercial use, redistribution,
deployment, or derivative works without written permission. See `LICENSE.md`.

## Representation Notice

As of 2026-05-12, Enid Pinxit represents only herself and her own work unless a
specific written agreement states otherwise. See
`REPRESENTATION-NOTICE-2026-05-12.md`.

## 🛡️ Architectural Flow

The system is engineered as a multi-layer security gate:

**Developer** $\rightarrow$ **Intake Filters** $\rightarrow$ **Secret Redactor** $\rightarrow$ **Local Trust Proxy** $\rightarrow$ **Gemini Pro**

1. **Client Intake Filters**: Enforces strict limits on file counts and sizes, ignoring binaries and common noise directories (e.g., `node_modules`, `.git`) to prevent prompt injection or model overflow.
2. **Secret Redactor**: A deterministic regex-based layer that identifies and labels API keys, tokens, and private keys *before* they reach the transport layer.
3. **Local Trust Proxy**: A Node.js server that acts as the sole custodian of the Gemini API key. The client never sees the key; the proxy enforces security headers (CSP, HSTS) and validates payload integrity.
4. **Threat-Model-Driven Prompting**: Instead of "finding bugs," the system instructs the model to identify vulnerabilities through the lens of **Preconditions $\rightarrow$ Impact $\rightarrow$ Evidence**, reducing false positives and severity inflation.

## 🚀 Key Engineering Signals

- **Sovereign Key Management**: API keys are stored server-side in the local proxy, not in the browser or client state.
- **Structured Findings**: Results are returned as a strict JSON schema (Risk Score, Category, Severity, Patch), allowing for programmatic auditing and report generation.
- **Human-in-the-Loop Patching**: The tool generates "IDE Prompts"—precise instructions for a human to apply the fix—rather than performing autonomous (and potentially risky) file modifications.
- **Self-Hardening**: The repository includes scripts for restrictive filesystem permissions (`chmod 600/700`) to protect the security tool itself.

## 📁 Project Structure

- `/components/AgentDashboard.tsx`: The primary boundary interface for staging and privacy controls.
- `/services/secretSanitizer.ts`: The regex engine for identity and secret redaction.
- `/server/index.js`: The Local Trust Proxy and API gateway.
- `/docs/DEV-NOTES-2026-02-03.md`: Research notes on agentic boundaries and cognitive sovereignty.

## 🛠️ Setup & Execution

1. **Environment**: Clone the repo and install dependencies (`npm install`).
2. **Configuration**: Add your `GEMINI_API_KEY` to the `.env` file in the server directory.
3. **Hardening**: Run `./scripts/setup-permissions.sh` to secure the local environment.
4. **Launch**: Start the server (`node server/index.js`) and the frontend (`npm run dev`).

## 🔭 Future Research Direction

While the current implementation focuses on local code auditing, the underlying architecture is a prototype for **Agentic Boundary Security**. The long-term goal is to move from "code security" to "context security"—detecting prompt injection, drift, and contamination within the active memory of long-running agent systems.
