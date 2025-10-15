# Claude Code Plugin Marketplace

A marketplace for hosting Claude Code plugins.

## Structure

- `.claude-plugin/` - Marketplace configuration
  - `marketplace.json` - Marketplace metadata
- `plugins/` - Directory containing all hosted plugins

## Adding Plugins

To add a new plugin to this marketplace:

1. Create a new directory under `plugins/`
2. Add your plugin files following the Claude Code plugin structure
3. Update `.claude-plugin/marketplace.json` to include the new plugin

## Usage

This marketplace can be used with Claude Code to provide access to multiple plugins in one location.
