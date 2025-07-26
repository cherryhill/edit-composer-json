# 🍒 edit-composer-json

A simple tool for updating your `composer.json` with one-liners.

Currently, it can add items to the `repositories` array and to the `scripts` object.

**Requirements:**
- [`jq`](https://jqlang.org/download/) must be installed.
- Only GitHub repositories are supported.

## Usage

### Add a repository

```sh
curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/3.x/repositories | bash -s vendor/package-name
```

Or with a custom composer.json path:

```sh
curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/3.x/repositories | bash -s -f /path/to/composer.json vendor/package-name
```

This will add:

```json
{
    "repositories": [
        {
            "type": "github",
            "url": "https://github.com/vendor/package-name"
        }
    ]
}
```

---

### Add a script

```sh
curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/3.x/scripts | bash -s -s post-update-cmd:./vendor/cherryhill/example-custom-project/executable-command
```

Or with a custom composer.json path:

```sh
curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/3.x/scripts | bash -s -f /path/to/composer.json -s post-update-cmd:./vendor/cherryhill/example-custom-project/executable-command
```

This will add:

```json
{
    "scripts": {
        "post-update-cmd": "./vendor/cherryhill/example-custom-project/executable-command"
    }
}
```

---

**Note:**
- The scripts assume your `composer.json` is in the repository root unless you specify a path with `-f`.
- No support for remote fetching, branches, or non-GitHub repositories.
