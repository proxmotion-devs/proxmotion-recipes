# Proxmotion app recipes

The App Marketplace recipes of [Proxmotion](https://github.com/proxmotion-devs/proxmotion):
OpenVPN, WireGuard, Docker, WordPress and the other apps a panel can install on
a VPS. The panel downloads them from this repository in its **App Store**
(administrators only); nothing here runs until an administrator downloads an app.

## Layout

Each directory is one app:

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
  same number is refused.
- Lint before you open a pull request: `shellcheck` on the shell scripts and
  the panel's validation (below).

## Checking a recipe locally

With a Proxmotion checkout next to this one:

```bash
cd proxmotion/panel
composer install
php artisan apps:validate --path=../../proxmotion-recipes
php artisan apps:check-versions --base-path=../../recipes-base --path=../../proxmotion-recipes
```

`apps:make --path=<this repository> <slug>` creates a skeleton for a new app.

## Licence

AGPL-3.0, see [LICENSE](LICENSE).
