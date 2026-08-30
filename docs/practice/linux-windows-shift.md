# Linux/Bash to Windows/PowerShell Transition Guide

## Table of Contents
1. [Essential Command Mappings](#essential-command-mappings)
2. [PowerShell Setup & Configuration](#powershell-setup--configuration)
3. [File System Navigation](#file-system-navigation)
4. [Development Tools & Package Managers](#development-tools--package-managers)
5. [Environment Variables](#environment-variables)
6. [Process Management](#process-management)
7. [Network & System Information](#network--system-information)
8. [Text Processing & File Operations]()
9. [Git & Version Control](#git--version-control)
10. [Full-Stack Development Workflow](#full-stack-development-workflow)
11. [Useful PowerShell Features](#useful-powershell-features)

---

## Essential Command Mappings

### Basic Commands
| Linux/Bash | PowerShell | Description |
|------------|------------|-------------|
| `ls` | `Get-ChildItem` or `ls` | List directory contents |
| `cd` | `Set-Location` or `cd` | Change directory |
| `pwd` | `Get-Location` or `pwd` | Print working directory |
| `clear` | `Clear-Host` or `clear` | Clear screen |
| `cat` | `Get-Content` or `cat` | Display file contents |
| `cp` | `Copy-Item` or `cp` | Copy files/directories |
| `mv` | `Move-Item` or `mv` | Move/rename files |
| `rm` | `Remove-Item` or `rm` | Remove files/directories |
| `mkdir` | `New-Item -ItemType Directory` or `mkdir` | Create directory |
| `touch` | `New-Item -ItemType File` | Create empty file |
| `grep` | `Select-String` | Search text patterns |
| `find` | `Get-ChildItem -Recurse` | Find files |
| `ps` | `Get-Process` | List processes |
| `kill` | `Stop-Process` | Terminate process |
| `which` | `Get-Command` | Locate command |
| `man` | `Get-Help` | Display help/manual |
| `echo` | `Write-Output` or `echo` | Print to console |
| `env` | `Get-ChildItem Env:` | List environment variables |
| `history` | `Get-History` or `history` | Command history |

### File Permissions & Ownership
| Linux/Bash | PowerShell | Description |
|------------|------------|-------------|
| `chmod` | `icacls` or `Set-Acl` | Change permissions |
| `chown` | `takeown` or `Set-Acl` | Change ownership |

---

## PowerShell Setup & Configuration

### 1. Install Windows Terminal (Recommended)
Windows Terminal provides a modern, feature-rich terminal experience:
- Download from Microsoft Store or [GitHub](https://github.com/microsoft/terminal)
- Supports multiple tabs, split panes, and rich customization

### 2. Install PowerShell 7+ (PowerShell Core)
The newer cross-platform PowerShell is much better than Windows PowerShell 5.1:
```powershell
winget install Microsoft.PowerShell
```

### 3. Set Execution Policy
Allow scripts to run (run as Administrator):
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### 4. Create PowerShell Profile
Your profile is like `.bashrc` or `.bash_profile`:

Check if profile exists:
```powershell
Test-Path $PROFILE
```

Create profile if it doesn't exist:
```powershell
New-Item -Path $PROFILE -Type File -Force
```

Edit profile:
```powershell
notepad $PROFILE
# or use VS Code
code $PROFILE
```

### 5. Essential Profile Configuration

Add this to your `$PROFILE`:

```powershell
# Set UTF-8 encoding
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# Aliases for convenience
Set-Alias -Name vim -Value nvim  # If you use Neovim
Set-Alias -Name g -Value git
Set-Alias -Name touch -Value New-Item

# Custom function for 'touch' command
function touch { 
    param($file)
    if (Test-Path $file) {
        (Get-Item $file).LastWriteTime = Get-Date
    } else {
        New-Item -ItemType File -Path $file
    }
}

# Function to open current directory in VS Code
function code-here { code . }

# Enhanced ls with colors (requires PSReadLine)
function ll { Get-ChildItem -Force | Format-Table -AutoSize }
function la { Get-ChildItem -Force }

# Quick navigation
function .. { Set-Location .. }
function ... { Set-Location ../.. }
function .... { Set-Location ../../.. }

# Clear command
Set-Alias -Name c -Value Clear-Host

# Git shortcuts
function gs { git status }
function ga { git add $args }
function gc { git commit -m $args }
function gp { git push }
function gl { git log --oneline --graph --decorate }

# Development shortcuts
function dev { cd ~/Development }
function proj { cd ~/Projects }

# Function to find files (like 'find' command)
function Find-Files {
    param([string]$pattern)
    Get-ChildItem -Recurse -Filter $pattern
}
Set-Alias -Name ff -Value Find-Files

# Function to search content (like 'grep')
function Search-Content {
    param([string]$pattern, [string]$path = ".")
    Get-ChildItem -Path $path -Recurse | Select-String -Pattern $pattern
}
Set-Alias -Name grep -Value Search-Content

# Show-Path: Display PATH in readable format
function Show-Path {
    $env:Path -split ';'
}

# Reload profile
function Reload-Profile {
    . $PROFILE
    Write-Host "Profile reloaded!" -ForegroundColor Green
}
Set-Alias -Name reload -Value Reload-Profile
```

### 6. Install Oh My Posh (Optional - Makes terminal beautiful)
Oh My Posh is like Oh My Zsh for PowerShell:

```powershell
# Install Oh My Posh
winget install JanDeDobbeleer.OhMyPosh

# Install a Nerd Font (required for icons)
oh-my-posh font install
```

Add to your profile:
```powershell
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/jandedobbeleer.omp.json" | Invoke-Expression
```

### 7. Install PSReadLine (Better command-line editing)
Already included in PowerShell 7+, but configure it:

```powershell
# Add to your profile
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -EditMode Emacs  # or Windows/Vi
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
```

---

## File System Navigation

### Path Differences
- Linux: `/home/user/project`
- Windows: `C:\Users\user\project`
- PowerShell supports both `/` and `\` for paths

### Home Directory
```powershell
# Navigate to home
cd ~
# or
cd $HOME

# Your home directory
$HOME  # Usually C:\Users\YourUsername
```

### Drive Navigation
```powershell
# Change drives
cd D:\
cd C:\

# List all drives
Get-PSDrive
```

---

## Development Tools & Package Managers

### Package Managers

#### 1. Winget (Windows Package Manager)
Built into Windows 11, like `apt` or `yum`:

```powershell
# Search for package
winget search nodejs

# Install package
winget install NodeJS.NodeJS
winget install Git.Git
winget install Microsoft.VisualStudioCode
winget install Python.Python.3.11

# List installed
winget list

# Upgrade all
winget upgrade --all
```

#### 2. Chocolatey (Alternative)
Community-driven package manager:

```powershell
# Install Chocolatey (run as Admin)
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Install packages
choco install nodejs
choco install git
choco install vscode
choco install python

# Upgrade all
choco upgrade all
```

#### 3. Scoop (User-level package manager)
No admin rights required:

```powershell
# Install Scoop
irm get.scoop.sh | iex

# Install packages
scoop install nodejs
scoop install git
scoop install vscode
```

### Essential Development Tools

Install these for full-stack development:

```powershell
# Node.js and npm
winget install OpenJS.NodeJS.LTS

# Git
winget install Git.Git

# VS Code
winget install Microsoft.VisualStudioCode

# Python
winget install Python.Python.3.12

# Docker Desktop
winget install Docker.DockerDesktop

# PostgreSQL
winget install PostgreSQL.PostgreSQL

# MongoDB
winget install MongoDB.Server

# Redis (use WSL or Docker)
# Native Windows version available but WSL recommended

# Postman
winget install Postman.Postman
```

---

## Environment Variables

### View Environment Variables
```powershell
# List all
Get-ChildItem Env:

# Get specific variable
$env:PATH
$env:NODE_ENV

# In Linux style
echo $env:PATH
```

### Set Environment Variables

#### Temporary (current session only)
```powershell
$env:NODE_ENV = "development"
$env:API_KEY = "your-secret-key"
```

#### Permanent (User level)
```powershell
[System.Environment]::SetEnvironmentVariable('NODE_ENV', 'development', 'User')
```

#### Permanent (System level - requires Admin)
```powershell
[System.Environment]::SetEnvironmentVariable('JAVA_HOME', 'C:\Program Files\Java\jdk-17', 'Machine')
```

### Modify PATH
```powershell
# Add to PATH temporarily
$env:PATH += ";C:\MyProgram\bin"

# Add to PATH permanently (User)
$currentPath = [System.Environment]::GetEnvironmentVariable('PATH', 'User')
$newPath = $currentPath + ";C:\MyProgram\bin"
[System.Environment]::SetEnvironmentVariable('PATH', $newPath, 'User')
```

### .env Files
For Node.js projects, use `dotenv` package as usual:
```javascript
require('dotenv').config();
```

---

## Process Management

### List Processes
```powershell
# All processes
Get-Process

# Specific process
Get-Process -Name node
Get-Process -Name chrome

# With ports (like netstat)
Get-NetTCPConnection | Where-Object State -eq Listen
```

### Kill Processes
```powershell
# By name
Stop-Process -Name node
Stop-Process -Name chrome -Force

# By PID
Stop-Process -Id 1234

# Kill process on specific port
$port = 3000
$process = Get-NetTCPConnection -LocalPort $port -ErrorAction SilentlyContinue
if ($process) {
    Stop-Process -Id $process.OwningProcess -Force
}
```

### Find Process Using Port
```powershell
# Function to add to your profile
function Find-ProcessOnPort {
    param([int]$port)
    Get-NetTCPConnection -LocalPort $port -ErrorAction SilentlyContinue | 
        Select-Object LocalPort, OwningProcess, @{Name="ProcessName";Expression={(Get-Process -Id $_.OwningProcess).ProcessName}}
}

# Usage
Find-ProcessOnPort 3000
```

---

## Network & System Information

### Network Commands
| Linux/Bash | PowerShell | Description |
|------------|------------|-------------|
| `ifconfig` | `Get-NetIPAddress` | Network interfaces |
| `ping` | `Test-Connection` or `ping` | Ping host |
| `curl` | `Invoke-WebRequest` or `curl` | HTTP request |
| `wget` | `Invoke-WebRequest` | Download file |
| `netstat` | `Get-NetTCPConnection` | Network connections |
| `hostname` | `hostname` or `$env:COMPUTERNAME` | Computer name |

### Examples
```powershell
# Get IP address
Get-NetIPAddress | Where-Object AddressFamily -eq IPv4

# Test connection
Test-Connection google.com

# Download file
Invoke-WebRequest -Uri "https://example.com/file.zip" -OutFile "file.zip"

# Make API call
$response = Invoke-RestMethod -Uri "https://api.example.com/data" -Method Get
$response | ConvertTo-Json

# POST request
$body = @{ name = "John"; email = "john@example.com" } | ConvertTo-Json
Invoke-RestMethod -Uri "https://api.example.com/users" -Method Post -Body $body -ContentType "application/json"
```

---

## Text Processing & File Operations

### Search Text (grep equivalent)
```powershell
# Search in files
Select-String -Path *.js -Pattern "function"

# Search recursively
Get-ChildItem -Recurse -Filter *.js | Select-String -Pattern "TODO"

# Case-insensitive search
Select-String -Path *.txt -Pattern "error" -CaseSensitive:$false

# Show line numbers
Select-String -Path app.js -Pattern "import" | Select-Object LineNumber, Line
```

### File Operations
```powershell
# Copy directory recursively
Copy-Item -Path source -Destination dest -Recurse

# Remove directory recursively
Remove-Item -Path node_modules -Recurse -Force

# Create nested directories
New-Item -Path "src/components/ui" -ItemType Directory -Force

# Count files
(Get-ChildItem -File).Count

# Find large files
Get-ChildItem -Recurse | Where-Object { $_.Length -gt 10MB } | Sort-Object Length -Descending

# Get file size
(Get-Item file.txt).Length / 1MB  # Size in MB
```

### Text Manipulation
```powershell
# Read file
Get-Content app.js

# Read last 10 lines (like tail)
Get-Content app.js -Tail 10

# Follow file updates (like tail -f)
Get-Content app.js -Wait

# Replace text in file
(Get-Content config.js) -replace 'localhost', '127.0.0.1' | Set-Content config.js

# Count lines
(Get-Content app.js).Count
```

---

## Git & Version Control

Git works the same way! But here are some PowerShell-specific tips:

```powershell
# Git is available as usual
git clone https://github.com/user/repo.git
git status
git add .
git commit -m "message"
git push

# PowerShell-enhanced git status
function Git-Status-Enhanced {
    git status
    Write-Host "`nBranch: " -NoNewline -ForegroundColor Cyan
    git branch --show-current
}

# Posh-Git (Git status in prompt)
Install-Module posh-git -Scope CurrentUser -Force
Import-Module posh-Git
Add-PoshGitToProfile
```

---

## Full-Stack Development Workflow

### Frontend Development (React/Vue/Angular)

```powershell
# Create React app
npx create-react-app my-app
cd my-app
npm start

# Create Vite project
npm create vite@latest my-vue-app -- --template vue
cd my-vue-app
npm install
npm run dev

# Create Next.js app
npx create-next-app@latest my-nextjs-app
cd my-nextjs-app
npm run dev
```

### Backend Development (Node.js/Express)

```powershell
# Initialize project
mkdir my-api
cd my-api
npm init -y

# Install dependencies
npm install express mongoose dotenv cors

# Install dev dependencies
npm install -D nodemon

# Run with nodemon
npm run dev
```

### Database Setup

#### MongoDB
```powershell
# Start MongoDB (if installed locally)
net start MongoDB

# Or use MongoDB Compass GUI
# Or use Docker:
docker run -d -p 27017:27017 --name mongodb mongo
```

#### PostgreSQL
```powershell
# Start PostgreSQL service
net start postgresql-x64-15

# Connect to database
psql -U postgres

# Or use pgAdmin GUI
```

### Docker Commands
Docker works identically to Linux:

```powershell
# Build image
docker build -t my-app .

# Run container
docker run -d -p 3000:3000 my-app

# Docker Compose
docker-compose up -d
docker-compose down

# List containers
docker ps

# View logs
docker logs container-name -f
```

### Running Multiple Services

Create a PowerShell script `start-dev.ps1`:

```powershell
# Start backend
Start-Process pwsh -ArgumentList "-NoExit", "-Command", "cd backend; npm run dev"

# Start frontend
Start-Process pwsh -ArgumentList "-NoExit", "-Command", "cd frontend; npm run dev"

# Start database (if using Docker)
docker-compose up -d

Write-Host "Development environment started!" -ForegroundColor Green
```

Run with:
```powershell
.\start-dev.ps1
```

---

## Useful PowerShell Features

### 1. Pipeline (Similar to Linux)
```powershell
# Chain commands with pipeline
Get-Process | Where-Object CPU -gt 100 | Sort-Object CPU -Descending

# Count items
Get-ChildItem *.js | Measure-Object

# Export to CSV
Get-Process | Export-Csv -Path processes.csv
```

### 2. Object-Oriented Output
PowerShell outputs objects, not text:

```powershell
# Get properties
$file = Get-Item app.js
$file.Length
$file.LastWriteTime
$file.Extension

# Select specific properties
Get-Process | Select-Object Name, CPU, Memory | Format-Table
```

### 3. Tab Completion
Tab completion is powerful in PowerShell:
- Press `Tab` to cycle through options
- Works with commands, parameters, file paths, and values

### 4. Command History Search
- `Ctrl + R`: Reverse search through history (like Bash)
- `F8`: Search history based on current input
- `Get-History`: View all history

### 5. Splatting (Clean parameter passing)
```powershell
$params = @{
    Path = "C:\Projects"
    Recurse = $true
    Filter = "*.js"
}
Get-ChildItem @params
```

### 6. Error Handling
```powershell
try {
    # Your command
    Remove-Item file.txt -ErrorAction Stop
} catch {
    Write-Host "Error: $_" -ForegroundColor Red
}
```

---

## Quick Reference Card

### Daily Commands
```powershell
# Navigation
cd, pwd, ls, ..

# File operations
cp, mv, rm, mkdir, cat

# Development
npm start, npm install, npm run dev
git status, git add, git commit, git push

# Process management
Get-Process, Stop-Process
Find-ProcessOnPort 3000

# Environment
$env:NODE_ENV = "development"

# Search
Select-String -Pattern "text" -Path *.js

# Help
Get-Help Get-Process
Get-Command *process*
```

### Keyboard Shortcuts
- `Ctrl + C`: Cancel command
- `Ctrl + L`: Clear screen (or `clear`)
- `Ctrl + R`: Search history
- `Tab`: Auto-complete
- `F7`: Visual command history
- `Up/Down`: Navigate history

---

## Tips for Smooth Transition

1. **Use Aliases**: PowerShell has built-in aliases for common commands (`ls`, `cd`, `pwd`, `cat`, `cp`, `mv`, `rm`)

2. **Install Windows Terminal**: Much better than default PowerShell console

3. **Use VS Code**: Integrated terminal works great with PowerShell

4. **Consider WSL2**: For Linux-specific tools, you can use Windows Subsystem for Linux alongside PowerShell

5. **Embrace Objects**: PowerShell's object-oriented nature is powerful once you get used to it

6. **Use `Get-Help`**: Detailed help for any command: `Get-Help Get-Process -Full`

7. **Explore Modules**: PowerShell has modules for everything: `Find-Module keyword`

8. **Script Everything**: Save your workflows as `.ps1` scripts

---

## Additional Resources

- **PowerShell Documentation**: https://docs.microsoft.com/powershell
- **Windows Terminal**: https://github.com/microsoft/terminal
- **Oh My Posh**: https://ohmyposh.dev
- **PSReadLine**: https://github.com/PowerShell/PSReadLine
- **Awesome PowerShell**: https://github.com/janikvonrotz/awesome-powershell

---
