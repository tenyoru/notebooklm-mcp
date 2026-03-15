# Browser Configuration

The NotebookLM MCP server supports multiple browsers beyond Chrome. You can configure which browser to use via environment variables.

## Supported Browsers

- **Chrome** (default) - Google Chrome stable
- **Chromium** - Open-source Chromium browser
- **Firefox** - Mozilla Firefox
- **Zen** - Zen Browser (Firefox-based, privacy-focused)

## Configuration

### Environment Variables

Set these environment variables to configure your browser:

```bash
# Browser type: chrome, chromium, firefox, or zen
export BROWSER_TYPE=zen

# Optional: Path to browser executable
# Required for Zen Browser and custom installations
export BROWSER_EXECUTABLE_PATH=/path/to/browser/executable
```

### Common Browser Paths

#### Zen Browser

**Linux:**
```bash
export BROWSER_TYPE=zen
export BROWSER_EXECUTABLE_PATH=/opt/zen-browser/zen-bin
# Or if installed via Flatpak:
export BROWSER_EXECUTABLE_PATH=/var/lib/flatpak/app/io.github.zen_browser.zen/current/active/files/bin/zen
# Or if installed via AppImage:
export BROWSER_EXECUTABLE_PATH=$HOME/Applications/zen-browser.AppImage
```

**macOS:**
```bash
export BROWSER_TYPE=zen
export BROWSER_EXECUTABLE_PATH="/Applications/Zen Browser.app/Contents/MacOS/zen"
```

**Windows:**
```bash
set BROWSER_TYPE=zen
set BROWSER_EXECUTABLE_PATH="C:\Program Files\Zen Browser\zen.exe"
```

#### Firefox

**Linux:**
```bash
export BROWSER_TYPE=firefox
export BROWSER_EXECUTABLE_PATH=/usr/bin/firefox
```

**macOS:**
```bash
export BROWSER_TYPE=firefox
export BROWSER_EXECUTABLE_PATH="/Applications/Firefox.app/Contents/MacOS/firefox"
```

**Windows:**
```bash
set BROWSER_TYPE=firefox
set BROWSER_EXECUTABLE_PATH="C:\Program Files\Mozilla Firefox\firefox.exe"
```

#### Chromium

**Linux:**
```bash
export BROWSER_TYPE=chromium
export BROWSER_EXECUTABLE_PATH=/usr/bin/chromium-browser
```

**macOS:**
```bash
export BROWSER_TYPE=chromium
export BROWSER_EXECUTABLE_PATH="/Applications/Chromium.app/Contents/MacOS/Chromium"
```

## MCP Configuration

Add the environment variables to your MCP configuration file:

### Claude Code

Edit `~/.config/Claude/mcp.json`:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "npx",
      "args": ["-y", "notebooklm-mcp@latest"],
      "env": {
        "BROWSER_TYPE": "zen",
        "BROWSER_EXECUTABLE_PATH": "/opt/zen-browser/zen-bin"
      }
    }
  }
}
```

### Cursor

Edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "npx",
      "args": ["-y", "notebooklm-mcp@latest"],
      "env": {
        "BROWSER_TYPE": "zen",
        "BROWSER_EXECUTABLE_PATH": "/opt/zen-browser/zen-bin"
      }
    }
  }
}
```

### Codex

Edit `~/.codex/mcp.json`:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "npx",
      "args": ["-y", "notebooklm-mcp@latest"],
      "env": {
        "BROWSER_TYPE": "zen",
        "BROWSER_EXECUTABLE_PATH": "/opt/zen-browser/zen-bin"
      }
    }
  }
}
```

## Finding Your Browser Executable

### Linux

```bash
# Zen Browser
which zen-bin
# Or if installed via package manager:
ls /opt/zen-browser/

# Firefox
which firefox

# Chromium
which chromium-browser
```

### macOS

```bash
# List Applications folder
ls /Applications/ | grep -i "zen\|firefox\|chromium"

# Check typical paths
ls "/Applications/Zen Browser.app/Contents/MacOS/"
ls "/Applications/Firefox.app/Contents/MacOS/"
ls "/Applications/Chromium.app/Contents/MacOS/"
```

### Windows

```powershell
# Check Program Files
dir "C:\Program Files" | findstr /i "zen firefox chromium"

# Common paths
dir "C:\Program Files\Zen Browser\"
dir "C:\Program Files\Mozilla Firefox\"
dir "C:\Program Files\Chromium\"
```

## Verification

After configuring your browser, test the setup:

```bash
# Run the MCP server directly to see startup logs
npx notebooklm-mcp@latest

# Or check health via Claude Code
# In Claude Code, ask: "Check NotebookLM health"
```

You should see logs indicating your browser type:
```
🚀 Launching persistent ZEN context...
📍 Profile location: /home/user/.local/share/notebooklm-mcp/chrome_profile
```

## Troubleshooting

### Browser not found

**Error:** `browserType.launchPersistentContext: Executable doesn't exist`

**Solution:** Verify the browser executable path:
```bash
# Test if the path is correct
ls -la "$BROWSER_EXECUTABLE_PATH"

# For Zen Browser on Linux, common locations:
ls /opt/zen-browser/zen-bin
ls ~/.local/bin/zen
ls ~/Applications/zen-browser.AppImage
```

### Profile conflicts

If you switch browsers, you may need to create a fresh profile:

```bash
# Remove old profile (backup first if needed)
rm -rf ~/.local/share/notebooklm-mcp/chrome_profile
```

### Permission issues

Ensure the browser executable has execute permissions:

```bash
chmod +x "$BROWSER_EXECUTABLE_PATH"
```

## Why Use Zen Browser?

Zen Browser is a Firefox-based browser with privacy-first features:

- **Privacy-focused**: Built-in tracker blocking and fingerprint protection
- **Lightweight**: Optimized performance with minimal resource usage
- **Firefox engine**: Full compatibility with Firefox extensions
- **Open source**: Community-driven development

Combined with NotebookLM MCP's stealth features, Zen Browser provides an excellent balance of privacy and automation capabilities.

## Technical Details

### Browser Profile Storage

All browsers share the same profile directory structure:
```
~/.local/share/notebooklm-mcp/chrome_profile/  (Linux)
~/Library/Application Support/notebooklm-mcp/chrome_profile/  (macOS)
%APPDATA%\notebooklm-mcp\chrome_profile\  (Windows)
```

The name "chrome_profile" is kept for backwards compatibility, but works with all browser types.

### Session Persistence

Authentication and session data persist across browser changes, stored in:
```
~/.local/share/notebooklm-mcp/browser_state/state.json
```

When switching browsers, you may need to re-authenticate if cookie formats differ between Chromium-based and Firefox-based browsers.
