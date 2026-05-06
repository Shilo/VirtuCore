# VirtuCore
VirtuCade Shared foundation addon for related Godot plugins, providing reusable scripts, editor plugin base classes, utilities, and common assets used across the Virtu plugin suite.

## 🔧 Maintainer: publish the addon branch

The public subtree branch is always named `addon`. After changing files under `addons/virtucore` on `main`, the GitHub workflow publishes that directory as the root of `addon` automatically.

To create or repair the branch manually from the VirtuCore repo root, publish the addon directory tree with `git commit-tree`:

```powershell
$addonDir = "addons/virtucore"
git fetch origin "+refs/heads/addon:refs/remotes/origin/addon" 2>$null
$addonTree = git rev-parse "main:$addonDir"
$currentTree = git rev-parse "origin/addon^{tree}" 2>$null

if ($LASTEXITCODE -eq 0 -and $addonTree -eq $currentTree) {
  "addon branch already up to date"
} else {
  $parent = git rev-parse --verify origin/addon 2>$null
  if ($LASTEXITCODE -eq 0) {
    $newCommit = git commit-tree $addonTree -p $parent -m "chore: sync addon branch from $(git rev-parse --short main)"
  } else {
    $newCommit = git commit-tree $addonTree -m "chore: sync addon branch from $(git rev-parse --short main)"
  }
  git push origin "${newCommit}:refs/heads/addon"
}
```

The `addon` branch contains only the files that belong inside a dependent project's `addons/virtucore` directory. It is a generated one-way publish branch, so make source changes under `addons/virtucore` on `main` instead of editing `addon` directly.

The `.github/workflows/sync-addon-branch.yml` workflow uses the same `git commit-tree` publish flow whenever `main` receives changes under `addons/virtucore`.

## Using VirtuCore as a subtree dependency

Dependent Godot projects should keep these shared files at:

```text
addons/virtucore
```

Git subtree is useful here because the dependent repo gets real committed files instead of a submodule pointer. That means the project still opens normally in Godot and does not require an extra clone step.

This repository is a full Godot demo project. The reusable addon files live in `addons/virtucore`, so subtree consumers should use the generated `addon` branch.

### Initialize the subtree

From the root of the repo that depends on VirtuCore:

```powershell
git subtree add --prefix=addons/virtucore https://github.com/Shilo/VirtuCore.git addon --squash
```

This adds the shared VirtuCore files into `addons/virtucore` and records enough subtree history for future updates.

### Update to the latest VirtuCore commit

From the dependent repo root:

```powershell
git subtree pull --prefix=addons/virtucore https://github.com/Shilo/VirtuCore.git addon --squash
```

If Git reports conflicts, resolve them like a normal merge, then commit the result.

## VS Code task for updating without typing the CLI command

In any dependent repo, create `.vscode/tasks.json` with this task:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Update VirtuCore subtree",
      "type": "shell",
      "command": "git",
      "args": [
        "subtree",
        "pull",
        "--prefix=addons/virtucore",
        "https://github.com/Shilo/VirtuCore.git",
        "addon",
        "--squash"
      ],
      "problemMatcher": []
    }
  ]
}
```

Then run it from VS Code:

1. Open the Command Palette with `Ctrl+Shift+P`.
2. Choose `Tasks: Run Task`.
3. Choose `Update VirtuCore subtree`.

Optional keyboard shortcut in VS Code `keybindings.json`:

```json
{
  "key": "ctrl+alt+u",
  "command": "workbench.action.tasks.runTask",
  "args": "Update VirtuCore subtree"
}
```

The task still runs Git under the hood, but you can trigger it from VS Code without retyping the subtree command.

## 📦 Dependencies

None.
