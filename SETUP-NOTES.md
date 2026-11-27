# Environment Setup Notes — Tech42

## 1. Ordered List of Installations & Exact Commands

### Step 1: WSL2 with Ubuntu 24.04
Installed a clean, isolated Ubuntu 24.04 instance for the program, set it as default, and updated all system packages:

```powershell
wsl --install -d Ubuntu-24.04
wsl --set-default Ubuntu-24.04
```

Inside Ubuntu 24.04:

```bash
 sudo apt update && sudo apt upgrade -y
uname -a
# Output: Linux tech42 6.18.58.1-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Tue Oct 27 00:24:11 UTC 2026 x86_64 x86_64 x86_64 GNU/Linu
cat /etc/os-release
# Output: PRETTY_NAME="Ubuntu 24.04.5 LTS"
```

### Step 2: Git (Windows & WSL)
Verified Git availability on both environments:
- **Windows (PowerShell):** `git --version` -> `git version 2.55.0.windows.3`
- **WSL (Ubuntu 24.04):** `git --version` -> `git version 2.43.0`

**Which Git I will actually use and why:**
I will exclusively use **Git inside WSL (Ubuntu 24.04)** because all project files, Python virtual environments, and tooling live inside the Linux filesystem (`~/tech42`). Using WSL Git avoids cross-OS file permission issues, prevents CRLF/LF line-ending conflicts, and offers significantly faster I/O performance than accessing `/mnt/c`.

### Step 3: Python 3.11 & Virtual Environment
Added the `deadsnakes` PPA to install Python 3.11 alongside Ubuntu's default Python, verified versions, and tested creating, activating, and deactivating a `venv`:

```bash
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.11 python3.11-venv python3-pip -y
python3.11 --version   # Python 3.11.18
pip3 --version         # pip 24.0

cd ~/tech42/dev-setup-amro_h
python3.11 -m venv venv
source venv/bin/activate      
deactivate
```

### Step 4: VS Code Customization (`teck42` Profile)
Created a dedicated VS Code profile (`teck42`) to isolate program extensions and settings from personal projects, pointed the integrated terminal default profile to **Ubuntu-24.04 (WSL)**, and opened the project directly from WSL using `code .`.

**Three Deliberate Settings:**
1. `files.autoSave`: `"afterDelay"` — Automatically saves code changes after a short delay to prevent losing work.
2. `editor.formatOnSave`: `true` — Automatically formats Python code on save to maintain clean, consistent style.
3. `editor.fontSize`: `15` — Increases code readability during long coding and debugging sessions.

**Three Extensions:**
1. **WSL (Microsoft):** Connects VS Code on Windows directly to the Ubuntu 24.04 filesystem and runtime.
2. **Python (Microsoft):** Provides IntelliSense, linting, debugging, and virtual environment detection for Python 3.11.
3. **GitLens:** Enhances Git visibility with inline blame annotations, commit history, and branch tracking.

### Step 5: GitHub SSH Authentication
Generated a new Ed25519 SSH key pair inside WSL, added the public key to GitHub (`Settings -> SSH and GPG keys`), and verified authentication:

```bash
ssh-keygen -t ed25519 -C "amr.elhaj87@gmail.com"
cat ~/.ssh/id_ed25519.pub
ssh -T git@github.com
# Output: Hi amrelhaj87-cloud! You've successfully authenticated, but GitHub does not provide shell access.
```

All future `git push` and `git pull` operations will use this SSH key.

---

## 2. Troubleshooting: Something That Went Wrong & How I Fixed It
- **Problem:** Running `sudo apt install python3.11` directly failed with `E: Unable to locate package python3.11`.
- **Root Cause:** Ubuntu 24.04 ships with Python 3.12 in its default apt repositories and does not include Python 3.11 packages out of the box.
- **Fix:** Added the trusted `deadsnakes` PPA (`sudo add-apt-repository ppa:deadsnakes/ppa -y`), refreshed package lists with `sudo apt update`, and then installed `python3.11` and `python3.11-venv` cleanly.


---

## 3. Bonus Deliverables
1. **Exported VS Code Profile:** Exported the dedicated `teck42` profile (including extensions and settings) to `teck42.code-profile` at the root of this repository for easy reproducibility.
2. **Shell Aliases in `~/.bashrc`:** Added documented shortcuts in `~/.bashrc` to speed up daily navigation and environment activation:
   - `t42='cd ~/tech42/dev-setup-amro_h'` (jump directly to the project directory)
   - `act='source venv/bin/activate'` (activate the local Python 3.11 virtual environment)
   - `gs='git status'` (check repository status quickly)
   - `update='sudo apt update && sudo apt upgrade'` (update Ubuntu system packages)
3. **Windows Terminal Default Profile:** Configured Windows Terminal (`Settings -> Startup -> Default profile`) to launch directly into **Ubuntu-24.04 (WSL)** instead of PowerShell.