---
name: selenium
description: >
  Browser automation with Selenium WebDriver. Use this skill when the user mentions
  Selenium, WebDriver, ChromeDriver, browser testing, cross-browser automation, or
  needs to automate web interactions using the Selenium framework. Also use when
  connecting to existing browser instances via debug port.
---

# Selenium WebDriver Automation Skill

Automate browsers with Selenium — the most widely-used browser automation framework.

## Installation

```bash
pip install selenium
```

Selenium 4.6+ includes automatic driver management (no manual ChromeDriver download needed).

## Core Patterns

### Open a page

```python
from selenium import webdriver

driver = webdriver.Chrome()  # Auto-downloads matching ChromeDriver
driver.get("https://example.com")
print(driver.title)
driver.quit()
```

### Headless mode

```python
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("--headless=new")
driver = webdriver.Chrome(options=options)
```

### Find elements and interact

```python
from selenium.webdriver.common.by import By

driver.find_element(By.ID, "username").send_keys("my_user")
driver.find_element(By.ID, "password").send_keys("my_pass")
driver.find_element(By.CSS_SELECTOR, "button[type='submit']").click()
```

### Wait for elements

```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

element = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.CSS_SELECTOR, ".results"))
)

# Wait for clickable
button = WebDriverWait(driver, 10).until(
    EC.element_to_be_clickable((By.ID, "submit"))
)
button.click()
```

### Extract data

```python
heading = driver.find_element(By.TAG_NAME, "h1").text
link = driver.find_element(By.CSS_SELECTOR, "a.profile").get_attribute("href")
items = driver.find_elements(By.CSS_SELECTOR, ".product-card")
for item in items:
    name = item.find_element(By.CSS_SELECTOR, ".name").text
    price = item.find_element(By.CSS_SELECTOR, ".price").text
```

### Screenshots

```python
driver.save_screenshot("screenshot.png")
element = driver.find_element(By.ID, "chart")
element.screenshot("chart.png")
```

### Execute JavaScript

```python
driver.execute_script("window.scrollTo(0, document.body.scrollHeight)")
result = driver.execute_script("return document.title")
```

### Connect to existing browser (debug port)

```python
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.service import Service

options = Options()
options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
driver = webdriver.Chrome(options=options)
```

### Manage cookies

```python
cookies = driver.get_cookies()
driver.add_cookie({"name": "key", "value": "val"})
driver.delete_all_cookies()
```

### Multiple tabs

```python
driver.execute_script("window.open('https://example.com', '_blank')")
driver.switch_to.window(driver.window_handles[1])  # Switch to new tab
driver.switch_to.window(driver.window_handles[0])  # Back to first tab
```

## Locator strategies

| Strategy | Example |
|---|---|
| `By.ID` | `find_element(By.ID, "email")` |
| `By.CSS_SELECTOR` | `find_element(By.CSS_SELECTOR, "div.card > h2")` |
| `By.XPATH` | `find_element(By.XPATH, "//button[contains(text(),'Submit')]")` |
| `By.CLASS_NAME` | `find_element(By.CLASS_NAME, "product-title")` |
| `By.TAG_NAME` | `find_elements(By.TAG_NAME, "li")` |
| `By.LINK_TEXT` | `find_element(By.LINK_TEXT, "Click here")` |

Prefer `By.CSS_SELECTOR` — it's faster and more readable than XPath.

## Common errors

| Error | Fix |
|---|---|
| "SessionNotCreatedException" | Chrome and ChromeDriver version mismatch; update Selenium to 4.6+ for auto-management |
| "NoSuchElementException" | Element doesn't exist or hasn't loaded; use WebDriverWait |
| "ElementNotInteractableException" | Element exists but can't be clicked; scroll to it or wait for visibility |
| "StaleElementReferenceException" | Page has changed since element was found; re-find the element |

## Important notes

- Always call `driver.quit()` when done to prevent zombie Chrome processes
- Selenium does NOT auto-wait; always use `WebDriverWait` for dynamic content
- Use `find_elements` (plural) when you expect multiple results; it returns `[]` instead of raising an error
- For Selenium 4.x, use `Service` class: `webdriver.Chrome(service=Service(path), options=options)`
