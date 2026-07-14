<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License: MIT][license-shield]][license-url]

<!-- PROJECT LOGO -->
<br />
<div align="center">

  <h3 align="center">SWE Internship Resume Reviewer</h3>

  <p align="center">
    A single-file web tool that scores SWE internship resumes using a local LLM (Ollama) or cloud APIs (Anthropic, OpenAI).
    <br />
    <a href="#installation"><strong>Installation »</strong></a>
    &middot;
    <a href="#usage">Usage</a>
    &middot;
    <a href="#configuration">Configuration</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>
    <li><a href="#installation">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#configuration">Configuration — Backend Provider</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

This tool evaluates SWE / Engineering internship resumes using a rigorous, recruiter-style scoring rubric. It sends the resume text (parsed from PDF/DOCX/TXT) plus an optional job description to your chosen LLM backend and returns a scored breakdown:

- **Overall score** (out of 100)
- **8 sub-category bars**: Impact & Quantification, Technical Depth, Relevant Experience, Clarity, Formatting / ATS, Skills Accuracy, Education, Polish / Red Flags
- **Interview likelihood** — two percentages with and without a referral
- **JD-specific gap analysis** (optional)
- **Top 3–5 prioritized fixes**

No build step, no framework, no dependencies to install. It is a single `index.html` file served from any static server.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

You need one of the following backend providers configured:

**Option A — Ollama (free, local)**
- [Install Ollama](https://ollama.com)
- Pull a chat model: `ollama pull qwen3:8b` (or any OpenAI-compatible chat model)

**Option B — Anthropic API**
- An [Anthropic API key](https://console.anthropic.com/settings/keys). Supports Claude Sonnet / Opus models.

**Option C — OpenAI API**
- An [OpenAI API key](https://platform.openai.com/api-keys). Uses the Chat Completions API with `gpt-4o-mini` or equivalent.

### Installation

1. Clone or download this repository:
   ```sh
   git clone https://github.com/ThinkerDesigns/reviewer.git
   cd reviewer
   ```

2. Start a static server from the project directory:
   ```sh
   # Python 3
   python3 -m http.server 8080

   # Node.js (if you prefer)
   npx serve . -p 8080
   ```

3. Open `http://localhost:8080` in your browser.

4. Configure your backend provider — see the [Configuration](#configuration) section below.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE -->
## Usage

1. **Paste or upload** a resume (PDF, DOCX, or TXT). PDFs and DOCX files are parsed client-side — your file never leaves your browser before the API call.
2. **Optional**: Paste a job description to get targeted keyword-gap feedback.
3. **Toggle referral** if the candidate has an employee referral.
4. Click **Review Resume**.

The results render as: overall score, animated sub-category bars, interview likelihood, JD gap list, and prioritized fixes.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONFIGURATION -->
## Configuration — Backend Provider

Open `index.html` in a text editor. Find the provider section (around line 215) and set your preferred backend:

### Option A: Ollama (default)

```js
const PROVIDER = 'ollama';
const OLLAMA_URL = 'http://localhost:11434';    // your ollama endpoint
const OLLAMA_MODEL = 'qwen3:8b';                 // model pulled via `ollama pull qwen3:8b`
```

### Option B: Anthropic API

```js
const PROVIDER = 'anthropic';
const ANTHROPIC_API_KEY = 'sk-ant-api03-...';    // your key from console.anthropic.com
const ANTHROPIC_MODEL = 'claude-sonnet-4-20250514';
```

### Option C: OpenAI (ChatGPT) API

```js
const PROVIDER = 'openai';
const OPENAI_API_KEY = 'sk-proj-...';             // your key from platform.openai.com
const OPENAI_MODEL = 'gpt-4o-mini';
```

Then update the `sendToBackend` function (same section) to route requests:

```js
// Inside sendToBackend(resumeText, referral, jd):

if (PROVIDER === 'ollama') {
  url = `${OLLAMA_URL}/api/chat`;
  body = { model: OLLAMA_MODEL, messages: [{ role:'system', content: prompt }, { role:'user', content: 'Review this resume and output the JSON.' }], stream: false };
} else if (PROVIDER === 'anthropic') {
  url = 'https://api.anthropic.com/v1/messages';
  headers['x-api-key'] = ANTHROPIC_API_KEY;
  headers['anthropic-version'] = '2023-06-01';
  body = { model: ANTHROPIC_MODEL, messages: [{ role:'user', content: prompt }], max_tokens: 4096 };
} else if (PROVIDER === 'openai') {
  url = 'https://api.openai.com/v1/chat/completions';
  headers['Authorization'] = `Bearer ${OPENAI_API_KEY}`;
  body = { model: OPENAI_MODEL, messages: [{ role:'system', content: prompt }, { role:'user', content: 'Review this resume and output the JSON.' }] };
}
```

> **Note:** When using Anthropic or OpenAI, you may need to update CORS headers in your browser or run a small proxy if you hit cross-origin restrictions from `file://`. The easiest path is to serve over `http://localhost:8080` as described above.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

- [ ] Provider toggle UI (dropdown in the page itself, no code edit needed)
- [ ] API key input field on the page (no file edits)
- [ ] Export results as PDF
- [ ] Save/review history (in-browser localStorage)
- [ ] Dark mode

See the [open issues](https://github.com/ThinkerDesigns/reviewer/issues) for a full list of proposed features.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Dhruv Bhadauriya — bhadauriya@ucdavis.edu

Project Link: [https://github.com/ThinkerDesigns/reviewer](https://github.com/ThinkerDesigns/reviewer)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

- [Ollama](https://ollama.com) — local model serving
- [mammoth.js](https://github.com/mammoth-browser/mammoth.js) — DOCX parsing
- [pdf.js](https://github.com/pdfjs-dist/pdf.js) — PDF text extraction
- [Best README Template](https://github.com/othneildrew/Best-README-Template)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
[contributors-shield]: https://img.shields.io/github/contributors/ThinkerDesigns/reviewer.svg?style=for-the-badge
[contributors-url]: https://github.com/ThinkerDesigns/reviewer/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/ThinkerDesigns/reviewer.svg?style=for-the-badge
[forks-url]: https://github.com/ThinkerDesigns/reviewer/network/members
[stars-shield]: https://img.shields.io/github/stars/ThinkerDesigns/reviewer.svg?style=for-the-badge
[stars-url]: https://github.com/ThinkerDesigns/reviewer/stargazers
[issues-shield]: https://img.shields.io/github/issues/ThinkerDesigns/reviewer.svg?style=for-the-badge
[issues-url]: https://github.com/ThinkerDesigns/reviewer/issues
[license-shield]: https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge
[license-url]: https://opensource.org/licenses/MIT
