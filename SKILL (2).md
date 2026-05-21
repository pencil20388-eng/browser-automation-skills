---
name: playwright
description: >
  Browser automation with Playwright. Use this skill when the user mentions Playwright,
  browser testing, web scraping with a browser, headless Chrome, page screenshots,
  network interception, or needs to automate interactions with web pages (clicking,
  typing, navigating, extracting data). Also use for PDF generation from web pages,
  visual regression testing, and multi-browser automation.
---

# Playwright Browser Automation Skill

Automate browsers with Playwright — Microsoft's modern browser automation framework.
Faster and more reliable than Selenium, with built-in auto-wait and network control.

## Installation

```bash
pip install playwright
playwright install chromium  # or: playwright install  (all browsers)
```

## Core Patterns

### Open a page and navigate

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)  # headless=True for no UI
    page = browser.new_page()
    page.goto("https://example.com")
    print(page.title())
    browser.close()
```

### Async version

```python
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page()
        await page.goto("https://example.com")
        print(await page.title())
        await browser.close()

asyncio.run(main())
```

### Click, fill, select

```python
page.click("button.submit")                        # Click a button
page.fill("#username", "my_user")                   # Fill an input
page.fill("#password", "my_pass")
page.select_option("select#country", "US")          # Select dropdown
page.check("input#agree")                           # Check a checkbox
page.press("#search", "Enter")                      # Press a key
```

### Wait for elements

```python
page.wait_for_selector(".results", timeout=10000)   # Wait up to 10s
page.wait_for_load_state("networkidle")              # Wait for network quiet
page.wait_for_url("**/dashboard")                    # Wait for navigation
```

### Extract data

```python
text = page.text_content("h1")                      # Single element text
href = page.get_attribute("a.link", "href")          # Attribute value
items = page.query_selector_all(".product")          # Multiple elements
for item in items:
    name = item.text_content()
    price = item.query_selector(".price").text_content()
```

### Screenshots

```python
page.screenshot(path="page.png")                     # Viewport screenshot
page.screenshot(path="full.png", full_page=True)     # Full page
element = page.query_selector(".chart")
element.screenshot(path="chart.png")                 # Element only
```

### PDF generation

```python
page.pdf(path="report.pdf", format="A4", print_background=True)
```

### Network interception

```python
# Block images to speed up loading
def handle_route(route):
    if route.request.resource_type == "image":
        route.abort()
    else:
        route.continue_()

page.route("**/*", handle_route)
page.goto("https://example.com")

# Intercept and modify a request
def modify_request(route):
    headers = {**route.request.headers, "X-Custom": "value"}
    route.continue_(headers=headers)

page.route("**/api/**", modify_request)
```

### Multiple browser contexts (isolated sessions)

```python
context1 = browser.new_context()
context2 = browser.new_context()
page1 = context1.new_page()  # Separate cookies, storage
page2 = context2.new_page()  # Completely isolated
```

### Set viewport and emulate devices

```python
page.set_viewport_size({"width": 1920, "height": 1080})

# Or emulate a device
iphone = p.devices["iPhone 13"]
context = browser.new_context(**iphone)
```

## Multi-browser support

```python
browser = p.chromium.launch()    # Chrome/Edge
browser = p.firefox.launch()     # Firefox
browser = p.webkit.launch()      # Safari
```

## Common errors

| Error | Fix |
|---|---|
| "Browser not installed" | Run `playwright install chromium` |
| "Timeout waiting for selector" | Increase timeout or check selector |
| "Navigation failed" | Page might require JavaScript; use `wait_for_load_state` |
| "Element not visible" | Use `force=True` or wait for visibility |

## Important notes

- Playwright auto-waits for elements before interacting — usually no need for `time.sleep()`
- Use `headless=False` for debugging, `headless=True` (default) for production
- Each `browser.new_context()` creates an isolated session (separate cookies, localStorage)
- `page.goto()` waits for `load` event by default; use `wait_until="networkidle"` for SPAs
