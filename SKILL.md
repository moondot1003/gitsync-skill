---
name: gitsync
description: Sync project work between computers through GitHub using plain-language requests. Use for GitSync requests to log in, configure Git identity, connect or clone repositories, compare local work with GitHub, preview and approve uploads, or download the latest changes.
---

# GitSync

Help the user move work between computers through their GitHub repository. Recognize GitSync as the friendly name; the skill invocation is `$gitsync`. Translate requests into Git operations and explain results in plain language.

## Establish the context

Inspect the working directory, repository root, Git status (including staged and untracked files), current branch, remotes, and upstream before choosing an operation. Preserve local work and existing Git configuration. Ask one focused question at a time when the repository, destination, branch, or intended action is unclear. Inspect remote URLs without displaying embedded credentials.

Use the connected repository and current branch when unambiguous. Never assume the repository is named origin, the branch is main, or every computer has the same folders. A skill installation does not copy the project or authenticate GitHub.

Use Git for existing repositories. Use GitHub CLI (`gh`) for creating GitHub repositories when available. If tools or authentication are missing, explain the requirement and guide the user through the appropriate installation or interactive sign-in; perform authorized setup where permissions allow. Keep credentials out of conversation, tracked files, remote URLs, and command history. Respect execution approval requirements.

## Guided login and project setup

Treat "help me log in to Git and connect my project" as a request to perform setup, not merely supply terminal commands. Git commit identity and GitHub authentication are separate; configure and verify both.

1. Inspect installed Git/GitHub CLI, existing identity, authentication status, and project state without revealing credentials. Before choosing or changing an account, ask which GitHub username the user wants to connect, even if an account is already logged in. Ask one question at a time and wait for each answer.
2. Obtain the desired commit display name and email individually when not already supplied or confirmed. Explain that commit email is stored in commit history and offer a GitHub noreply address if privacy matters; use the exact address provided or confirmed rather than constructing one. Ask only for public account/identity details in chat.
3. Run the setup commands yourself within authorization and environment permissions. For a project setup, use repository-local `git config user.name` and `git config user.email` once a repository exists. Change global identity only when the user requests it. If Git or `gh` is absent, install it using the environment's supported method when authorized and permitted; otherwise identify the exact remaining setup action.
4. Preserve a working SSH connection when it belongs to the chosen account. For a new connection, prefer `gh auth login --hostname github.com --git-protocol https --web` and its supported Git credential setup. The user completes browser authorization and two-factor authentication themselves. If the chosen account differs from the active account, ask before switching it. Where an interactive login cannot run through the available tools, give the single necessary terminal/browser action, then resume automation after it completes.
5. Never request passwords, personal access tokens, private SSH keys, or key passphrases in chat. If token authentication is necessary, have the user enter it directly through the local credential/login prompt. For SSH, use an existing local key or guide local generation and public-key registration; keep the private key and passphrase on the computer and preserve existing keys. Do not automatically publish or copy credentials.
6. Verify the authenticated account and repository access without printing tokens (for example, use `gh auth status` and `git ls-remote` as appropriate). A configured username/email alone does not prove authentication. Report failures accurately and continue only after access is established.
7. Ask whether to connect an existing GitHub repository or create a new one when unclear, then collect only the missing repository details one question at a time. Follow Connect or clone below. Confirm the selected account, identity scope, repository, branch, and verified access. Preserve the upload preview/approval requirement before any commit or push.

Example opening question: "Which GitHub username should this project connect to?" After the answer, obtain the missing commit identity and complete authentication through the browser rather than collecting secrets in conversation.

## Connect or clone

For a request to connect to an existing GitHub repository, obtain its URL or owner/name if it cannot be discovered. Determine whether the current folder is already the matching checkout.

- If it is the matching repository, keep the connection and proceed with the requested comparison or download.
- If there is no checkout, clone into an empty or new destination agreed with the user. Do not clone over an existing populated folder.
- If local project files need connecting, inspect Git state and the remote's history before initializing Git or adding a remote. Explain whether histories are related before suggesting integration.
- If a different repository is already connected, present the difference and ask before replacing its URL or switching the project to another repository.

For a new GitHub repository, ask for any missing owner, repository name, and public/private visibility. Creating a repository requires an explicit creation request; merely connecting does not authorize creation. Avoid creating an initial remote commit when uploading an existing local history. Connecting or creating alone does not authorize committing and pushing files.

## Compare workspace with GitHub

Fetch the selected remote before reporting its current state; fetching updates remote references without merging project files. If fetching fails, label any comparison as stale and report the reason.

Report:

- Repository, branch, and upstream, including whether the local or remote branch does not yet exist.
- Uncommitted files, including staged, unstaged, and untracked paths with added/modified/deleted status.
- Local-only and remote-only commits, with short hashes, subjects, and timezone-qualified commit dates.
- Files changed on each side since the merge base, plus a concise diff summary when useful.

Distinguish uncommitted changes from committed differences. Name whether a displayed date is the author date or commit date; do not call it an upload time. Git records commit timestamps, not GitHub push timestamps or dependable per-file edit times. If histories are unrelated, report that explicitly rather than inventing a merge base.

A comparison request permits fetching and reading; it does not authorize staging, committing, merging, or pushing.

## Upload: preview, approval, commit, push

1. Inspect status, relevant diffs, ignore rules, remote, and branch. Show the proposed included paths and any exclusions, proposed commit message, and push destination. Include already staged files in the review. Identify archives, generated artifacts, large files, or likely credentials when they occur; ask about uncertain inclusion rather than silently including them. Respect ignore rules and never force-add ignored files. Stop to resolve exposed credentials before upload.
2. Wait for explicit approval of this concrete file list and destination before staging, committing, or pushing. A general request to save to GitHub starts the preview; it is not approval to skip it. If there are no file changes but there are unpushed commits, preview those commits and the destination for approval.
3. Before acting on approval, recheck the reviewed changes and destination. If they materially changed, refresh the preview and obtain approval for the revised scope. Stage only approved paths, preserve unrelated staged work, and verify the proposed commit contains only approved changes. If isolating the commit would disturb other staged work, stop and resolve that scope with the user.
4. Commit with the approved message when necessary, then push the approved branch. Set an upstream when the destination is agreed and none exists. Never push all branches or tags by default. If push is rejected due to remote changes, fetch and show the divergence; do not retry indefinitely or force-push. If commit succeeds but push fails, report the local commit hash and the failure separately.
5. Report the committed/pushed branch and commit hash, along with any changes left local. Verify success from command results rather than assuming a commit means the push succeeded.

## Download the latest work

A request to download the latest changes authorizes cloning into the agreed destination or updating the selected checkout. Show incoming file changes and remote commit dates before applying them. If the user only requested a comparison, stop after showing it.

- For a clean branch with related history, use a fast-forward-only pull/update. If already current, say so. Local commits that are simply ahead do not need downloading.
- If local modifications or untracked files exist, show them and ask how to preserve them before updating. Do not silently stash, discard, or commit them.
- If histories diverge or are unrelated, explain the situation and ask before any merge, rebase, or replacement. Never automatically reset, clean, force-push, or overwrite local work.
- After a successful update, report the resulting branch/commit, changed file summary, and remaining local changes. Downloading source does not automatically install dependencies, copy ignored secrets, or run project code; perform additional setup only within the user's requested scope.

## Two-computer example

Laptop: `$gitsync Please save my project to GitHub.` Review the upload preview and wait for approval before committing and pushing.

Desktop: `$gitsync Connect to OWNER/REPO, show changed files and the latest commit dates, and download the latest changes.` Inspect the destination, clone if absent, or fetch and compare an existing checkout. Apply the requested update when it can preserve local work; ask about conflicts or missing choices.
