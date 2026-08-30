# Working with FZF in PowerShell Like a Power User

If PowerShell is your command-line operating system, then fzf (Fuzzy Finder) is one of the best productivity tools you can add to your workflow. It helps you instantly find files, folders, command history, Git branches, processes, and more using an interactive fuzzy-search interface.

## What is FZF?

fzf is a command-line fuzzy finder that allows you to interactively search through lists and select items without typing exact names.

Instead of:

```pwsh
cd C:\Projects\ClickShare\Telemetry\Backend\Api
```

You can simply:

```pwsh
fd | fzf
```

Type a few characters:

```text
tele api
```

And instantly find the desired directory or file.

## Why Use FZF?

Without FZF:

```pwsh
Get-ChildItem -Recurse
```

Scroll endlessly.

With FZF:

```pwsh
Get-ChildItem -Recurse | fzf
```

Instant search across thousands of items.

Benefits:

- Faster navigation
- Better command history search
- Interactive Git workflows
- Easier log analysis
- Keyboard-driven workflow
- Works great with PowerShell pipelines

## Installation

### Option 1: Winget (Recommended)

```pwsh
winget install junegunn.fzf
```

Verify installation:

```pwsh
fzf --version
```

### Option 2: Scoop

```pwsh
scoop install fzf
```

### Option 3: Chocolatey

```pwsh
choco install fzf
```

## Basic Usage

### Search Through Files

```pwsh
Get-ChildItem | Select-Object -ExpandProperty Name | fzf
```

Example:

```pwsh
dir | % Name | fzf
```

### Search Recursively

```pwsh
Get-ChildItem -Recurse -File |
Select-Object -Expand FullName |
fzf
```

### Search Running Processes

```pwsh
Get-Process |
Select-Object ProcessName |
fzf
```

### Search Services

```pwsh
Get-Service |
Select-Object Name |
fzf
```

## Directory Navigation

One of the most common uses.

### Find and Change Directory

```pwsh
cd (
    Get-ChildItem -Directory -Recurse |
    Select-Object -Expand FullName |
    fzf
)
```

Example:

```pwsh
cd (Get-ChildItem -Directory -Recurse | % FullName | fzf)
```

## Command History Search

PowerShell history becomes much more useful with FZF.

### Search Command History

```pwsh
Get-History |
Select-Object -ExpandProperty CommandLine |
fzf
```

Result:

```text
git cherry-pick f85cd1
```

Select and reuse it.

## Install PSFzf

The real magic for PowerShell users.

```pwsh
Install-Module PSFzf -Scope CurrentUser
```

Import it:

```pwsh
Import-Module PSFzf
```

### Enable Automatic Key Bindings

```pwsh
Set-PsFzfOption -PSReadlineChordProvider 'Ctrl+t'
Set-PsFzfOption -PSReadlineChordReverseHistory 'Ctrl+r'
```

Add to your profile for permanent usage:

```pwsh
$PROFILE
```

## Power User Shortcuts

### Ctrl + R

Interactive command history search.

Instead of:

```text
Up Arrow
Up Arrow
Up Arrow
...
```

Press:

```text
Ctrl + R
```

Search:

```text
docker
```

Instantly finds:

```text
docker compose up
```

### Ctrl + T

Interactive file picker.

Press:

```text
Ctrl + T
```

Search:

```text
settings
```

Returns:

```text
settings.json
```

### Alt + C

Directory picker. Quickly jump across your filesystem.

Press:

```text
Alt + C
```

Type:

```text
telemetry
```

Press Enter. You are there instantly.

## Preview Files While Searching

One of FZF's killer features.

```pwsh
Get-ChildItem -File -Recurse |
ForEach-Object FullName |
fzf --preview "Get-Content {} -Head 50"
```

While selecting:

```text
config.json
```

Preview window shows file contents.

## Searching Log Files

Very useful during production support.

```pwsh
Get-ChildItem *.log |
Select-Object -Expand FullName |
fzf --preview "Get-Content {} -Tail 100"
```

Browse logs interactively.

## Working with Git

FZF shines with Git.

### Checkout Branches

```pwsh
git branch |
fzf |
ForEach-Object {
    git checkout $_.Trim()
}
```

### Browse Commit History

```pwsh
git log --oneline |
fzf
```

Search:

```text
fix login
```

Find commits instantly.

### Cherry Pick Commit

```pwsh
git log --oneline |
fzf |
ForEach-Object {
    git cherry-pick ($_ -split " ")[0]
}
```

### Find Modified Files

```pwsh
git status --short |
fzf
```

## Using FZF with Ripgrep

This is an elite developer workflow.

Install ripgrep:

```pwsh
winget install BurntSushi.ripgrep.MSVC
```

Search text:

```pwsh
rg telemetry
```

Interactive search:

```pwsh
rg telemetry | fzf
```

### Search Across Source Code

```pwsh
rg "ClickShare" | fzf
```

Find references immediately.

## Multi-Select Mode

Select multiple items:

```pwsh
fzf -m
```

Example:

```pwsh
git branch |
fzf -m
```

Select multiple branches.

## Exact Matching

FZF defaults to fuzzy matching. For exact matches:

```pwsh
fzf -e
```

## Query Preloading

Start with a search query:

```pwsh
fzf -q telemetry
```

Useful in scripts.

## Building a PowerShell Dashboard

Pane 1 (Logs):

```pwsh
Get-Content app.log -Wait
```

Pane 2 (Git):

```pwsh
git status
```

Pane 3 (FZF File Browser):

```pwsh
Get-ChildItem -Recurse |
Select-Object -Expand FullName |
fzf
```

Pane 4 (Search Source Code):

```pwsh
rg TODO | fzf
```

## Advanced Functions for Your PowerShell Profile

Add to profile:

```pwsh
notepad $PROFILE
```

### Fuzzy Change Directory

```pwsh
function cdf {
    $dir = Get-ChildItem -Directory -Recurse |
        Select-Object -Expand FullName |
        fzf

    if ($dir) {
        Set-Location $dir
    }
}
```

Usage:

```pwsh
cdf
```

### Fuzzy Open File

```pwsh
function ff {
    $file = Get-ChildItem -File -Recurse |
        Select-Object -Expand FullName |
        fzf

    if ($file) {
        code $file
    }
}
```

Usage:

```pwsh
ff
```

### Kill Process Interactively

```pwsh
function pkillf {
    $proc = Get-Process |
        Select-Object Id, ProcessName |
        Out-String |
        fzf

    if ($proc) {
        $id = ($proc.Trim() -split '\s+')[0]
        Stop-Process -Id $id
    }
}
```

Usage:

```pwsh
pkillf
```

## Tips and Tricks from Daily Usage

### 1. Pair FZF with Windows Terminal Panes

Example layout:

```text
+----------------------+----------------------+
| Code Search          | Logs                 |
+----------------------+----------------------+
| Git Operations       | PowerShell Console   |
+----------------------+----------------------+
```

One pane always running:

```pwsh
rg . | fzf
```

### 2. Use FZF for Everything Interactive

Instead of remembering:

- Branch names
- File names
- Commit hashes
- Service names
- Process IDs

Just search.

### 3. Learn Query Operators

| Pattern | Meaning |
| --- | --- |
| telemetry | Fuzzy match |
| 'telemetry | Exact match |
| ^telemetry | Starts with |
| service$ | Ends with |
| telemetry api | Both terms required |
| !test | Exclude results |

Examples:

```text
^feature
!obsolete
```

### 4. Preview Before Selecting

Golden rule:

```pwsh
fzf --preview ...
```

Almost every time.

Example:

```pwsh
git log --oneline |
fzf --preview "git show {1}"
```

### 5. Create Project Jump Lists

```pwsh
@(
"C:\Projects\ClickShare"
"C:\Projects\Telemetry"
"C:\Projects\Scripts"
) | fzf
```

Useful when working across multiple repositories.

### 6. Combine with fd Instead of Get-ChildItem

Install:

```pwsh
winget install sharkdp.fd
```

Then:

```pwsh
fd | fzf
```

Much faster on large repositories.

## A Senior Engineer's Workflow

At the beginning of the day:

Pane 1:

```pwsh
git pull
```

Pane 2:

```pwsh
rg TODO | fzf
```

Pane 3:

```pwsh
Get-Content application.log -Wait
```

Pane 4:

```pwsh
fzf
```

Use pane 4 for quick file navigation.

This setup dramatically reduces directory hopping, tab switching, and time spent searching for files or commands.

## Best Practices

- Install and configure PSFzf
- Add key bindings to your $PROFILE
- Use Ctrl+R constantly for history search
- Use previews whenever possible
- Combine FZF with rg and fd
- Keep a dedicated FZF pane in Windows Terminal
- Create custom functions for navigation and Git tasks
- Favor keyboard-driven workflows over manual browsing

## Conclusion

FZF is one of the highest ROI tools you can add to a PowerShell environment. Combined with Windows Terminal panes, PSReadLine, ripgrep, fd, and Git, it transforms PowerShell into a lightning-fast, keyboard-centric workspace. Once Ctrl+R, Ctrl+T, fuzzy directory navigation, and live previews become muscle memory, you will rarely go back to traditional file browsing or command history navigation.
