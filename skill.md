GitHub Spec Kit – Fresh Laptop Setup (Windows)
Prerequisites
Windows 10/11
Visual Studio Code
Git for Windows
GitHub Copilot and GitHub Copilot Chat extensions
PowerShell
GitHub access and corporate network permissions
Installation Steps
Step 1: Install Git
winget install --id Git.Git -e
Step 2: Install VS Code
winget install --id Microsoft.VisualStudioCode -e
Step 3: Install uv package manager
winget install --id astral-sh.uv -e
Restart PowerShell after installation.
Step 4: Verify uv
uv --version
Step 5: Enable native TLS for Zscaler
$env:UV_NATIVE_TLS = "true"
Step 6: Install Python 3.12 through uv
uv python install 3.12 --native-tls
Step 7: Install GitHub Spec Kit
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git --python 3.12 --native-tls
Step 8: Configure PATH
uv tool update-shell
Restart VS Code or PowerShell.
Step 9: Verify installation
uv tool list
specify --help
Step 10: Initialize Spec Kit with GitHub Copilot
specify init my-project --ai copilot
Step 11: Open project in VS Code
cd my-project
code .
Step 12: Start Spec Kit workflow in Copilot Chat
/speckit.constitution
/speckit.specify
/speckit.clarify
/speckit.plan
/speckit.tasks
/speckit.analyze
/speckit.implement
Notes
Install the GitHub Copilot extensions through VS Code and sign in with your authorized GitHub account.
Python 3.12 is managed by uv; a separate system-wide Python installation is unnecessary.
Corporate software installation policies may require IT approval.
The Python 3.12-based approach is recommended based on the encountered certificate and PyYAML build errors; final successful setup was not yet confirmed.