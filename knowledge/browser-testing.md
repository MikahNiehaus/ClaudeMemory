# Browser Testing with Playwright MCP

TRIGGER: browser, playwright, interactive, e2e, end-to-end, click, navigate, test app, manual test

## Overview

Interactive browser testing using Playwright MCP tools. This enables real-time browser control for testing applications - navigating pages, clicking elements, filling forms, and observing results in a visible browser window.

**Key Distinction**: This is for INTERACTIVE testing (you control the browser in real-time), NOT for writing automated test scripts (that's `test-agent` with `knowledge/testing.md`).

---

## CRITICAL RULES

### Rule 1: USE MCP TOOLS, NOT CODE

| DO | DON'T |
|----|-------|
| Use `mcp__playwright_browser_navigate` | Write Playwright scripts |
| Use `mcp__playwright_browser_click` | Use Bash to run `npx playwright` |
| Use `mcp__playwright_browser_type` | Create `.spec.ts` files |
| Use `mcp__playwright_browser_snapshot` | Generate automation code |

**WHY**: Interactive mode = direct tool usage. If you want automated tests, use `test-agent`.

**How to prompt correctly**:
- GOOD: "Use playwright mcp to open localhost:3000"
- GOOD: "Click the login button using the browser tool"
- BAD: "Write a Playwright test for this" (will generate code)
- BAD: "Create automation for the login flow" (will generate code)

### Rule 2: URL ACCESS POLICY

| URL Type | Action | Examples |
|----------|--------|----------|
| **Localhost** | AUTO-ALLOW | `localhost:*`, `127.0.0.1:*`, `*.localhost`, `[::1]` |
| **OAuth/Auth providers** | AUTO-ALLOW | See list below |
| **Other external URLs** | ASK USER | `google.com`, production sites |
| **User explicitly allows** | ALLOW | Any URL user says is okay |

**OAuth/Auth Auto-Allow List**:
- Microsoft: `*.b2clogin.com`, `login.microsoftonline.com`, `login.live.com`
- Google: `accounts.google.com`
- Auth0: `*.auth0.com`
- Okta: `*.okta.com`, `*.oktapreview.com`
- GitHub: `github.com/login/oauth`
- AWS Cognito: `*.auth.*.amazoncognito.com`
- Firebase: `*.firebaseapp.com`
- Keycloak: Common patterns for self-hosted

**WHY**: Testing should stay on localhost by default. OAuth flows naturally redirect to external providers - that's expected. Other external URLs require explicit permission.

---

## MCP Setup

### Check if Playwright MCP is Installed

```bash
# Check MCP list for playwright
claude mcp list | grep -i playwright
```

Or run `/mcp` in Claude Code and look for playwright tools.

### Install Playwright MCP (if needed)

```bash
# Basic installation
claude mcp add playwright npx '@playwright/mcp@latest'

# With localhost restriction (optional extra safety)
claude mcp add playwright npx '@playwright/mcp@latest' --allowed-origins 'localhost;127.0.0.1'
```

### Verify Installation

After installation, restart Claude Code and run `/mcp`. You should see tools like:
- `mcp__playwright_browser_navigate`
- `mcp__playwright_browser_click`
- `mcp__playwright_browser_type`
- `mcp__playwright_browser_snapshot`

---

## Essential Tools (Priority Order)

Focus on these 5 tools - they cover 90% of interactive testing:

| Tool | Purpose | When to Use |
|------|---------|-------------|
| `browser_navigate` | Go to URL | Start of any test flow |
| `browser_snapshot` | Get page state | Before clicking, to see elements |
| `browser_click` | Click element | Buttons, links, form controls |
| `browser_type` | Enter text | Form inputs, search boxes |
| `browser_take_screenshot` | Visual capture | Document findings, debug issues |

### Tool Usage Patterns

**Navigation**:
```
Navigate to localhost:3000
→ Use browser_navigate with url="http://localhost:3000"
```

**Element Interaction**:
```
Click the login button
→ First: browser_snapshot to see available elements
→ Then: browser_click with appropriate selector
```

**Form Filling**:
```
Fill in the email field
→ browser_type with selector and text
```

---

## Correct Prompting Patterns

### Starting a Test Session

GOOD:
- "Use playwright mcp to open my app at localhost:3000"
- "Open a browser to localhost:8080 using the playwright tool"
- "Navigate to localhost:3000 with the browser"

BAD:
- "Test my app" (ambiguous - might write code)
- "Create tests for localhost:3000" (will write code)

### Interacting with Elements

GOOD:
- "Click the Submit button"
- "Type 'test@example.com' in the email field"
- "Take a screenshot of the current page"

BAD:
- "Write code to click the button" (will generate script)

### When Claude Tries to Write Code

If Claude starts writing Playwright code instead of using tools, say:
- "No, use the playwright MCP tool directly, don't write code"
- "Use the browser_click tool, not a script"
- "Interactive mode - use MCP tools"

---

## Common Testing Workflows

### 1. Basic Navigation Test

1. "Use playwright mcp to open localhost:3000"
2. "Take a snapshot to see what's on the page"
3. "Click the 'About' link"
4. "Take a screenshot"

### 2. Form Testing

1. Navigate to form page
2. Snapshot to see form fields
3. Type in each field
4. Click submit
5. Snapshot to verify result

### 3. Authentication Flow

1. Navigate to login page
2. **For OAuth**: The redirect to B2C/Auth0/etc. is auto-allowed
3. You can manually log in if needed (cookies persist)
4. Continue testing authenticated state

### 4. Visual Regression Check

1. Navigate to page
2. Take screenshot
3. Make change in code
4. Refresh and take another screenshot
5. Compare visually

---

## Troubleshooting

### Problem: Claude writes code instead of using tools

**Symptoms**: Claude generates `.spec.ts` files or Playwright scripts

**Solutions**:
1. Explicitly say "use playwright mcp" in your request
2. Say "interactive mode" or "use the browser tool"
3. If it starts writing code, interrupt and say "no code, use MCP tools"

### Problem: MCP tools not appearing

**Solutions**:
1. Run `claude mcp list` to verify installation
2. Restart Claude Code after adding MCP
3. Run `/mcp` to see available tools
4. Reinstall: `claude mcp add playwright npx '@playwright/mcp@latest'`

### Problem: Browser doesn't open

**Solutions**:
1. Ensure Playwright browsers are installed: `npx playwright install`
2. Check for port conflicts
3. Try specifying browser: `--browser chromium`

### Problem: Can't find elements

**Solutions**:
1. Always take a snapshot first to see available elements
2. Use accessibility-based selectors (roles, labels)
3. Wait for page to fully load before snapshotting

### Problem: OAuth redirect blocked

**Solutions**:
1. OAuth providers are auto-allowed - should work
2. If blocked, explicitly tell Claude "allow the redirect to [domain]"
3. Check if domain is in auto-allow list

---

## Anti-Patterns to Avoid

### DON'T: Write Test Files
```
# BAD - this is automated testing, not interactive
"Write a Playwright test for the login flow"
```

### DON'T: Use Bash for Playwright
```
# BAD - bypasses MCP
"Run npx playwright test"
```

### DON'T: Navigate to Production
```
# BAD - always ask first
"Open https://production-app.com"
```

### DON'T: Skip Snapshots
```
# BAD - flying blind
"Click the submit button" (without first seeing what's on page)
```

---

## Integration with Other Agents

| Scenario | Hand Off To |
|----------|-------------|
| Found a bug during testing | `debug-agent` for root cause analysis |
| Flow works, need automated tests | `test-agent` for regression tests |
| Security concern discovered | `security-agent` for assessment |
| UI looks wrong | `ui-agent` for implementation fix |
| Performance issue observed | `performance-agent` for profiling |

---

## Session Lifecycle

### RULE 3: ASK PERMISSION BEFORE STARTING

**ALWAYS ask user permission BEFORE starting a browser session:**

```
I'm going to use Playwright to interact with [URL].

This will:
- Open a visible browser window
- Navigate to [localhost:PORT / allowed URL]
- Allow me to click/type/interact with the page

Start browser session? (All localhost interactions will be auto-approved)
```

Only proceed after user confirms.

### RULE 4: CLOSE BROWSER WHEN DONE

**ALWAYS close the browser session when testing is complete:**

1. After completing all requested testing actions
2. Before moving to unrelated tasks
3. If user says "stop", "done", or "that's enough"

Use `browser_close` tool to close the session cleanly.

**Report session end:**
```
Browser session closed.
- Actions performed: [N]
- Findings: [summary]
- Screenshots saved: [if any]
```

### RULE 5: VERIFY LOCALHOST BEFORE EVERY NAVIGATION

**Before ANY navigation, check the URL:**

```
Pre-Navigation Check:
□ Is URL localhost/127.0.0.1? → Proceed
□ Is URL an OAuth provider? → Proceed (login flow)
□ Is URL external? → STOP, ask user permission
□ Is URL a production domain? → STOP, warn user, ask permission
```

**Production Detection Patterns:**
- Contains "prod", "production", "live" in domain
- Known production domains for this project
- No port number AND not localhost
- HTTPS without localhost

---

## Permission & Auto-Approval

### What Gets Auto-Approved (Localhost)

Once user starts a browser session, these are AUTO-APPROVED:
- Navigation to `localhost:*`
- Navigation to `127.0.0.1:*`
- All clicks, types, snapshots on localhost pages
- Screenshots
- OAuth redirects that return to localhost

### What Requires Permission

- Starting the browser session (first time)
- Navigation to external non-OAuth URLs
- Navigation to production URLs
- Any URL the user hasn't approved

### Sample Permission Request

```
I need to navigate to [external-url].

This is NOT localhost. Proceed?
- Yes: Allow this navigation
- No: Stay on localhost only
```

---

## Session State

- **Cookies persist** for the duration of the session
- **Auth state** carries over between navigations
- **Browser window** stays open between tool calls
- **You can manually interact** with the visible browser

This means:
- Log in once, stay logged in
- OAuth tokens persist
- Can mix manual and Claude-controlled interaction

---

## Session Checklist

### Starting a Session
- [ ] Ask user permission to start browser
- [ ] Verify target URL is localhost (or user-approved)
- [ ] Confirm MCP tools are available (`/mcp`)
- [ ] Take initial snapshot after navigation

### During Session
- [ ] Snapshot before clicking unknown elements
- [ ] Check URL after each navigation (ensure still localhost)
- [ ] Take screenshots of important states
- [ ] Note any issues or findings

### Ending a Session
- [ ] Confirm all requested testing is complete
- [ ] Take final screenshot if needed
- [ ] Close browser with `browser_close`
- [ ] Report summary of actions and findings
