---
name: adspower
description: >
  Manage AdsPower antidetect browser profiles through the Local API. Use this skill when
  the user mentions AdsPower, antidetect browser, browser profiles, fingerprint browser,
  multi-account management, batch create profiles, bind proxies, export cookies, check
  fingerprints, account warmup, or browser automation with AdsPower. Also use when the user
  wants to open/close browser profiles, manage proxies across profiles, or automate tasks
  across multiple browser identities.
---

# AdsPower Local API Automation Skill

Automate AdsPower antidetect browser operations through its Local API. This skill handles
profile creation, browser control, proxy management, cookie operations, and fingerprint
verification.

## Prerequisites

- AdsPower installed and running locally (paid plan with API access)
- API Key generated (AdsPower → Automation → API → Generate)
- Python 3.8+ with `requests` installed

## API Basics

Base URL: `http://local.adspower.net:50325`

All requests require the Authorization header:
```
Authorization: Bearer YOUR_API_KEY
```

Success response: `{"code": 0, "msg": "success", "data": {...}}`
Error response: `{"code": -1, "msg": "error description"}`

## Core Operations

### 1. Check API Status

```bash
curl -s -H "Authorization: Bearer $API_KEY" \
  "http://local.adspower.net:50325/api/v1/status"
```

### 2. Create a Profile

```python
import requests

resp = requests.post(
    "http://local.adspower.net:50325/api/v1/user/create",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "name": "profile_001",
        "group_id": "0",
        "fingerprint_config": {
            "automatic_timezone": "1",
            "language": ["en-US", "en"],
        },
    },
).json()
profile_id = resp["data"]["id"]
```

### 3. Open a Browser Profile

```python
resp = requests.get(
    f"http://local.adspower.net:50325/api/v1/browser/start?user_id={profile_id}",
    headers={"Authorization": f"Bearer {API_KEY}"},
    timeout=30,  # 30s timeout for slow proxies
).json()

# Connection info for automation frameworks:
webdriver_path = resp["data"]["webdriver"]
selenium_address = resp["data"]["ws"]["selenium"]     # For Selenium
puppeteer_ws = resp["data"]["ws"]["puppeteer"]         # For Playwright
```

### 4. Close a Browser Profile

```python
requests.get(
    f"http://local.adspower.net:50325/api/v1/browser/stop?user_id={profile_id}",
    headers={"Authorization": f"Bearer {API_KEY}"},
)
```

### 5. Connect Selenium

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service

service = Service(executable_path=webdriver_path)
options = Options()
options.add_experimental_option("debuggerAddress", selenium_address)
driver = webdriver.Chrome(service=service, options=options)
driver.get("https://www.browserscan.net/")
```

### 6. Connect Playwright

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp(puppeteer_ws)
    context = browser.contexts[0]
    page = context.pages[0] if context.pages else context.new_page()
    page.goto("https://www.browserscan.net/")
```

### 7. Update Proxy

```python
requests.post(
    "http://local.adspower.net:50325/api/v1/user/update",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "user_id": profile_id,
        "user_proxy_config": {
            "proxy_soft": "other",
            "proxy_type": "http",       # http | https | socks5
            "proxy_host": "1.2.3.4",
            "proxy_port": "8080",       # Must be string
            "proxy_user": "username",
            "proxy_password": "password",
        },
    },
).json()
```

### 8. Query Profiles

```python
resp = requests.get(
    "http://local.adspower.net:50325/api/v1/user/list",
    headers={"Authorization": f"Bearer {API_KEY}"},
    params={"page": 1, "page_size": 100, "group_name": "My Campaign"},
).json()
profiles = resp["data"]["list"]
```

### 9. Create a Group

```python
resp = requests.post(
    "http://local.adspower.net:50325/api/v1/group/create",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"group_name": "My Campaign"},
).json()
group_id = resp["data"]["group_id"]
```

## Rate Limits

| Profile count | Max frequency |
|---|---|
| 0–200 | 2 req/sec |
| 200–5000 | 5 req/sec |
| > 5000 | 10 req/sec |

Profile creation is always limited to 1 req/sec. Add `time.sleep(1.1)` between batch operations.

## Common Errors

| Error | Cause | Fix |
|---|---|---|
| Connection refused | AdsPower not running | Start AdsPower, check API status |
| Invalid API key | Wrong or expired key | Regenerate in Automation → API |
| Error 100044 | Browser start timeout | Check proxy, clear cache, increase timeout |
| Error 100001 | Browser failed to start | Repair profile, update AdsPower |
| Too many requests | Rate limit exceeded | Add sleep between API calls |
| Profile not found | Wrong user_id | Query `/api/v1/user/list` to get correct IDs |

## Important Notes

- `proxy_port` must be a **string**, not an integer
- `user_id` is the internal ID from creation, not the profile name or UI number
- The API port (default 50325) can change between sessions — verify in AdsPower settings
- Always close browsers via API after automation to prevent resource leaks
- Use `resp["data"]["ws"]["selenium"]` for Selenium, `resp["data"]["ws"]["puppeteer"]` for Playwright

## Resources

- [AdsPower Official](https://www.adspower.net/)
- [Local API Documentation](https://localapi-doc-en.adspower.com/)
- [Ready-to-use Scripts & Guides](https://github.com/pencil20388-eng/awesome-adspower-automation)
