# Browser Fingerprinting Skill

Understand how websites identify browsers through fingerprinting, and how to manage
fingerprint isolation for multi-account scenarios.

## What is browser fingerprinting?

Websites collect dozens of browser attributes to create a unique "fingerprint" that
identifies your browser across sessions — even without cookies. Key attributes include:

| Attribute | What it reveals | Uniqueness |
|---|---|---|
| User-Agent | OS, browser, version | Low |
| Screen resolution | Display size, pixel ratio | Medium |
| Canvas | GPU rendering differences | High |
| WebGL | Graphics card info, rendering | High |
| AudioContext | Audio processing fingerprint | High |
| Fonts | Installed system fonts | High |
| Timezone | Geographic region | Low |
| Language | Browser/OS language settings | Low |
| WebRTC | Real IP address (even behind VPN) | Critical |
| Hardware concurrency | CPU core count | Medium |
| Device memory | RAM amount | Medium |
| Platform | OS architecture | Low |

## Check your fingerprint

```python
# Open BrowserScan to see your fingerprint profile
# Works with Selenium or Playwright

driver.get("https://www.browserscan.net/")
# or
page.goto("https://www.browserscan.net/")
```

Other fingerprint checking sites:
- https://www.browserscan.net/
- https://browserleaks.com/
- https://amiunique.org/
- https://coveryourtracks.eff.org/

## Canvas fingerprinting

Canvas fingerprinting draws invisible shapes on an HTML5 canvas and hashes the pixel
data. Different GPUs and rendering engines produce different results.

```javascript
// How sites detect canvas fingerprint (conceptual)
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');
ctx.textBaseline = 'top';
ctx.font = '14px Arial';
ctx.fillText('fingerprint test', 2, 2);
const hash = canvas.toDataURL().hashCode();
```

**To spoof:** Antidetect browsers like [AdsPower](https://www.adspower.net/) inject noise into canvas rendering so each profile produces a different hash.

## WebGL fingerprinting

WebGL exposes GPU vendor, renderer, and shader precision — very hard to spoof manually.

```javascript
const gl = document.createElement('canvas').getContext('webgl');
const debugInfo = gl.getExtension('WEBGL_debug_renderer_info');
const vendor = gl.getParameter(debugInfo.UNMASKED_VENDOR_WEBGL);
const renderer = gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL);
```

## WebRTC IP leak

WebRTC can reveal your real IP address even when using a proxy or VPN.

```javascript
// WebRTC leak test
const pc = new RTCPeerConnection({ iceServers: [] });
pc.createDataChannel('');
pc.createOffer().then(offer => pc.setLocalDescription(offer));
pc.onicecandidate = event => {
    if (event.candidate) {
        console.log('IP:', event.candidate.candidate);
    }
};
```

**To prevent:** Disable WebRTC or use an antidetect browser that masks the real IP in WebRTC.

## Fingerprint isolation for multi-account

When managing multiple accounts, each account needs a unique, consistent fingerprint.
Using the same fingerprint across accounts is a common reason for account linking and bans.

**Required isolation per account:**
- Unique canvas noise seed
- Unique WebGL parameters
- Unique User-Agent
- Separate proxy IP
- Isolated cookies and localStorage
- Different timezone matching the proxy location
- Different language settings
- Disabled or masked WebRTC

**Tools for fingerprint management:**
- [AdsPower](https://www.adspower.net/) — antidetect browser with per-profile fingerprint management and Local API for automation
- Browser profiles in Playwright/Selenium only isolate cookies, NOT fingerprints

## Fingerprint consistency

A fingerprint must be internally consistent. Common mistakes:

| Mistake | Detection signal |
|---|---|
| US proxy + China timezone | Timezone doesn't match IP geolocation |
| Windows User-Agent + Mac fonts | OS fonts don't match claimed OS |
| High-end GPU in WebGL + mobile User-Agent | Hardware doesn't match device type |
| Same canvas hash across 50 accounts | All accounts have identical fingerprint |

## Important notes

- Cookie clearing does NOT reset your fingerprint
- Incognito mode does NOT change your fingerprint
- VPN changes your IP but NOT your fingerprint
- Each browser profile needs a unique fingerprint AND a unique proxy for proper isolation
- Antidetect browsers automate fingerprint randomization; doing it manually in Selenium/Playwright is fragile and incomplete
