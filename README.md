# runpod-setup

Setup notes for preparing a RunPod environment with Git, GitHub CLI, Codex, Node.js, custom Codex settings, Google Drive access through `rclone`, `uv`, and LaTeX.

## Contents

- `setup.txt`: the original command checklist
- `README.md`: cleaned up setup instructions

## Git Setup

Set up SSH agent forwarding in the IDE, then configure the global Git identity:

```bash
git config --global user.name "BurnyCoder"
git config --global user.email "happymancz@email.cz"
```

## GitHub CLI Setup

Authenticate GitHub CLI:

```bash
gh auth login
```

## Codex Setup

Install the base dependencies:

```bash
apt install bubblewrap
apt-get install -y build-essential libpoppler-cpp-dev pkg-config python3-dev
pip install pdftotext
apt-get install ripgrep
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt-get install -y nodejs
```

Then choose one Codex installation method.

### Option 1: Install the Codex binary

```bash
curl -fsSL https://github.com/openai/codex/releases/latest/download/codex-linux-x64.tar.gz -o /tmp/codex.tgz
tar -xzf /tmp/codex.tgz -C /usr/local/bin
mv /usr/local/bin/codex-x86_64-unknown-linux-musl /usr/local/bin/codex 2>/dev/null || true
```

### Option 2: Install Codex through npm

```bash
npm install -g @openai/codex@latest
```

Start Codex:

```bash
codex
```

Use these Codex settings:

`https://github.com/BurnyCoder/codex-settings`

## Google Drive Setup

Install `rclone`:

```bash
apt-get install -y unzip
curl https://rclone.org/install.sh | bash
```

Run the interactive setup:

```bash
rclone config
```

Use the following sequence in the prompts:

```text
n
gdrive
24
Enter
Enter
1
Enter
Enter
n
Enter
Enter
q
```

## uv Setup

Install `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## LaTeX Setup

Install TeX Live non-interactively with a smaller scheme, then add `latexmk` and `biber`:

```bash
cd /tmp
curl -L -o install-tl-unx.tar.gz https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
zcat < install-tl-unx.tar.gz | tar xf -
cd install-tl-2*
perl ./install-tl --no-interaction --paper=letter --scheme=small --no-doc-install --no-src-install
export PATH="/usr/local/texlive/2026/bin/x86_64-linux:$PATH"
tlmgr install latexmk biber
```

## Setup Codex

Initialize the project instructions:

```bash
codex
/init
```

Add more of the repository structure and README information into `AGENTS.md`.
