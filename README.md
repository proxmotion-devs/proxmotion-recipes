# Proxmotion app packages

The App Marketplace apps of [Proxmotion](https://github.com/proxmotion-devs/proxmotion):
OpenVPN, WireGuard, Docker, WordPress and the other apps a panel can install on a VPS,
each as a **`.proxapp` package**.

## Installing an app in a panel

1. Download `<slug>/<slug>.proxapp` of the app.
2. In the panel, open **Apps → Install package** and choose the file.
3. The panel shows what the package changes (for an update, against the version installed), then installs it.

A newer package of an installed app updates it; servers keep the version they were installed with.

## Layout

```
<slug>/
  <slug>.proxapp   # the app's package, and nothing else
```

A package is a zip of one recipe directory:

```
<slug>/
  recipe.yaml      # the recipe (format 2, see panel/recipes/_schema in the panel)
  install.sh       # or .ps1 for Windows recipes
  uninstall.sh, upgrade.sh, actions/*.sh   # as the recipe declares them
  README.md        # for administrators and providers
```

The shared helper and runner scripts (`_lib`) and the JSON Schemas (`_schema`)
stay in the panel repository, because the panel writes them into every server
itself.

## Rules

- Every script starts with `set -euo pipefail` (Linux) or the Windows
  equivalent, and is idempotent.
- Inputs are never interpolated into scripts; they arrive as `inputs.json`.
- A change to a recipe's `recipe.yaml` or any script it uses needs a **higher
  `version`**. Installed versions never change, so a changed version with the
  same number is refused. CI compares a pull request's packages with its base.
- An app folder holds only its package; CI refuses anything else in it.

## Changing an app

Unpack the package into a working directory outside this repository, edit it,
raise its version and pack it again with a Proxmotion checkout:

```bash
unzip wireguard/wireguard.proxapp -d ~/recipe-sources
# edit ~/recipe-sources/wireguard, raise version in recipe.yaml
cd proxmotion/panel
composer install
php artisan apps:pack --path=~/recipe-sources --out=../../proxmotion-recipes
php artisan apps:validate --path=../../proxmotion-recipes
```

`apps:pack` packs every recipe of `--path` into `<out>/<slug>/<slug>.proxapp`,
and leaves a package whose content did not change as it is.
`php artisan apps:make <slug> --path=~/recipe-sources` creates the sources of a new app.

PowerShell scripts keep their CRLF line endings inside a package; edit them
with an editor that does not convert them.

## Licence

AGPL-3.0, see [LICENSE](LICENSE).
