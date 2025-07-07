# 🍒 edit-composer-json

A cross-platform tool for merging `composer.json` configuration from GitHub repositories into your root package.

## What it does

This tool fetches a `composer.json` file from a GitHub repository and merges any configuration found in the `extra.root-package` section into your local `composer.json` file. This is useful for distributing common Composer configuration across multiple projects.

## Requirements

The tool automatically detects and uses the best available JSON processor:
- **jq** (preferred) - if available
- **Python 3** - if available
- **Python 2** - as fallback

No manual installation required - it works out of the box on macOS and in DDEV containers.

## Usage

### Basic usage

```bash
# Using curl (works on macOS, Linux, etc.)
curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s vendor/package-name

# Using ddev (for Drupal projects)
ddev exec 'curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s vendor/package-name'
```

### With custom composer.json location

```bash
# Using curl with custom path
curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s -f /path/to/composer.json vendor/package-name

# Using ddev with custom path
ddev exec 'curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s -f /var/www/html/composer.json vendor/package-name'
```

## Source Repository Structure

For this tool to work, the source GitHub repository must have a `composer.json` file with an `extra.root-package` section. Here's the expected structure:

```json
{
    "name": "vendor/package-name",
    "description": "Example package with root-package configuration",
    "extra": {
        "root-package": {
            "repositories": [
                {
                    "type": "github",
                    "url": "https://github.com/some/custom-repo"
                }
            ],
            "scripts": {
                "post-install-cmd": "echo 'Installation complete'",
                "test": "phpunit"
            },
            "require": {
                "monolog/monolog": "^2.0",
                "symfony/console": "^5.0"
            },
            "require-dev": {
                "phpunit/phpunit": "^9.0"
            },
            "autoload": {
                "psr-4": {
                    "Vendor\\Package\\": "src/"
                }
            },
            "autoload-dev": {
                "psr-4": {
                    "Vendor\\Package\\Tests\\": "tests/"
                }
            },
            "config": {
                "sort-packages": true,
                "optimize-autoloader": true
            }
        }
    }
}
```

## Supported Sections

The tool will merge these sections if they exist in the `extra.root-package`:

- **`repositories`** - Adds to existing repositories array
- **`scripts`** - Adds to existing scripts object
- **`require`** - Adds to existing require object
- **`require-dev`** - Adds to existing require-dev object
- **`autoload`** - Adds to existing autoload object
- **`autoload-dev`** - Adds to existing autoload-dev object
- **`config`** - Adds to existing config object

## Examples

### Example 1: Adding a custom repository and script

Source repository `cherryhill/example-custom-project` has:
```json
{
    "extra": {
        "root-package": {
            "repositories": [
                {
                    "type": "github",
                    "url": "https://github.com/cherryhill/private-package"
                }
            ],
            "scripts": {
                "post-update-cmd": "./vendor/cherryhill/example-custom-project/install.sh"
            }
        }
    }
}
```

Running:
```bash
curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s cherryhill/example-custom-project
```

Results in your `composer.json`:
```json
{
    "repositories": [
        {
            "type": "github",
            "url": "https://github.com/cherryhill/private-package"
        }
    ],
    "scripts": {
        "post-update-cmd": "./vendor/cherryhill/example-custom-project/install.sh"
    }
}
```

### Example 2: Adding development dependencies

Source repository `cherryhill/drupal-tools` has:
```json
{
    "extra": {
        "root-package": {
            "require-dev": {
                "drupal/core-dev": "^9.0",
                "phpunit/phpunit": "^9.0"
            },
            "scripts": {
                "test": "phpunit",
                "cs": "phpcs"
            }
        }
    }
}
```

Running:
```bash
ddev exec 'curl -LsSf https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/merge | bash -s cherryhill/drupal-tools'
```

Adds the development dependencies and test scripts to your project.

## How it works

1. **Fetches** the `composer.json` from `https://raw.githubusercontent.com/{vendor}/{package}/main/composer.json`
2. **Checks** for an `extra.root-package` section
3. **Merges** all found sections into your local `composer.json`
4. **Preserves** existing configuration (additive, not destructive)
5. **Handles** both objects (scripts, require) and arrays (repositories) appropriately

## Error Handling

- **No `extra.root-package` section**: Gracefully skips with a message
- **Invalid JSON**: Clear error messages
- **Network issues**: Helpful error messages for fetch failures
- **Missing tools**: Suggests installing jq or ensuring Python is available

## Cross-Platform Support

- **macOS**: Uses Python (no jq required)
- **DDEV containers**: Uses jq (faster) or Python (fallback)
- **Linux**: Uses jq if available, Python otherwise
- **Windows**: Works with WSL or Git Bash

The tool automatically detects the best available JSON processor and shows which one it's using.
