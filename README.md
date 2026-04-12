# runpod-setup

Setup notes for preparing a RunPod environment with Git, GitHub CLI, Codex, Node.js, custom Codex settings, Google Drive access through `rclone`, `uv`, and LaTeX.

## Contents

- `setup.txt`: the original command checklist
- `README.md`: cleaned up setup instructions

## RunPod Setup

Before the rest of the environment setup:

- deploy the RunPod instance
- connect to the IDE
- attach a 200 GB network volume

## Git Setup

Set up SSH agent forwarding in the IDE, then configure the global Git identity:

```bash
git config --global user.name "BurnyCoder"
git config --global user.email "happymancz@email.cz"
```

## GitHub CLI Setup

Install GitHub CLI:

```bash
(type -p wget >/dev/null || (apt update && apt install wget -y)) \
  && mkdir -p -m 755 /etc/apt/keyrings \
  && out=$(mktemp) && wget -nv -O"$out" https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  && cat "$out" | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && mkdir -p -m 755 /etc/apt/sources.list.d \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && apt update \
  && apt install gh -y
```

Export a GitHub token for shell sessions:

```bash
echo 'export GH_TOKEN="ghp_your_token_here"' >> ~/.bashrc
source ~/.bashrc
```

## Codex Setup

Install the base dependencies and tools Codex commonly finds useful:

```bash
apt install bubblewrap
apt-get install -y build-essential libpoppler-cpp-dev pkg-config python3-dev
pip install pdftotext pytest
apt-get install ripgrep
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt-get install -y nodejs
npm install -g @openai/codex@latest
```

Apply the Codex configuration based on `https://github.com/BurnyCoder/codex-settings`:

```bash
mkdir -p /root/.codex/rules /root/.codex/backups

if [ -f /root/.codex/config.toml ]; then
  cp /root/.codex/config.toml "/root/.codex/backups/config.toml.pre-burnycoder-$(date +%Y%m%d-%H%M%S)"
fi

cat > /root/.codex/config.toml <<'EOF'
model = "gpt-5.4"
model_reasoning_effort = "xhigh"
approval_policy = "never"
sandbox_mode = "danger-full-access"

[features]
multi_agent = true

[plugins."github@openai-curated"]
enabled = true

[projects."/workspace/physics-foundation-model-activation-steering"]
trust_level = "trusted"

[projects."/root"]
trust_level = "trusted"
EOF

cat > /root/.codex/rules/default.rules <<'EOF'
# Block direct rm invocations, even when commands otherwise auto-run.
prefix_rule(
    pattern = ["rm"],
    decision = "forbidden",
    justification = "Do not use rm. Remove files manually after review or use a safer alternative.",
    match = [
        "rm file.txt",
        "rm -rf build",
    ],
    not_match = [
        "rmdir empty-dir",
    ],
)

prefix_rule(
    pattern = ["/bin/rm"],
    decision = "forbidden",
    justification = "Do not use rm. Remove files manually after review or use a safer alternative.",
    match = [
        "/bin/rm file.txt",
        "/bin/rm -rf build",
    ],
)

prefix_rule(
    pattern = ["sudo", "rm"],
    decision = "forbidden",
    justification = "Do not use rm. Remove files manually after review or use a safer alternative.",
    match = [
        "sudo rm file.txt",
        "sudo rm -rf build",
    ],
)

prefix_rule(
    pattern = ["sudo", "/bin/rm"],
    decision = "forbidden",
    justification = "Do not use rm. Remove files manually after review or use a safer alternative.",
    match = [
        "sudo /bin/rm file.txt",
        "sudo /bin/rm -rf build",
    ],
)
EOF
```

## Google Drive Setup

Install `rclone` and create a Google Drive remote:

```bash
apt-get install -y unzip && curl https://rclone.org/install.sh | bash && rclone config create googledrive drive scope=drive
```

## uv Setup

Install `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Then set up a working Python `uv` virtual environment with the required dependencies.

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
