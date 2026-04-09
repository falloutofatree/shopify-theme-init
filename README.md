# shopify-theme-init

Pragmatic workflow for developing and maintaining stores built on off-the-shelf Shopify themes:

* Keep theme code in version control
* Pull in Shopify Admin code/template changes (including Theme Store updates)
* Exclude content and settings from Git

**v1.1.0**

---

## 🧹 Install

### 1. Install a theme via Shopify Admin

Choose a theme from the Shopify Theme Store and install it.

### 2. Install Shopify CLI and log in

Install [Shopify CLI](https://shopify.dev/docs/themes/tools/cli):

```bash
npm install -g @shopify/cli@latest
```

Now log in (this opens your browser so you can sign in the usual way):

```bash
shopify auth login
```

### 3. Create a theme repo

Create a new repository on GitHub for the theme (e.g. `my-store-theme`).

### 4. Clone the workflow scaffolding

```bash
git clone https://github.com/falloutofatree/shopify-theme-init.git
mv shopify-theme-init my-store-theme
cd my-store-theme
git remote set-url origin git@github.com:my-org/my-store-theme.git
```
This gives you `.gitignore`, `.shopifyignore`, `shopify-theme.toml`, and this README.

### 5. Configure the local environment

In `shopify.theme.toml`, fill in the `store` and `theme` lines and uncomment them:

```toml
[environments.default]
store = "my-store.myshopify.com"
theme = "my-theme-id"
```

⚠️ Double-check your theme ID — you don't want to make changes to the wrong theme.

### 6. Pull theme files, commit, and push

```bash
shopify theme pull
git add .
git commit -m "chore: initial theme pull"
git push -u origin main
```

You're ready to develop.

---

## 🧑‍💻 Usage

```bash
shopify theme pull
git diff                        # Inspect any admin-side changes
# Make your changes
# Commit and push your changes to git
shopify theme push
```

---

## 🔄 Shopify Admin Updates

Here's how to deal with theme edits that happen directly in the Shopify Admin:

* **Admin-side template edits**
  To check for edits to templates, code, or settings in the Shopify Admin affecting the codebase:
  Run `shopify theme pull`, then `git diff`, and inspect what changed.
  Review, commit, and push.

* **Admin-side theme version updates (applies only to themes installed from the Theme Store)**
  When someone clicks Update in the Shopify Admin, Shopify creates a new copy of the theme with the updates applied. This new theme has a different ID, so you'll need to update `shopify.theme.toml` to point to it.

  ```bash
  shopify theme list                          # Find the new theme ID
  vi shopify.theme.toml                       # Update theme ID
  shopify theme pull
  git diff                                    # Inspect everything the update changed
  git add .
  git commit -m "chore: theme update vX.X.X"
  ```

  ⚠️ Commit the raw update before making any fixes.

  Even if Shopify's update tool reports success, assume things broke. Check for:
  - visual regressions
  - broken sections/blocks
  - missing or reset theme settings
  - custom code that was overwritten or refactored out
  
  Resolve any issues in separate commits. When ready, set the theme as live!

  💡 If a theme update turns out to be too destructive, you can always republish the previous theme and apply the update manually.

---

## ⚠️ (Optional) Track settings

`config/settings_data.json` is ignored by default because settings change frequently in the Admin, causing noise and conflicts. If you must track it, comment it out in `.gitignore`.
