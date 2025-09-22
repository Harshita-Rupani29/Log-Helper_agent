# Log Helper Agent

A powerful CLI tool that analyzes your failed commands and provides intelligent suggestions to fix them using AI-powered analysis.

## Features

- 🔍 **Smart Error Analysis**: Uses AI to analyze command failures and suggest fixes
- 🐚 **Multi-Shell Support**: Works with bash, zsh, fish, tcsh, and PowerShell
- 🚀 **Real-time History Access**: Reliably gets the last command from your shell
- 📚 **Rule-based Suggestions**: Built-in rules for common command errors
- 🎯 **Command Suggestions**: Suggests similar commands when you mistype
- 🌐 **Cross-Platform**: Supports macOS, Linux, and Windows

## Quick Start

### Prerequisites

- **Node.js** (version 12 or higher): Download from [nodejs.org](https://nodejs.org/)
- **npm**: Comes bundled with Node.js
- A supported shell environment

### Installation

#### Option 1: Global Installation (Recommended)

```bash
npm install -g log-helper-agent
```

Then set up shell integration:

```bash
# For Bash/Zsh
eval "$(log-helper --alias)"

# Add to your shell config for permanent setup
echo 'eval "$(log-helper --alias)"' >> ~/.bashrc  # or ~/.zshrc
```

#### Option 2: Local Development Setup

```bash
# Clone and navigate to project directory
git clone <repository-url>
cd log-helper-agent

# Install dependencies
npm install

# Test the installation
node src/main.js --help
```

### Shell Integration Setup

The tool requires one-time shell integration for optimal performance:

#### For Bash Users

**Temporary setup (current session only):**
```bash
eval "$(log-helper --alias)"
# Or for local setup: eval "$(./src/main.js --alias)"
```

**Permanent setup:**
```bash
# Add to ~/.bashrc (Linux) or ~/.bash_profile (macOS)
echo 'eval "$(log-helper --alias)"' >> ~/.bashrc
source ~/.bashrc
```

#### For Zsh Users

**Temporary setup:**
```bash
eval "$(log-helper --alias)"
```

**Permanent setup:**
```bash
# Add to ~/.zshrc
echo 'eval "$(log-helper --alias)"' >> ~/.zshrc
source ~/.zshrc
```

#### For Fish Users

```bash
# Add to ~/.config/fish/config.fish
echo 'log-helper --alias | source' >> ~/.config/fish/config.fish
source ~/.config/fish/config.fish
```

#### For Tcsh Users

**Note:** Tcsh uses a different aliasing mechanism.

```tcsh
# Add to ~/.tcshrc (replace with your actual project path)
alias log-helper 'set prev_cmd = "`history 2 | head -1 | sed '"'"'s/^[ ]*[0-9]*[ ]*//'"'"'`"; node /full/path/to/log-helper-agent/src/main.js "$prev_cmd"'

# Reload configuration
source ~/.tcshrc
```

## Usage

### Basic Usage

After setup, simply run `log-helper` after any failed command:

```bash
$ grp "hello" file.txt
grp: command not found

$ log-helper
Analyzing previous command: "grp "hello" file.txt"

Maybe you meant: grep "hello" file.txt

--- Neurolink Analysis ---
The error "grp: command not found" indicates that the shell could not find the 'grp' command. 
The correct command is likely 'grep' which is used for searching text patterns in files.
--------------------------

Did you mean one of these?
- grep
- git
- gcc
```

### Advanced Usage

#### Debug Mode
Enable detailed logging for troubleshooting:

```bash
LOG_HELPER_DEBUG=true log-helper
```

#### Analyze Specific Commands
You can analyze specific commands directly:

```bash
log-helper "your-failed-command"
# Or for local setup: node src/main.js "your-failed-command"
```

#### Shell Override
Force detection of a specific shell:

```bash
SHELL_OVERRIDE=bash log-helper
```

## Supported Platforms and Shells

### Fully Supported Shells
- ✅ **Bash** - Full support with real-time history access
- ✅ **Zsh** - Full support with real-time history access (default on macOS Catalina+)
- ✅ **Tcsh/Csh** - Full support with direct alias integration

### Partially Supported (Fallback Mode)
- ⚠️ **Fish** - Uses history file fallback method
- ⚠️ **PowerShell** (Windows) - Basic support with history file reading

### Platform Support
- **macOS**: Fully supported (Zsh/Bash/Tcsh)
- **Linux**: Fully supported (Bash/Zsh/Tcsh)
- **Windows**: Partial support (PowerShell, WSL recommended)

## How It Works

### Shell Integration Mechanisms

**Bash/Zsh**: Uses the `fc` command with dynamic function generation for real-time history access.

**Tcsh**: Uses `history 2` command with direct alias definition for reliable access to command history.

**Fallback Mode**: Reads shell history files directly from disk and uses process tree analysis to detect shell type.

### Analysis Process

1. **Shell Integration**: Captures your last command directly from shell memory or history
2. **Command Analysis**: Analyzes the failed command and its error output
3. **AI-Powered Suggestions**: Uses advanced analysis to suggest corrections and alternatives
4. **Rule-Based Fixes**: Applies built-in rules for common mistakes

## Features in Detail

### Error Analysis
- **Pattern Matching**: Built-in rules for common command errors
- **AI Analysis**: Intelligent error interpretation and suggestions using NeuroLink
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

## Troubleshooting

### Common Issues

#### "Could not retrieve the last command from history"

This usually means the shell integration isn't set up correctly.

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

**Solutions:**
1. Check that you added the correct line to your shell configuration file
2. Restart your terminal or run `source ~/.bashrc` (or equivalent for your shell)
3. For global installation, ensure the package is installed: `npm list -g log-helper-agent`

#### Commands not being analyzed

Ensure you're running `log-helper` immediately after the failed command. The tool analyzes the most recent command in your shell history.

#### Tcsh Path Issues

**Cause:** The absolute path in the tcsh alias is incorrect.

**Solution:** Use `pwd` in the project directory to get the correct path and update your alias.

### Debug Information

Enable debug mode to see detailed information about what the tool is doing:

```bash
LOG_HELPER_DEBUG=true log-helper
```

### Environment Variables

- `LOG_HELPER_DEBUG`: Enable debug logging (true/false)
- `SHELL_OVERRIDE`: Force specific shell detection (bash, zsh, fish, tcsh, etc.)
- `HISTFILE`: Custom history file path (respected by the tool)

## Quick Reference

### One-Time Setup Commands

**Bash:**
```bash
echo 'eval "$(log-helper --alias)"' >> ~/.bashrc && source ~/.bashrc
```

**Zsh:**
```bash
echo 'eval "$(log-helper --alias)"' >> ~/.zshrc && source ~/.zshrc
```

**Fish:**
```bash
echo 'log-helper --alias | source' >> ~/.config/fish/config.fish && source ~/.config/fish/config.fish
```

### Usage Workflow
1. Run a command (it may fail)
2. Type `log-helper`
3. Get AI-powered analysis and suggestions
4. Apply the suggested fix

## Configuration

The tool automatically detects your shell and adapts its behavior accordingly. No additional configuration is required for basic usage.

## Contributing

Contributions are welcome! Please feel free to submit issues and enhancement requests.

## Uninstallation

### Global Installation
```bash
npm uninstall -g log-helper-agent
```

### Remove Shell Integration
Remove the `eval "$(log-helper --alias)"` line from your shell configuration file and reload your shell.

## License

ISC License - see LICENSE file for details.
