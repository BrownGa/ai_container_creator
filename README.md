# AI Container Creator

A single-file tool that sets up an isolated AI development sandbox in any project directory, with [Claude Code](https://claude.ai/code) and [OpenAI Codex](https://github.com/openai/codex) pre-installed and ready to use.

## What it does

Running `ai_container_creator` in a project directory creates:

- `.devcontainer/` — VS Code devcontainer config (Dockerfile + devcontainer.json)
- `open-terminal.sh` — opens a shell inside the container (builds on first run)
- `container.sh` — interactive menu to start/stop/rebuild/inspect the container

The container runs as a non-root `sandbox` user with your project mounted at `/workspace`. Your `~/.claude`, `~/.codex`, and SSH key are mounted in automatically so Claude Code and Codex work without extra setup.

## Requirements

- Docker
- Python 3
- An SSH key at `~/.ssh/ai_container_id_ed25519` registered with GitHub (for git operations inside the container)

## Installation

Copy `ai_container_creator` to somewhere on your PATH, e.g.:

```bash
cp ai_container_creator ~/.local/bin/ai_container_creator
chmod +x ~/.local/bin/ai_container_creator
```

## Usage

```bash
cd /path/to/your/project
ai_container_creator
```

Then use one of the generated scripts:

```bash
./open-terminal.sh        # open a shell (builds image on first run)
./container.sh            # interactive menu
./container.sh start      # start the container
./container.sh stop       # stop the container
./container.sh shell      # open a shell in a running container
./container.sh rebuild    # rebuild the Docker image from scratch
./container.sh status     # show container and image state
./container.sh logs       # tail container logs
```

Or open the project in VS Code and choose **"Reopen in Container"**.

To tear everything down:

```bash
ai_container_creator --remove
```

This removes the container, image, and all generated files from the project directory.

## Inside the container

Both `claude` and `codex` are available with permissions/approvals bypassed automatically:

```bash
claude    # Claude Code
codex     # OpenAI Codex
```

When using the shell scripts, up to 3 free ports in the range `3000–3030` are forwarded to the host. When using VS Code devcontainer, all ports in that range are forwarded.

## SSH key setup

The container expects a dedicated SSH key (kept separate from your main key) at `~/.ssh/ai_container_id_ed25519`. To create it:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/ai_container_id_ed25519 -C "ai-container"
```

Then add the public key (`~/.ssh/ai_container_id_ed25519.pub`) to your GitHub account under **Settings → SSH and GPG keys**.

## Environment variables

`ANTHROPIC_API_KEY` and `OPENAI_API_KEY` are forwarded from your host environment into the container if set, but neither is required — credentials are already available inside the container via the mounted `~/.claude` and `~/.codex` directories.
