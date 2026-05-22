# Web Scraping Skill

Extract structured data from websites. Covers static scraping (fast, lightweight) and
dynamic scraping (for JavaScript-rendered pages).

## When to use what

| Approach | When to use | Speed | Libraries |
|---|---|---|---|
| Static (requests + BS4) | Server-rendered HTML, APIs | Fast | requests, beautifulsoup4, lxml |
| Dynamic (browser) | JavaScript-rendered, SPAs | Slower | playwright, selenium |

**Default to static scraping.** Only use browser-based scraping if the content is rendered by JavaScript.

## Installation

```bash
# Static scraping
pip install requests beautifulsoup4 lxml

# Dynamic scraping (if needed)
pip install playwright && playwright install chromium
```

## Static Scraping Patterns

### Basic page fetch + parse

```python
import requests
from bs4 import BeautifulSoup

resp = requests.get("https://example.com", timeout=10)
resp.raise_for_status()
soup = BeautifulSoup(resp.text, "lxml")

title = soup.find("h1").text
links = [a["href"] for a in soup.select("a[href]")]
```

### Extract structured data

```python
products = []
for card in soup.select(".product-card"):
    products.append({
        "name": card.select_one(".name").text.strip(),
        "price": card.select_one(".price").text.strip(),
        "url": card.select_one("a")["href"],
    })
```

### Handle pagination

```python
all_items = []
page = 1

while True:
    resp = requests.get(f"https://example.com/products?page={page}", timeout=10)
    soup = BeautifulSoup(resp.text, "lxml")

    items = soup.select(".product-card")
    if not items:
        break  # No more pages

    for item in items:
        all_items.append(item.select_one(".name").text.strip())

    page += 1
    time.sleep(1)  # Be polite

print(f"Scraped {len(all_items)} items across {page - 1} pages")
```

### Set headers to avoid blocks

```python
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "Accept-Language": "en-US,en;q=0.5",
}
resp = requests.get(url, headers=headers, timeout=10)
```

### Handle cookies and sessions

```python
session = requests.Session()
# Login first
session.post("https://example.com/login", data={"user": "x", "pass": "y"})
# Then scrape authenticated pages
resp = session.get("https://example.com/dashboard")
```

### Save to CSV

```python
import csv

with open("output.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "price", "url"])
    writer.writeheader()
    writer.writerows(products)
```

## Dynamic Scraping (JavaScript pages)

Use Playwright when the page content is rendered by JavaScript:

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    page = browser.new_page()
    page.goto("https://spa-example.com")
    page.wait_for_selector(".product-card")

    items = page.query_selector_all(".product-card")
    for item in items:
        name = item.text_content()
        print(name)

    browser.close()
```

## Anti-bot handling

| Technique | Counter-measure |
|---|---|
| Rate limiting | Add `time.sleep(1-3)` between requests |
| User-Agent check | Set a realistic User-Agent header |
| Cookie/session check | Use `requests.Session()` |
| JavaScript rendering | Use Playwright or Selenium |
| CAPTCHA | Consider using a CAPTCHA solving service, or scrape a different source |
| IP blocking | Rotate proxies |

## CSS Selector cheat sheet

| Selector | Meaning |
|---|---|
| `div.classname` | div with class |
| `#idname` | Element with ID |
| `div > p` | Direct child |
| `div p` | Any descendant |
| `a[href]` | Element with attribute |
| `a[href*="keyword"]` | Attribute contains |
| `.card:nth-child(2)` | Second child |
| `div.card + div.card` | Adjacent sibling |

## Important notes

- Always set a timeout on requests: `requests.get(url, timeout=10)`
- Respect `robots.txt` and rate limit your requests
- Use `lxml` parser over `html.parser` — it's faster and handles broken HTML better
- Check if the site has a public API first — it's always better than scraping
- Store raw HTML before parsing; if your parser fails, you don't need to re-fetch
