# Prompt Injection PoC: LLM Security Gateway

### Overview

This repository features a **minimalist Proof-of-Concept (PoC)** demonstrating fundamental detection mechanisms for **Prompt Injection vulnerabilities** in Large Language Models (LLMs). It implements a simple Python-based security gateway that inspects user prompts before forwarding them to a local LLM (Ollama). This PoC provides a hands-on understanding of **OWASP LLM01: Prompt Injection**, exploring both **direct** and the conceptual challenge of **indirect** injection.

---
### Purpose (Personal Learning Project)

**This project was created by me for my personal learning and skill development.** It serves as an exercise to deepen my understanding of LLM security, particularly Prompt Injection vulnerabilities and their basic detection. The implemented mechanisms are simplified and are **not intended for production environments**. My goal with this PoC is to demonstrate core concepts, experiment with initial security controls, and gain practical, hands-on experience in the evolving field of GenAI application security.

---
### Problem Statement

Prompt Injection attacks aim to manipulate an LLM's behavior by overriding its initial instructions, coaxing it into unintended actions, or extracting sensitive information. These attacks can be direct (malicious instructions in the user's explicit prompt) or indirect (malicious instructions hidden within data retrieved or processed by the LLM from external sources). This PoC showcases a basic layer of defense against direct injections and illustrates contexts susceptible to indirect injections.

---
### Key Security Features Implemented

* **Prompt Injection Detection (Direct):**
    * **Mechanism:** Uses a predefined set of keywords and regular expressions loaded from an external JSON file to identify common direct prompt injection patterns within user inputs.
    * **Functionality:** If a suspected direct injection is detected, the prompt is blocked, preventing it from reaching the LLM. This acts as a preliminary defense against explicit malicious instructions.
* **Prompt Injection Risk Detection (Indirect - Conceptual):**
    * **Mechanism:** Identifies specific phrases or contexts in the user's prompt that indicate the LLM might be processing external, untrusted content (e.g., "summarize the document:", "analyze the email:"). While not actively *detecting* an indirect injection itself (which is far more complex), it flags the **potential risk** in such scenarios.
    * **Functionality:** For this PoC, these contexts are primarily for illustrative purposes in the detection output; they do not trigger a block by default, highlighting that deeper analysis is required for true indirect injection mitigation.
* **External Pattern Configuration:**
    * **Mechanism:** All detection patterns (keywords, regex, indirect context indicators) are loaded from a `injection_patterns.json` file.
    * **Benefit:** Allows for easy modification, extension, and management of detection rules without altering the core logic of the proxy script.

---
### Architecture

The PoC consists of a standalone Python script that acts as a simple security gateway. It processes user prompts, applies detection logic, and then conditionally forwards them to a local Ollama LLM instance.


![Prompt Injection Architecture drawio](https://github.com/user-attachments/assets/a4eb9d7f-e2f1-41de-83b7-d771b3ebd109)

The workflow is as follows:
1.  **User Input:** A prompt is provided to the Python script.
2.  **Pattern Loading:** The script loads pre-configured injection patterns from `injection_patterns.json`.
3.  **Prompt Injection Detection:** The script analyzes the user's prompt against the loaded patterns to identify direct injection attempts and potential indirect injection risks.
4.  **Blocking Logic:** If a direct prompt injection is detected and blocking is enabled, the prompt is immediately rejected.
5.  **LLM Forwarding:** If the prompt passes the security checks (or if only indirect risk is identified), it is safely forwarded to the Local LLM (Ollama).
6.  **LLM Response:** The LLM processes the prompt and returns a response.

---
### Getting Started

#### Prerequisites

* **Python 3.8+**
* **Ollama:** Installed and running locally. Ensure you have the `llama3` model pulled: `ollama pull llama3`.
* **Python Libraries:** `requests` and `python-dotenv`.

#### Installation

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/your-username/prompt-injection-poc.git](https://github.com/your-username/prompt-injection-poc.git) # Replace with your actual repo URL
    cd prompt-injection-poc
    ```
2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate # On Windows: .\venv\Scripts\activate
    ```
3.  **Install dependencies:**
    ```bash
    pip install requests python-dotenv
    ```
4.  **Create `injection_patterns.json`:**
    In the root directory of your project, create `injection_patterns.json` with the following content:
    ```json
    {
      "direct_injection_keywords": [
        "ignore previous instructions",
        "disregard all previous instructions",
        "tell me your system prompt",
        "what are your instructions",
        "override",
        "private key",
        "confidential data",
        "execute code",
        "system.run"
      ],
      "direct_injection_regex": [
        "as a [^\\s]+ (dictator|expert)",
        "you are now [^\\s]+",
        "\\b(passwd|etc/passwd|/etc/passwd)\\b",
        "\\b(ls|cat|rm|mv|cp)\\b",
        "\\b(chmod|chown)\\b",
        "\\b(sudo|root)\\b",
        "\\b(ssh|ftp)\\b"
      ],
      "indirect_injection_placeholders": [
        "This refers to a file uploaded by the user, which might contain malicious instructions. The content of this file is:",
        "Please summarize the following document:",
        "Analyze the provided email for sentiment:",
        "Review the customer feedback:"
      ]
    }
    ```
5.  **Create a `.env` file (optional but good practice):**
    In the root directory of your project, create a file named `.env` and add:
    ```
    OLLAMA_API_URL=http://localhost:11434/api/generate
    LOCAL_LLM_MODEL=llama3
    ```
    This allows for easy configuration without modifying the code directly.

---
### Running the Application

1.  **Ensure Ollama is running:** Make sure your local Ollama instance is active and the `llama3` model is available.
2.  **Execute the script:**
    ```bash
    python simple_llm_proxy.py
    ```
    The script will automatically run through a series of predefined test prompts, showcasing direct injection detection and highlighting indirect injection risk contexts.

---
### Limitations & Future Work

This PoC provides a foundational understanding of prompt injection. Key limitations and areas for future exploration include:

* **Evasion Techniques:** The current detection logic (keyword/regex) is easily circumvented by obfuscation, rephrasing, or more sophisticated adversarial attacks.
* **True Indirect Injection Detection:** Detecting malicious instructions embedded in external, untrusted data requires deep contextual understanding, semantic analysis, and potentially LLM-based reasoning, which is beyond the scope of this basic PoC.
* **LLM-based Defense:** Utilizing the LLM itself to identify and mitigate injections (e.g., through guardrail models or input/output sanitization prompts).
* **Integration:** Incorporating this logic into a larger DevSecOps pipeline or a comprehensive AI-ASPM platform.
* **Output Filtering:** Implementing checks for malicious or unintended content in the LLM's response, beyond the scope of input prompt analysis.

---
### Contribution

Feel free to fork this repository, explore the code, and experiment with different prompt injection techniques.

---
### License

[MIT License](LICENSE)
