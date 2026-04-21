# Git Guideline

This document defines standardized Git usage practices for all projects. It ensures consistent
workflows, clean commit history, secure collaboration, and efficient code management across teams
and environments.

### .gitignore File

The `.gitignore` file defines which files and directories Git should not track.
It helps keep the repository clean, reduces clutter, and prevents sensitive or unnecessary files
from being committed.

Example:

```
/build/
*.log
*.tmp
```

**Rules**

- Always include a .gitignore in every repository (root and submodules if applicable).
- Never commit build artifacts, dependencies, secrets, or IDE-specific configurations.
- Keep .gitignore updated as new tools or frameworks are added.
- For shared projects, use a team-approved global template (e.g., global.gitignore).

### Branching Strategy

Follow a feature-based branching model for consistent workflows.

| Branch Type | Naming Convention        | Description                             |
|-------------|--------------------------|-----------------------------------------|
| `main`      | –                        | Stable, production-ready code only      |
| `develop`   | –                        | Integration branch for upcoming release |
| `feature/*` | `feature/login-ui`       | New features or enhancements            |
| `bugfix/*`  | `bugfix/fix-login-error` | Non-critical bug fixes                  |
| `hotfix/*`  | `hotfix/payment-crash`   | Critical production fixes               |
| `release/*` | `release/v1.2.0`         | Pre-release stabilization branch        |

**Rules**

- Never commit directly to main.
- Always create a new branch from develop (or main if develop doesn’t exist).
- Delete feature branches after merging.
- Keep branch names short, descriptive, and lowercase.

### Commit Guidelines

Write small, atomic commits with clear messages.
Format

```
type(scope): short summary

[optional body: details, rationale, or references]
```

**Common Types**

| Type     | Meaning                                    |
|----------|--------------------------------------------|
| feat     | New feature                                |
| fix      | Bug fix                                    |
| refactor | Code restructuring without behavior change |
| docs     | Documentation only changes                 |
| style    | Formatting, missing semicolons, etc.       |
| test     | Adding or fixing tests                     |
| chore    | Maintenance tasks, build scripts, etc.     |

Example

```
feat(auth): add JWT authentication for user login

- Update database schema
- Create login API endpoints
- ...
```

**Rules**

- Use imperative mood: “add feature” not “added feature.”
- Commit logically grouped changes (avoid “big bang” commits).
- Review changes before committing (git diff).
- Do not include temporary files.
- Make one change at a time
- Don't commit broken code (if need then use `git stash`)

### Security & Safety

- Use SSH keys or PATs (Personal Access Tokens) instead of passwords.
- Always enable 2FA (Two-Factor Authentication) on Git hosting platforms.
- Never use git push --force on shared branches — use --force-with-lease if required.
- Backup important repositories or mirror to another remote.
- Use signed commits if security and authenticity are critical.