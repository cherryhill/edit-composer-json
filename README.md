# 🍒 edit-composer-json

tool that can be expanded for updating `composer.json` with one-liners

it currently can add items to the `repositories` array and to the `scripts` object

we use it like:

```sh
ddev exec 'bash -c "$(curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/insert)" repository cherryhill/example-custom-project'
```

to get:

```json
{
    "repositories": [
        {
            "type": "github",
            "url": "https://github.com/cherryhill/example-custom-project"
        }
    ]
}
```

and:

```sh
ddev exec 'bash -c "$(curl -fsSL https://raw.githubusercontent.com/cherryhill/edit-composer-json/main/insert)" script post-update-cmd ./vendor/cherryhill/example-custom-project/executable-command'
```

to get:

```json
{
    "scripts": {
        "post-update-cmd": "./vendor/cherryhill/example-custom-project/executable-command"
    }
}
```
