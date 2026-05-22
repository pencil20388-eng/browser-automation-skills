# Chrome DevTools MCP + AdsPower Integration Skill

Connect Google's Chrome DevTools MCP to AdsPower antidetect browser profiles. This lets
AI coding agents (Claude Code, Cursor, Codex CLI) control fingerprint-isolated browser
sessions through natural language.

## Why This Combination

- **Chrome DevTools MCP** gives AI agents eyes in the browser: inspect DOM, monitor network, run JavaScript, take screenshots
- **AdsPower** gives each browser session a unique fingerprint, proxy, and identity
- **Together**: your AI agent can operate across multiple isolated browser identities — each with its own fingerprint, cookies, and IP address

```
Claude Code → Chrome DevTools MCP → AdsPower Profile (unique fingerprint + proxy)
                                  → AdsPower Profile (unique fingerprint + proxy)
                                  → AdsPower Profile (unique fingerprint + proxy)
```

## How It Works

AdsPower's Local API opens browser profiles and exposes a Chrome DevTools Protocol (CDP)
endpoint for each one. Chrome DevTools MCP connects to that endpoint via `--browserUrl`.

## Prerequisites

- [AdsPower](https://www.adspower.net/download) installed and running (paid plan with API access)
- Chrome DevTools MCP installed (`npm install -g chrome-devtools-mcp@latest`)
- API Key generated (AdsPower → Automation → API → Generate)
- Python 3.8+ with `requests` installed

## Step 1: Open an AdsPower Profile and Get the CDP Endpoint

```python
import requests

API_KEY = "your_api_key"
BASE_URL = "http://local.adspower.net:50325"
HEADERS = {"Authorization": f"Bearer {API_KEY}"}

PROFILE_ID = "your_profile_id"

# Open the browser profile
resp = requests.get(
    f"{BASE_URL}/api/v1/browser/start?user_id={PROFILE_ID}",
    headers=HEADERS,
    timeout=30,
).json()

if resp["code"] == 0:
    # This is the CDP WebSocket endpoint
    ws_endpoint = resp["data"]["ws"]["puppeteer"]
    debug_port = ws_endpoint.split(":")[2].split("/")[0]
    print(f"CDP WebSocket: {ws_endpoint}")
    print(f"Debug port: {debug_port}")
else:
    print(f"Error: {resp['msg']}")
```

The `ws_endpoint` looks like: `ws://127.0.0.1:XXXXX/devtools/browser/GUID`

## Step 2: Connect Chrome DevTools MCP to the AdsPower Profile

Once the profile is open and you have the debug port, connect Chrome DevTools MCP:

```bash
# Connect to the AdsPower browser instance
npx chrome-devtools-mcp@latest --browserUrl=http://127.0.0.1:XXXXX
```

Replace `XXXXX` with the actual port from Step 1.

### Configure in Claude Code

Add to your MCP config (`.claude/mcp.json` or `settings.json`):

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--browserUrl=http://127.0.0.1:XXXXX"
      ]
    }
  }
}
```

## Step 3: Use Natural Language to Control the Browser

Once connected, you can tell your AI agent:

```
> Navigate to amazon.com and check if the account is still logged in

> Take a screenshot of the current page

> Check the network requests for any blocked or failed calls

> Run document.title in the browser console and tell me what page this is

> Inspect the fingerprint by visiting browserscan.net
```

The AI agent sees the browser through Chrome DevTools MCP, while AdsPower ensures
the browser has a unique fingerprint and proxy.

## Automating Multiple Profiles

To work across multiple AdsPower profiles sequentially:

```python
import requests
import subprocess
import time

API_KEY = "your_api_key"
BASE_URL = "http://local.adspower.net:50325"
HEADERS = {"Authorization": f"Bearer {API_KEY}"}

def get_profiles(group_name):
    resp = requests.get(
        f"{BASE_URL}/api/v1/user/list",
        headers=HEADERS,
        params={"group_name": group_name, "page": 1, "page_size": 100},
    ).json()
    return resp["data"]["list"]

def open_profile(profile_id):
    resp = requests.get(
        f"{BASE_URL}/api/v1/browser/start?user_id={profile_id}",
        headers=HEADERS,
        timeout=30,
    ).json()
    if resp["code"] == 0:
        ws = resp["data"]["ws"]["puppeteer"]
        port = ws.split(":")[2].split("/")[0]
        return port
    return None

def close_profile(profile_id):
    requests.get(
        f"{BASE_URL}/api/v1/browser/stop?user_id={profile_id}",
        headers=HEADERS,
    )

# Process each profile
profiles = get_profiles("My Campaign")
for p in profiles:
    port = open_profile(p["user_id"])
    if port:
        print(f"Profile {p['name']} ready on port {port}")
        print(f"Connect MCP: npx chrome-devtools-mcp@latest --browserUrl=http://127.0.0.1:{port}")
        # Your automation here...
        input("Press Enter when done with this profile...")
        close_profile(p["user_id"])
        time.sleep(2)
```

## Use Cases

| Scenario | What happens |
|---|---|
| Multi-account health check | Open each profile → MCP checks login status, screenshots dashboard |
| Ad verification | Open profiles with different geo proxies → MCP inspects ad content per region |
| Fingerprint auditing | Open profiles → MCP navigates to BrowserScan → reads fingerprint results |
| Authenticated scraping | Open profile with saved cookies → MCP extracts data from logged-in pages |
| A/B test across identities | Open multiple profiles → MCP captures different UI versions |

## Important Notes

- Each AdsPower profile exposes its own CDP port — you can only connect one MCP instance per profile at a time
- Close the MCP connection before closing the AdsPower profile to avoid orphaned processes
- The CDP port changes every time a profile is opened — always read it fresh from the API response
- AdsPower handles fingerprint and proxy; Chrome DevTools MCP handles browser control and inspection
- Rate limit: wait 1-2 seconds between opening/closing profiles

## Resources

- [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [AdsPower Local API Docs](https://localapi-doc-en.adspower.com/)
- [AdsPower Official](https://www.adspower.net/)
- [More AdsPower automation scripts](https://github.com/pencil20388-eng/awesome-adspower-automation)
