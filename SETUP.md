# Log Helper: Complete Setup Guide

This guide provides a comprehensive walkthrough for setting up, configuring, and using the Log Helper project across different platforms and shell environments.

## 1. Project Overview

Log Helper is a command-line tool designed to intercept errors from your last executed command, analyze them, and suggest corrections or provide insights. It works by integrating with your shell to gain access to your command history and uses NeuroLink to provide AI-powered analysis  for helpful suggestions.

## 2. Prerequisites

- **Node.js** (version 12 or higher): Download from [nodejs.org](https://nodejs.org/)
- **npm**: Comes bundled with Node.js
- A supported shell

## 3. Supported Platforms and Shells

### Fully Supported Shells
All of these shells are fully supported, but they use different aliasing mechanisms:

- **Zsh** (Default on macOS Catalina+)
  - Uses: `eval` with dynamic alias generation
  - History file: `~/.zsh_history`
  - Configuration file: `~/.zshrc`

- **Bash** (Common on Linux, older macOS)
  - Uses: `eval` with dynamic alias generation
  - History file: `~/.bash_history` or `$HISTFILE`
  - Configuration file: `~/.bashrc` or `~/.bash_profile`

- **Tcsh/Csh**
  - Uses: Direct alias definition with history command
  - History file: `~/.history`
  - Configuration file: `~/.tcshrc` or `~/.cshrc`

### Partially Supported (Fallback Mode)
These shells fall back to reading history files directly:

- **Fish**
  - History file: `~/.local/share/fish/fish_history`
  - Configuration file: `~/.config/fish/config.fish`

- **PowerShell** (Windows)
  - History file: `%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

### Platform Support
- **macOS**: Fully supported (Zsh/Bash/Tcsh)
- **Linux**: Fully supported (Bash/Zsh/Tcsh)
- **Windows**: Partial support (PowerShell, WSL recommended)

## 4. Installation

### Step 1: Clone and Install Dependencies

```bash
# Navigate to project directory
cd /path/to/log-helper-agent

# Install dependencies
npm install
```

### Step 2: Test Basic Functionality

```bash
# Test the script directly
node src/main.js --help

# Test alias generation (for bash/zsh)
node src/main.js --alias
```

## 5. Shell-Specific Configuration

### For Bash Users

**Temporary setup (current session only):**
```bash
eval "$(./src/main.js --alias)"
```

**Permanent setup:**
```bash
# Add to ~/.bashrc (Linux) or ~/.bash_profile (macOS)
echo 'eval "$('"$(pwd)"'/src/main.js --alias)"' >> ~/.bashrc

# Reload configuration
source ~/.bashrc
```

### For Zsh Users

**Temporary setup (current session only):**
```bash
eval "$(./src/main.js --alias)"
```

**Permanent setup:**
```bash
# Add to ~/.zshrc
echo 'eval "$('"$(pwd)"'/src/main.js --alias)"' >> ~/.zshrc

# Reload configuration
source ~/.zshrc
```

### For Tcsh Users

**Note:** Tcsh uses a different aliasing mechanism and cannot use the `--alias` flag.

**Permanent setup:**
```tcsh
# Add to ~/.tcshrc
# Replace /full/path/to/log-helper-agent with your actual project path
alias log-helper 'set prev_cmd = "`history 2 | head -1 | sed '"'"'s/^[ ]*[0-9]*[ ]*//'"'"'`"; node /full/path/to/log-helper-agent/src/main.js "$prev_cmd"'
```

**To get your full path:**
```bash
pwd  # Run this in the project directory to get the full path
```

**Reload configuration:**
```tcsh
source ~/.tcshrc
```

### For Fish Users

```bash
# Add to ~/.config/fish/config.fish
echo 'eval ('"$(pwd)"'/src/main.js --alias)' >> ~/.config/fish/config.fish

# Reload configuration
source ~/.config/fish/config.fish
```

## 6. Usage

### Basic Usage (Same for All Shells)

1. Run any command that might fail:
   ```bash
   git psuh origin main
   # Error: git: 'psuh' is not a git command. See 'git --help'.
   ```

2. Immediately run the log helper:
   ```bash
   log-helper
   ```

3. The tool will analyze the error and provide suggestions:
   ```
   Analyzing previous command: "git psuh origin main"
   
   Maybe you meant: git push origin main
   
   --- Neurolink Analysis ---
   The command failed because 'psuh' is not a valid git command...
   --------------------------
   ```

### Advanced Usage

#### Debug Mode
Enable detailed logging to troubleshoot issues:

```bash
LOG_HELPER_DEBUG=true log-helper
```

#### Manual Command Analysis
Analyze a specific command directly:

```bash
node src/main.js "your command here"
```

#### Shell Override
Force detection of a specific shell:

```bash
SHELL_OVERRIDE=bash log-helper
```

## 7. How It Works

### Bash/Zsh Mechanism
- Uses the `fc` command to access shell history
- Dynamic function generation provides optimal integration
- Real-time access to command history

### Tcsh Mechanism
- Uses `history 2` command to get the previous command
- Direct alias definition with command substitution
- Reliable access to tcsh history format

### Fallback Mechanism
- Reads shell history files directly from disk
- Uses process tree analysis to detect shell type
- Less reliable for the most recent commands

## 8. Features

### Error Analysis
- **Pattern Matching**: Built-in rules for common command errors
- **AI Analysis**: Intelligent error interpretation and suggestions
- **Command Correction**: Suggests likely intended commands for typos
- **History Context**: Uses command history for better analysis

### Shell Integration
- **Multi-shell Support**: Native support for bash, zsh, and tcsh
- **History Access**: Retrieves commands from shell history or live session
- **Process Tree Analysis**: Intelligently detects your current shell

### Customization
- **Rule System**: Extensible rule-based error detection
- **Environment Variables**: Configurable behavior through env vars
- **Debug Logging**: Detailed troubleshooting information

## 9. Troubleshooting

### Common Issues

#### "Could not determine the last command from history"
**Solutions:**
```bash
# For Bash - ensure history is enabled
echo 'HISTSIZE=1000' >> ~/.bashrc
echo 'SAVEHIST=1000' >> ~/.bashrc

# For Zsh - check history settings
echo 'HISTSIZE=1000' >> ~/.zshrc
echo 'SAVEHIST=1000' >> ~/.zshrc

# For Tcsh - ensure history is enabled
echo 'set history = 1000' >> ~/.tcshrc
echo 'set savehist = 1000' >> ~/.tcshrc
```

#### "log-helper: command not found"
**Cause:** The alias wasn't set up correctly or shell config wasn't reloaded.

**Solution:** 
1. Check that you added the correct line to your shell config
2. Restart your terminal or run `source ~/.bashrc` (or equivalent)

#### Tcsh Path Issues
**Cause:** The absolute path in the tcsh alias is incorrect.

**Solution:** Use `pwd` in the project directory to get the correct path and update your alias.

### Debug Information

Enable debug mode to see detailed information:

```bash
LOG_HELPER_DEBUG=true log-helper
```

### Environment Variables

- `LOG_HELPER_DEBUG`: Enable debug logging (true/false)
- `SHELL_OVERRIDE`: Force specific shell detection (bash, zsh, fish, tcsh, etc.)
- `HISTFILE`: Custom history file path (respected by the tool)

## 10. Quick Reference

### One-Time Setup Commands

**Bash:**
```bash
echo 'eval "$('"$(pwd)"'/src/main.js --alias)"' >> ~/.bashrc && source ~/.bashrc
```

**Zsh:**
```bash
echo 'eval "$('"$(pwd)"'/src/main.js --alias)"' >> ~/.zshrc && source ~/.zshrc
```

**Tcsh:**
```tcsh
echo 'alias log-helper '"'"'set prev_cmd = "`history 2 | head -1 | sed '"'"'"'"'"'"'"'"'s/^[ ]*[0-9]*[ ]*//'"'"'"'"'"'"'"'"'`"; node '"$(pwd)"'/src/main.js "$prev_cmd"'"'"'' >> ~/.tcshrc && source ~/.tcshrc
```

### Usage Workflow
1. Run a command (it may fail)
2. Type `log-helper`
3. Get analysis and suggestions

## 11. Uninstallation

1. Remove the alias from your shell configuration file
2. Reload your shell configuration
3. Optionally remove the project directory

---

The tool is designed to be set up once and work seamlessly across different shell environments. Each shell uses the most appropriate mechanism for accessing command history.
