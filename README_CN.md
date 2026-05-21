# 🌐 浏览器自动化 Skills — 让 AI 编程助手掌握浏览器操控

[English](./README.md) | **中文**

> 即插即用的技能包，让 Claude Code、Cursor、Codex CLI 等 AI 编程助手学会操控浏览器。一个文件夹对应一个工具，按需安装。

跟你的 AI 说一句 *"打开 50 个浏览器配置文件，检查指纹"* 或者 *"从这 10 个网址抓取商品价格"*，它就能直接干活。

## 为什么要做这个

AI 编程助手很强大，但它们开箱不会用浏览器自动化工具。这些 skills 补上了这个缺口，每个 skill 教会你的 AI 一个浏览器工具的 API 用法、最佳实践和常见坑，你只管说自然语言就行。

## 🧰 可用的 Skills

| Skill | 教你的 AI 做什么 | 安装命令 |
|-------|------------------|----------|
| [**AdsPower**](./skills/adspower/) | 管理指纹浏览器配置文件、代理、Cookie、指纹 | `cp -r skills/adspower .claude/skills/` |
| [**Playwright**](./skills/playwright/) | 快速浏览器自动化，自动等待，网络拦截，多浏览器支持 | `cp -r skills/playwright .claude/skills/` |
| [**Selenium**](./skills/selenium/) | 经典浏览器自动化，WebDriver 协议，跨浏览器测试 | `cp -r skills/selenium .claude/skills/` |
| [**Puppeteer**](./skills/puppeteer/) | Chrome DevTools Protocol 自动化，无头浏览器，PDF 生成 | `cp -r skills/puppeteer .claude/skills/` |
| [**Web Scraping**](./skills/web-scraping/) | 用 BeautifulSoup、lxml、CSS 选择器抓取数据，处理分页和反爬 | `cp -r skills/web-scraping .claude/skills/` |
| [**浏览器指纹**](./skills/fingerprinting/) | 理解和管理浏览器指纹：Canvas、WebGL、WebRTC、时区、语言 | `cp -r skills/fingerprinting .claude/skills/` |

## ⚡ 快速开始

### 安装单个 skill

```bash
# 举例：给 Claude Code 安装 Playwright skill
mkdir -p .claude/skills
cp -r skills/playwright .claude/skills/
```

### 安装全部 skills

```bash
mkdir -p .claude/skills
cp -r skills/* .claude/skills/
```

### 然后直接跟 AI 说话

```
> 抓取 amazon.com/bestsellers 上前 20 个商品名称和价格

> 打开 10 个 AdsPower 配置文件，每个访问 google.com，截图后关闭

> 写一个 Playwright 脚本，登录我的后台并下载月报

> 在 browserscan.net 上检查我的浏览器指纹是否唯一
```

## 🤝 参与贡献

有新的浏览器自动化 skill 想分享？欢迎提 PR！

## 📄 开源协议

[MIT](LICENSE)

---

**⭐ Star 一下方便下次找到，新 skill 持续更新中。**
