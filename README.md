<div align="center">

# 🛡️ PatchPilot AI

### Find it. Fix it. Prove it.

**An open-source, local-first Python security scanner that finds dangerous code with AST analysis, patches it with a local open-weight LLM, and verifies the fix, all without your code ever leaving your machine.**

![Python](https://img.shields.io/badge/python-3.9%2B-blue?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![Ollama](https://img.shields.io/badge/LLM-Ollama-black)
![Model](https://img.shields.io/badge/model-Qwen2.5%201.5B-00ff9c)
![License](https://img.shields.io/badge/license-MIT-green)
![Hacktoberfest](https://img.shields.io/badge/Hacktoberfest-2026-ff2bd6)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)

</div>

---

## 📸 Screenshot

<!-- Replace with a real screenshot of the running app: save it as docs/screenshot.png -->
![PatchPilot AI dashboard](docs/screenshot.png)

---

## What is PatchPilot AI?

AI coding assistants generate code fast, and sometimes insecure code with it, such as calls to `eval()` on user input. Checking that code usually means pasting it into a cloud tool, which is a problem when the code is proprietary.

**PatchPilot AI keeps everything on your machine.** You paste Python code into a cyberpunk-styled Streamlit dashboard. Python's built-in `ast` module finds the dangerous calls. A small open-weight model (**Qwen2.5 1.5B**, running through **Ollama**) explains why each one is unsafe and proposes a safer line. PatchPilot then checks the result before showing it to you.

No OpenAI key. No paid API. No telemetry. No code upload.

## ✨ Features

- 🔍 **AST-based detection.** It parses real syntax trees, so `eval(x)` is flagged but `"eval(x)"` inside a string or `ast.literal_eval(x)` is not.
- 🤖 **Local AI explanations and patches.** Qwen2.5 1.5B runs via Ollama on your own hardware.
- ✅ **Verification step.** Every patch is re-parsed and re-scanned. It must still be valid Python and must contain no remaining `eval()` calls.
- 🛟 **Safe fallback.** If the model is offline or produces a bad patch, a deterministic rewrite to `ast.literal_eval` is used and verified the same way.
- 🆚 **Side-by-side view.** The vulnerable line and the patch are shown together, with the explanation and the full patched file.
- 🔒 **Privacy by design.** All processing happens on `localhost`.
- 🎛️ **Configurable.** Change the Ollama model from the sidebar, or turn the LLM off entirely.
- 🧪 **Tested core.** Unit tests cover scanning, response parsing, patching and verification.

## ⚙️ How it works

```
 Pasted Python code
        │
        ▼
 ┌──────────────┐   ast.parse + NodeVisitor      ┌────────────────────┐
 │  1. SCAN     │ ─────────────────────────────▶ │ eval() calls found │
 └──────────────┘                                 └─────────┬──────────┘
                                                            ▼
 ┌──────────────┐   vulnerable line → Ollama      ┌────────────────────┐
 │ 2. EXPLAIN   │ ─────────────────────────────▶ │ explanation + patch│
 │    & PATCH   │      (Qwen2.5 1.5B, local)      └─────────┬──────────┘
 └──────────────┘                                           ▼
 ┌──────────────┐   re-parse + re-scan            ┌────────────────────┐
 │ 3. VERIFY    │ ─────────────────────────────▶ │ ✅ proven / ❌ fall │
 └──────────────┘                                 │ back to safe rewrite│
                                                  └────────────────────┘
```

## 🚀 Getting Started

### Prerequisites

- Python 3.9+
- [Ollama](https://ollama.com/download) installed

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/patchpilot-ai.git
cd patchpilot-ai
```

### 2. Create a virtual environment

```bash
python -m venv venv

# macOS / Linux
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install streamlit requests
```

### 4. Start the local model

In a **separate terminal**:

```bash
ollama run qwen2.5:1.5b
```

The first run downloads the model (about 1 GB). Keep Ollama running.

### 5. Launch the dashboard

```bash
streamlit run app.py
```

Open the URL Streamlit prints (usually http://localhost:8501), paste some Python and click **Scan & Patch**.

> 💡 No Ollama? Switch off **Use local AI model** in the sidebar. The deterministic fix still works.

### Try it

```python
def calculate(user_input):
    result = eval(user_input)
    return result
```

Expected output: the `eval(...)` line is flagged, `ast.literal_eval(user_input)` is proposed, and the patch is marked **Verified**.

## 🧪 Run the tests

```bash
pip install pytest
pytest tests
```

## 🗂️ Project structure

```
patchpilot-ai/
├── app.py            # Streamlit cyberpunk dashboard
├── core.py           # AST scanner, Ollama client, patcher, verifier
├── tests/
│   └── test_core.py  # Unit tests
├── docs/             # Screenshots
└── requirements.txt
```

## 🗺️ Roadmap

- [ ] More detection rules (`exec`, `pickle.loads`, `subprocess(shell=True)`, hardcoded secrets)
- [ ] Run the project's own test suite against patched code
- [ ] Upload whole files or folders, not just pasted snippets
- [ ] Export a security report (Markdown / JSON)
- [ ] CLI mode and a pre-commit hook
- [ ] Support for larger models (Qwen2.5 7B, Llama 3, Mistral)

## 🤝 Contributing

Contributions are welcome, especially during Hacktoberfest!

1. Fork the repo and create a branch: `git checkout -b feature/new-rule`
2. Make your changes and add a test in `tests/`
3. Commit with a clear message and open a Pull Request

Good first issues: adding a new detection rule, improving the prompt, or improving the UI.

## ⚠️ Limitations

PatchPilot AI is a learning and assistive tool, not a replacement for a full security audit. Currently it detects direct `eval()` calls only. A small 1.5B model can make mistakes, which is why every patch is verified and a deterministic fallback exists. Always review patches before using them. Note that `ast.literal_eval` only accepts Python literals, so code that relied on `eval` to run expressions will change behavior.

## 📄 License

Released under the [MIT License](LICENSE).

## 🙏 Acknowledgements

Built for the **MLH Hacktoberfest 2026 DigitalOcean "Best Open-Source AI Project"** prize challenge. Powered by [Ollama](https://ollama.com), [Qwen2.5](https://huggingface.co/Qwen) and [Streamlit](https://streamlit.io).
# Label-Lens
