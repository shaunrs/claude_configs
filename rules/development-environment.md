# Development Environment

## New Project Setup

- **Add standard scripts** - Always add common build, test, and linting scripts to the appropriate shortcut command (e.g., `scripts` in package.json for Node projects).
- **Use package manager commands for dependencies** - Do not manually edit project configuration. Use `pnpm add`, `flutter pub add`, `cargo add`, etc. to install the latest version.
- **Use git for file moves** - Always use `git mv` to ensure git history is preserved.

## Standard .gitignore

For any new project, add a `.gitignore` with at minimum:

```
# Claude
.claude
.specstory

# macOS
.DS_Store
*.pem
```

## Local Development

- **Dev containers** - Use dev containers for consistent environments across machines when the project supports it.
- **Environment consistency** - Local development setup should mirror CI as closely as possible to catch issues early.
