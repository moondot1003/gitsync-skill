# GitSync

A Codex skill for syncing projects between computers through GitHub using plain language.

Ask Codex to connect a repository, guide GitHub sign-in, compare changes, upload your work, or download updates. Before an upload, GitSync shows the proposed files, commit message, and destination and waits for your approval.

## Install in Codex

Ask Codex's built-in skill installer:

```text
$skill-installer Install https://github.com/moondot1003/gitsync-skill/tree/main using the skill name gitsync.
```

Alternatively, clone this repository into your personal skills folder. On Linux or WSL with the default Codex home:

```bash
git clone https://github.com/moondot1003/gitsync-skill.git ~/.codex/skills/gitsync
```

If you use a custom CODEX_HOME, install into its skills/gitsync directory instead. On Windows, use the equivalent skills directory under your Codex home. Preserve any existing gitsync installation rather than cloning over it. The skill's entrypoint is SKILL.md; README.md is the user manual.

Use the skill on your next turn after installation. If it does not appear, start a new Codex session. Install it separately on each computer where you want to use it.

## First-time setup

Git must be available. GitHub CLI (`gh`) supports guided browser login and repository creation. GitSync checks for missing tools and helps with setup where the environment permits it.

```text
$gitsync Help me log in to GitHub and connect my new project.
```

GitSync asks which GitHub account to use, obtains your commit name and email one question at a time, and configures the project. You complete browser authentication yourself. Commit identity and GitHub sign-in are separate. A GitHub noreply email can keep your personal email out of public commit history.

Enter passwords, tokens, and private-key passphrases only through the appropriate local or browser authentication prompt, never in chat. Authentication is configured independently on each computer.

## Everyday use

### Upload work

```text
$gitsync Save my project to GitHub.
```

Review the changed files, proposed commit message, and destination. Approve the preview to let Codex commit and push those changes. GitSync reports whether the commit and push succeeded separately.

### Compare before deciding

```text
$gitsync Compare this workspace with the connected GitHub repository. Show changed files and latest commit dates.
```

GitSync fetches remote information, lists uncommitted files, and reports local and remote commits. Comparison alone does not commit, push, or merge changes. Dates are Git commit dates, not upload times.

### Continue on another computer

On the first computer, upload your work and confirm the push succeeded. On the second computer, install this skill and ask:

```text
$gitsync Connect to OWNER/REPO, show changed files and latest commit dates, then download the latest changes.
```

Replace OWNER/REPO with your repository. GitSync clones into an agreed new or empty folder if needed, or updates an existing checkout. It asks before proceeding when local changes or diverging history need a decision. It does not silently discard local work.

### Create a repository

```text
$gitsync Create a private GitHub repository for this project and connect it.
```

GitSync asks for missing account, repository name, and visibility details. Connecting or creating a repository is separate from approving a file upload.

## Limits

- This is an instruction skill for Codex, not a background sync service or standalone executable.
- Codex needs available tools, network access, authentication, and the required environment permissions. It may need approval or a manual browser step.
- Ignored files such as .env, installed dependencies, and machine configuration do not transfer automatically through GitHub.
- Conflicts and unrelated histories require a user decision. GitSync does not automatically force-push, reset, or overwrite local work.
- Actual behavior depends on the Codex agent and environment; the skill supplies a workflow rather than enforcing Git operations in executable code.

## Repository contents

- SKILL.md: instructions Codex reads when using GitSync.
- README.md: installation and usage guide.
