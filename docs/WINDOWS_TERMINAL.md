# Windows PowerShell sticky notes

This is a MANUAL REFERENCE. When version 3 bootstrap.py --setup-env succeeds, sections
1–3 are already complete. Continue at section 4 only when ready to connect Git/GitHub.
Do not create a second environment or repeat setup through a VS Code wizard.
For manual use, run one section at a time in the VS Code terminal; stop on errors.
Commands assume the scaffold already exists. Change the first values for each project.
Variables last only for the current PowerShell session. These commands do not read
project.local.toml automatically.

## 1. Root and tools
```powershell
$ProjectRoot = 'D:\Projects\projects\retail_forecasting'
$PythonVersion = '3.12'
$BasePython = 'D:\python312\python.exe'
$GithubOwner = 'LeonovYurii'
$RepositoryName = 'retail_forecasting'
Set-Location -LiteralPath $ProjectRoot
Get-Location
& $BasePython --version
git --version
```
If $BasePython does not exist, set it to your actual installed Python executable and check its version.
Install Git/Python from their official distributions if missing. Do not recreate an
existing environment blindly; inspect .venv/pyvenv.cfg first.

## 2. Create an isolated environment (once per project)
Proceed only after the scaffold checker passes. If .venv already exists
from a cancelled attempt, first try `.\.venv\Scripts\python.exe --version` and inspect
.venv/pyvenv.cfg. Reuse it only if it works and matches the chosen Python version.
If incomplete or mismatched, rename it to a backup OUTSIDE the project before creating
a replacement; do not delete it blindly. Skip the venv creation line when reusing it.
```powershell
& $BasePython -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m pip install -e .
.\.venv\Scripts\python.exe -m pip check
.\.venv\Scripts\python.exe -c "import sys, pandas, numpy, sklearn; print(sys.executable)"
```
The explicit executable works without activation, including when PowerShell blocks
Activate.ps1. Optional activation for convenience:
```powershell
.\.venv\Scripts\Activate.ps1
```
In VS Code: Python: Select Interpreter -> .venv\Scripts\python.exe.
For each notebook also select this environment as its kernel. Check `sys.executable`
inside a notebook if imports differ from the terminal. Do not install packages globally.

## 3. Record the working environment
Only after imports and pip check succeed:
```powershell
.\.venv\Scripts\python.exe -m pip freeze --exclude-editable | Set-Content -Encoding utf8 requirements-environment.txt
.\.venv\Scripts\python.exe --version
```
Record the Python version and OS in README. Keep requirements.txt as the small direct
package list; requirements-environment.txt is a tested environment snapshot, not a
cross-platform lockfile. Excluding editable packages avoids embedding your absolute
project path. Restore a snapshot in a fresh environment using `-r requirements-environment.txt`,
then install this project with `-e .`. Recheck compatibility on other platforms.

## 4. Initialize Git locally
First inspect whether the folder already belongs to a repository:
```powershell
git rev-parse --show-toplevel
```
A fatal 'not a git repository' is expected for a new project. If a parent repository
is shown, decide whether the project belongs there before running git init.
For a genuinely new, independent project:
```powershell
git init -b main
git status --short --untracked-files=all
git config --get user.name
git config --get user.email
```
If identity is missing, set your real name and preferred GitHub email locally:
```powershell
git config user.name 'YOUR NAME'
git config user.email 'YOUR EMAIL OR GITHUB NOREPLY EMAIL'
```
Check ignored files (the following representative paths need not exist):
```powershell
git check-ignore -v .venv/example data/raw/train.csv .env kaggle.json artifacts/model.bin
```
After reviewing .gitignore and ensuring no credentials/data are included:
```powershell
git add .
git diff --cached --stat
git diff --cached
```
If something unintended is staged in this NEW repository, `git rm --cached -- PATH`
untracks a file without deleting the working copy. Then fix the ignore rule.
Commit only after reviewing the staged content:
```powershell
git commit -m 'Initialize project structure and working instructions'
```
.gitignore never removes already tracked files. Avoid blanket changes to existing repos.

## 5. Connect to GitHub when ready
In GitHub create an EMPTY repository named $RepositoryName under $GithubOwner.
Choose private while developing or public if ready for a portfolio. Do not initialize
it with a README, license, or .gitignore because those already exist locally.
Authenticate using Git Credential Manager's browser flow when prompted; never put a
token in the URL. GitHub account passwords are not Git HTTPS credentials.
```powershell
git remote -v
```
If there is no origin, add it:
```powershell
git remote add origin "https://github.com/$GithubOwner/$RepositoryName.git"
git remote -v
git push -u origin main
```
For an existing origin, inspect it instead of adding it again or replacing it silently.
For a nonempty remote, inspect/fetch its history and reconcile normally; do not force-push.
If your branch is not main, use its actual name (`git branch --show-current`).
A failed push does not remove the local commit; fix authentication/remote settings.

## 6. Normal work
Open the project root, select the environment, then:
```powershell
git status
```
After a meaningful change, inspect the diff, stage intended files, commit, and push.
Add optional libraries only for a concrete experiment. Update the direct requirements
and environment snapshot after verifying the new installation. Download raw Kaggle
files manually into data/raw after accepting the competition rules; do not commit them.
