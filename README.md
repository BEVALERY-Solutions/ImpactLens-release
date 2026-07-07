# ImpactLens

ImpactLens is a VS Code and Kodik extension for local code impact analysis.

It helps reviewers understand which files, routes, code symbols, and tests may
be affected by current changes or pull requests. The analysis runs locally
inside the editor workspace.

## Download

Download the latest VSIX package from GitHub Releases:

https://github.com/BEVALERY-Solutions/impactlens-release/releases/latest

Use the asset named `impactlens-vscode-0.1.0.vsix`.

## Install in VS Code

1. Download `impactlens-vscode-0.1.0.vsix` from the latest release.
2. Open VS Code.
3. Open the Extensions view.
4. Click `...`.
5. Choose `Install from VSIX...`.
6. Select the downloaded `.vsix` file.

Or install from a terminal:

```powershell
code --install-extension .\impactlens-vscode-0.1.0.vsix --force
```

## Install in Kodik

1. Download `impactlens-vscode-0.1.0.vsix` from the latest release.
2. Open Kodik.
3. Open the Extensions view.
4. Choose `Install from VSIX...`.
5. Select the downloaded `.vsix` file.

If the `kodik` command is available in your terminal:

```powershell
kodik --install-extension .\impactlens-vscode-0.1.0.vsix --force
```

## Usage

1. Open a source code workspace in VS Code or Kodik.
2. Open the ImpactLens icon in the Activity Bar.
3. Run `ImpactLens: Check Setup`.
4. Use `Current Changes` to analyze local Git changes.
5. Use `Pull Requests` to inspect and analyze available PRs.
6. Review affected files, routes, code symbols, suggested tests, risk, and
   optional AI suggestions.

## Notes

- Python and FastAPI projects have the richest analysis support.
- TypeScript, JavaScript, Go, Java, C#, and C/C++ are supported at MVP level.
- AI suggestions are optional and require an OpenAI-compatible provider in
  extension settings.
- The extension analyzes the current workspace locally and does not require a
  hosted backend.
