# 🌐 Browser Automation Skills for AI Coding Agents

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

[English](./README.md) | [中文文档](./README_CN.md)

> Drop-in skills that teach Claude Code, Cursor, Codex CLI, and other AI coding agents how to automate browsers. One folder per tool — install only what you need.

Tell your agent *"open 50 browser profiles and check their fingerprints"* or *"scrape product prices from these 10 URLs"* and it just works.

## Why This Exists

AI coding agents are powerful, but they don't know how to use browser automation tools out of the box. These skills bridge that gap — each one teaches your agent the API patterns, best practices, and gotchas for a specific browser tool, so you can stay in natural language while the agent writes correct automation code.

## 🧰 Available Skills

| Skill | What it teaches your agent | Install |
|-------|---------------------------|---------|
| [**AdsPower**](./skills/adspower/) | Manage antidetect browser profiles, proxies, cookies, fingerprints via Local API | `cp -r skills/adspower .claude/skills/` |
| [**Playwright**](./skills/playwright/) | Fast browser automation with auto-wait, network interception, multi-browser support | `cp -r skills/playwright .claude/skills/` |
| [**Selenium**](./skills/selenium/) | Classic browser automation, WebDriver protocol, cross-browser testing | `cp -r skills/selenium .claude/skills/` |
| [**Puppeteer**](./skills/puppeteer/) | Chrome DevTools Protocol automation, headless Chrome, PDF generation | `cp -r skills/puppeteer .claude/skills/` |
| [**Web Scraping**](./skills/web-scraping/) | Extract data from websites using BeautifulSoup, lxml, CSS selectors, handling pagination and anti-bot | `cp -r skills/web-scraping .claude/skills/` |
| [**Browser Fingerprinting**](./skills/fingerprinting/) | Understand and manage browser fingerprints, canvas, WebGL, WebRTC, timezone, language | `cp -r skills/fingerprinting .claude/skills/` |

## ⚡ Quick Start

### Install a single skill

```bash
# Example: install the Playwright skill for Claude Code
mkdir -p .claude/skills
cp -r skills/playwright .claude/skills/
```

### Install all skills

```bash
mkdir -p .claude/skills
cp -r skills/* .claude/skills/
```

### Then just talk to your agent

```
> Scrape the top 20 product names and prices from amazon.com/bestsellers

> Open 10 AdsPower profiles, visit google.com on each, screenshot and close

> Write a Playwright script that logs into my dashboard and downloads the monthly report

> Check if my browser fingerprint is unique on browserscan.net
```

## 📂 Skill Structure

Each skill follows the same pattern:

```
skills/
├── adspower/
│   └── SKILL.md          # API reference, code patterns, common errors
├── playwright/
│   └── SKILL.md
├── selenium/
│   └── SKILL.md
├── puppeteer/
│   └── SKILL.md
├── web-scraping/
│   └── SKILL.md
└── fingerprinting/
    └── SKILL.md
```

A single `SKILL.md` per tool. No dependencies, no config files, no build steps. Your agent reads the file and knows what to do.

## 🔧 Compatibility

These skills work with any AI coding agent that supports the SKILL.md / CLAUDE.md standard:

| Agent | Supported |
|-------|-----------|
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | ✅ |
| [Cursor](https://cursor.sh/) | ✅ |
| [Codex CLI](https://github.com/openai/codex) | ✅ |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | ✅ |
| [Antigravity](https://antigravity.dev/) | ✅ |
| [Hermes Agent](https://github.com/NousResearch/hermes-agent) | ✅ |
| Any agent reading `.claude/skills/` | ✅ |

## 🤝 Contributing

Have a browser automation skill to share? PRs welcome!

1. Create a folder under `skills/` with your tool name
2. Add a `SKILL.md` following the existing pattern (API reference, code examples, common errors)
3. Update this README's skill table
4. Open a PR

## 📄 License

[MIT](LICENSE)

---

**⭐ Star this repo to find it again — new skills added regularly.**
