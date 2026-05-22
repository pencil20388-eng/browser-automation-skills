# Puppeteer Browser Automation Skill

Automate Chrome/Chromium with Puppeteer — Google's Node.js library for Chrome DevTools Protocol.

## Installation

```bash
npm install puppeteer          # Downloads Chromium (~170MB)
# or
npm install puppeteer-core     # No bundled browser (bring your own)
```

## Core Patterns

### Open a page

```javascript
const puppeteer = require('puppeteer');

const browser = await puppeteer.launch({ headless: true });
const page = await browser.newPage();
await page.goto('https://example.com');
console.log(await page.title());
await browser.close();
```

### Click, type, select

```javascript
await page.click('button.submit');
await page.type('#username', 'my_user');        // Types character by character
await page.type('#password', 'my_pass');
await page.select('select#country', 'US');
await page.keyboard.press('Enter');
```

### Wait for elements

```javascript
await page.waitForSelector('.results', { timeout: 10000 });
await page.waitForNavigation({ waitUntil: 'networkidle0' });
await page.waitForFunction('document.querySelector(".loaded")');
```

### Extract data

```javascript
const title = await page.$eval('h1', el => el.textContent);
const href = await page.$eval('a.link', el => el.getAttribute('href'));
const items = await page.$$eval('.product', els =>
  els.map(el => ({
    name: el.querySelector('.name').textContent,
    price: el.querySelector('.price').textContent,
  }))
);
```

### Screenshots and PDF

```javascript
await page.screenshot({ path: 'page.png' });
await page.screenshot({ path: 'full.png', fullPage: true });
await page.pdf({ path: 'report.pdf', format: 'A4', printBackground: true });
```

### Network interception

```javascript
await page.setRequestInterception(true);
page.on('request', request => {
  if (request.resourceType() === 'image') {
    request.abort();
  } else {
    request.continue();
  }
});
```

### Connect to existing browser

```javascript
const browser = await puppeteer.connect({
  browserWSEndpoint: 'ws://127.0.0.1:9222/devtools/browser/xxx'
});
```

### Set viewport and user agent

```javascript
await page.setViewport({ width: 1920, height: 1080 });
await page.setUserAgent('Mozilla/5.0 ...');
```

## Common errors

| Error | Fix |
|---|---|
| "Navigation timeout" | Increase timeout: `page.goto(url, {timeout: 60000})` |
| "Node is detached from document" | Page changed; re-query the element |
| "Protocol error: Target closed" | Browser or page was closed unexpectedly |

## Important notes

- Puppeteer is Node.js only; for Python use Playwright or pyppeteer
- `headless: "new"` uses the new headless mode (more like real Chrome)
- `page.evaluate()` runs JavaScript in the browser context, not Node.js
- Always `await browser.close()` to prevent zombie processes
- `waitUntil: 'networkidle0'` = no network requests for 500ms (good for SPAs)
