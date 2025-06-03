# Just Prompt MCP Server - Complete Installation Guide

This guide provides step-by-step instructions for installing and configuring the Just Prompt MCP server for Claude Code CLI.

## Prerequisites

Before starting, ensure you have:
- macOS, Linux, or Windows with WSL
- Internet connection for downloading dependencies
- API keys for the LLM providers you want to use

## Step 1: Install UV Package Manager

UV is required to run this MCP server. Install it using the official installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Important**: After installation, restart your terminal or run:
```bash
source $HOME/.local/bin/env
```

Verify installation:
```bash
uv --version
```

## Step 2: Clone and Setup the Project

```bash
git clone https://github.com/yourusername/just-prompt.git
cd just-prompt
uv sync
```

## Step 3: Configure API Keys

1. Copy the environment template:
```bash
cp .env.sample .env
```

2. Edit `.env` with your actual API keys:
```bash
# Required: Add your API keys
OPENAI_API_KEY=your_actual_openai_key_here
ANTHROPIC_API_KEY=your_actual_anthropic_key_here
GEMINI_API_KEY=your_actual_gemini_key_here

# Optional: Comment out providers you don't want to use
# GROQ_API_KEY=your_groq_api_key_here
# DEEPSEEK_API_KEY=your_deepseek_api_key_here
# OLLAMA_HOST=http://localhost:11434
```

## Step 4: Test the Server

Verify the server works before adding to Claude Code:

```bash
# Test server startup
uv run just-prompt --show-providers

# Test with default models (should show your configured providers)
uv run just-prompt --help
```

Expected output should show your configured providers (e.g., "Available LLM providers: openai, anthropic, gemini").

## Step 5: Find UV Path

Claude Code needs the absolute path to UV. Find it with:

```bash
which uv
```

Common locations:
- macOS/Linux: `/Users/yourusername/.local/bin/uv`
- Windows WSL: `/home/yourusername/.local/bin/uv`

## Step 6: Install MCP Server in Claude Code

### Method 1: Simple Installation (Recommended)

Replace `/path/to/your/project` and `/path/to/uv` with your actual paths:

```bash
claude mcp add just-prompt -s user -- /Users/yourusername/.local/bin/uv --directory /full/path/to/just-prompt run just-prompt
```

### Method 2: With Custom Models

```bash
claude mcp add just-prompt -s user -- /Users/yourusername/.local/bin/uv --directory /full/path/to/just-prompt run just-prompt --default-models "openai:gpt-4o,anthropic:claude-3-5-haiku"
```

### Scope Options:
- `-s user`: Available across all your Claude Code sessions (recommended)
- `-s project`: Only available in current project
- `-s local`: Default, local to current directory

## Step 7: Verify Installation

Check MCP server status:
```bash
claude mcp list
```

You should see:
```
just-prompt: /Users/yourusername/.local/bin/uv --directory /full/path/to/just-prompt run just-prompt
```

Check connection status:
```bash
claude mcp
```

You should see:
```
• just-prompt: connected
```

## Step 8: Test in Claude Code

Open a new Claude Code session and try:
```
List available MCP tools
```

You should see tools like:
- `mcp__just-prompt__prompt`
- `mcp__just-prompt__ceo_and_board`
- `mcp__just-prompt__list_providers`

## Troubleshooting

### Common Issues and Solutions

#### 1. "spawn uv ENOENT" Error
**Problem**: Claude Code can't find the `uv` command.
**Solution**: Use the absolute path to `uv` (see Step 5).

#### 2. Server Status Shows "failed"
**Problem**: Server isn't starting properly.
**Solutions**:
- Check that your `.env` file has valid API keys
- Verify `uv sync` completed successfully
- Test server manually: `uv run just-prompt --show-providers`

#### 3. Missing API Key Warnings
**Problem**: Warnings about missing API keys for providers you don't use.
**Solution**: This is normal. Comment out unused providers in `.env` to reduce warnings.

#### 4. Permission Errors
**Problem**: Permission denied when running commands.
**Solution**: Ensure your user has read/write access to the project directory.

#### 5. Path Issues on Windows
**Problem**: Path separators causing issues.
**Solution**: Use forward slashes in paths, even on Windows, or escape backslashes.

### Debug Mode

For detailed error information:
```bash
claude --mcp-debug
```

### Log Files

Check MCP server logs at:
```
/Users/yourusername/Library/Caches/claude-cli-nodejs/[project-path]
```

## Uninstalling

To remove the MCP server:
```bash
claude mcp remove just-prompt
```

## Configuration Options

### Default Models
You can specify which models to use by default:
```bash
--default-models "openai:gpt-4o,anthropic:claude-3-5-haiku,gemini:gemini-2.0-flash-exp"
```

### Reasoning Effort (OpenAI o-series)
Use reasoning suffixes for o-series models:
- `:low` - minimal reasoning (faster, cheaper)
- `:medium` - balanced (default)
- `:high` - thorough reasoning (slower, more tokens)

Example: `openai:o3:high`

### Thinking Tokens (Claude)
For Claude 3.7 Sonnet, specify thinking token budget:
- `anthropic:claude-3-7-sonnet-20250219:4k` - 4096 thinking tokens

### Thinking Budget (Gemini)
For Gemini 2.5 Flash, specify thinking budget:
- `gemini:gemini-2.5-flash-preview-04-17:4k` - 4096 thinking budget

## Quick Reference

### Essential Commands
```bash
# Install UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# Setup project
uv sync

# Find UV path
which uv

# Add to Claude Code
claude mcp add just-prompt -s user -- /path/to/uv --directory /path/to/project run just-prompt

# Check status
claude mcp list
claude mcp

# Remove if needed
claude mcp remove just-prompt
```

### Common Paths
- UV location: `~/.local/bin/uv`
- Project directory: Use absolute path to your `just-prompt` folder
- Config file: Claude Code manages this automatically

## Support

If you encounter issues:
1. Follow the troubleshooting section above
2. Check the project logs with `claude --mcp-debug`
3. Verify your setup with manual testing: `uv run just-prompt --show-providers`
4. Open an issue on the project repository with error logs

---

**Pro Tip**: Bookmark this guide and keep your API keys secure. The MCP server will automatically start/stop as needed by Claude Code.