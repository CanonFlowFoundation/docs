### Why we changed location

Previously the repositories lived at:

```text
/mnt/e/github/CanonFlowFoundation
```

That is your Windows `E:` drive accessed through WSL translation. Git performs thousands of small filesystem operations, making it slow.

The new location:

```text
/root/github/CanonFlowFoundation
```

is inside WSL’s native Linux ext4 filesystem. Microsoft recommends this layout when using Linux tools. [Microsoft WSL filesystem guidance](https://learn.microsoft.com/en-us/windows/wsl/filesystems)

Our measurement improved from **35.67 seconds to 0.11 seconds** for `git status`.

### Open it in VS Code

1. Open **Windows Terminal** from the Start menu.

2. Open PowerShell and enter:

```powershell
wsl -u root
```

Your prompt should change to a Linux prompt.

3. Enter:

```bash
cd /root/github/CanonFlowFoundation
```

4. Confirm the location:

```bash
pwd
```

Expected:

```text
/root/github/CanonFlowFoundation
```

5. See the repositories:

```bash
ls
```

You should see `CanonFlow`, `ONDCFlow`, `FSharpAssay`, `docs` and the other repositories.

6. Open the workspace:

```bash
code .
```

The `.` means “open the current directory.” VS Code should open connected directly to WSL. This is the [official VS Code WSL workflow](https://code.visualstudio.com/docs/remote/wsl-tutorial).

Check the bottom-left corner of VS Code. It should display something similar to:

```text
WSL: Ubuntu
```

### If `code` is not found

Install:

1. Windows Visual Studio Code.
2. The Microsoft **WSL** extension in VS Code.
3. Close and reopen Windows Terminal.
4. Repeat:

```powershell
wsl -u root
```

```bash
cd /root/github/CanonFlowFoundation
code .
```

### Important

You now have two copies:

- `/root/github/CanonFlowFoundation` — new, fast, use this.
- `/mnt/e/github/CanonFlowFoundation` — old backup, don’t continue development here.

The Linux copy survives closing WSL and restarting Windows. Continue committing and pushing to GitHub normally.