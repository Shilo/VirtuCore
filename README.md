# VirtuCore
VirtuCade Shared foundation addon for related Godot plugins, providing reusable scripts, editor plugin base classes, utilities, and common assets used across the Virtu plugin suite.

## Maintainer: create the addon split branch

The public subtree branch is always named `addon`. After changing files under `addons/virtucore` on `main`, refresh and push the split branch from the VirtuCore repo root:

```powershell
git subtree split --prefix=addons/virtucore main --branch addon
git push origin addon
```

The `addon` branch contains only the files that belong inside a dependent project's `addons/virtucore` directory.

## Using VirtuCore as a subtree dependency

Dependent Godot projects should keep these shared files at:

```text
addons/virtucore
```

Git subtree is useful here because the dependent repo gets real committed files instead of a submodule pointer. That means the project still opens normally in Godot and does not require an extra clone step.

This repository is a full Godot demo project. The reusable addon files live in `addons/virtucore`, so subtree consumers should use the generated `addon` split branch.

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
