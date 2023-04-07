# windows

berisikan tweak serta setingan untuk windows

# oh-my-posh

<details>
<summary>Instalasi</summary>

saya menyimpan setingan di folder

```
C:\tools

// yang berisikan
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/15/2023   9:25 PM        3928576 ﬓ  fzf.exe
-a----         4/4/2023  10:25 PM            567   s.json
-a----         4/4/2023  10:16 PM           3432   stelbent.minimal.omp.json
```

create folder
trus buka powershell nya di folder nya
(run administrator)

```
irm "https://github.com/ChrisTitusTech/powershell-profile/raw/main/setup.ps1" | iex

abis itu yess to all
```

extrack file cove.zip

```
explorer .

kemudian unzip fole "cove.zip"
```

ubah jenis font nya menjadi caskaydiacove nerd front

lalu buka ulang terminal dan tambahkan scrip ini fungsi nya seperti sugest auto complete dari yang pernah kita pake

```
install-Module PSReadLine -Force

Set-PSReadLineOption -PredictionSource History

Set-PSReadLineOption -PredictionViewStyle ListView
```

untuk menambah setingan profile

```
cek direktory nya
$PROFILE

lalu buka dengan
notepad C:\Users\R\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

vscode C:\Users\R\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

link download font

```
https://www.nerdfonts.com/

```

install windows terminal nya

```

https://www.microsoft.com/store/productId/9N0DX20HK701

```

ubah font nya di vscode nya juga

```

ctrl+shift+p

ketik
setting.json

pilih yang personal tambahkan
"terminal.integrated.fontFamily": "CaskaydiaCove Nerd Font Mono",

```

profle setting

<details>

```ps1
# Import Terminal Icons
Import-Module -Name Terminal-Icons

# Find out if the current user identity is elevated (has admin rights)

$identity = [Security.Principal.WindowsIdentity]::GetCurrent()
$principal = New-Object Security.Principal.WindowsPrincipal $identity
$isAdmin = $principal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)

# If so and the current host is a command line, then change to red color

# as warning to user that they are operating in an elevated context

# Useful shortcuts for traversing directories

function cd... { Set-Location ..\.. }
function cd.... { Set-Location ..\..\.. }

# Compute file hashes - useful for checking successful downloads

function md5 { Get-FileHash -Algorithm MD5 $args }
function sha1 { Get-FileHash -Algorithm SHA1 $args }
function sha256 { Get-FileHash -Algorithm SHA256 $args }

# Quick shortcut to start notepad

function n { notepad $args }

# Drive shortcuts

function HKLM: { Set-Location HKLM: }
function HKCU: { Set-Location HKCU: }
function Env: { Set-Location Env: }

# Creates drive shortcut for Work Folders, if current user account is using it

if (Test-Path "$env:USERPROFILE\Work Folders") {
    New-PSDrive -Name Work -PSProvider FileSystem -Root "$env:USERPROFILE\Work Folders" -Description "Work Folders"
function Work: { Set-Location Work: }
}

# Set up command prompt and window title. Use UNIX-style convention for identifying

# whether user is elevated (root) or not. Window title shows current version of PowerShell

# and appends [ADMIN] if appropriate for easy taskbar identification

function prompt {
if ($isAdmin) {
"[" + (Get-Location) + "] # "
} else {
"[" + (Get-Location) + "] $ "
}
}

$Host.UI.RawUI.WindowTitle = "PowerShell {0}" -f $PSVersionTable.PSVersion.ToString()
if ($isAdmin) {
$Host.UI.RawUI.WindowTitle += " [ADMIN]"
}

# Does the the rough equivalent of dir /s /b. For example, dirs _.png is dir /s /b _.png

function dirs {
if ($args.Count -gt 0) {
        Get-ChildItem -Recurse -Include "$args" | Foreach-Object FullName
} else {
Get-ChildItem -Recurse | Foreach-Object FullName
}
}

# Simple function to start a new elevated process. If arguments are supplied then

# a single command is started with admin rights; if not then a new admin instance

# of PowerShell is started.

function admin {
if ($args.Count -gt 0) {
        $argList = "& '" + $args + "'"
        Start-Process "$psHome\powershell.exe" -Verb runAs -ArgumentList $argList
    } else {
        Start-Process "$psHome\powershell.exe" -Verb runAs
}
}

# Set UNIX-like aliases for the admin command, so sudo <command> will run the command

# with elevated rights.

Set-Alias -Name su -Value admin
Set-Alias -Name sudo -Value admin

# Make it easy to edit this profile once it's installed

function Edit-Profile {
if ($host.Name -match "ise") {
        $psISE.CurrentPowerShellTab.Files.Add($profile.CurrentUserAllHosts)
} else {
notepad $profile.CurrentUserAllHosts
}
}

# We don't need these any more; they were just temporary variables to get to $isAdmin.

# Delete them to prevent cluttering up the user profile.

Remove-Variable identity
Remove-Variable principal

Function Test-CommandExists {
Param ($command)
    $oldPreference = $ErrorActionPreference
    $ErrorActionPreference = 'SilentlyContinue'
    try { if (Get-Command $command) { RETURN $true } }
    Catch { Write-Host "$command does not exist"; RETURN $false }
Finally { $ErrorActionPreference = $oldPreference }
}

#

# Aliases

#

# If your favorite editor is not here, add an elseif and ensure that the directory it is installed in exists in your $env:Path

#

if (Test-CommandExists nvim) {
$EDITOR='nvim'
} elseif (Test-CommandExists pvim) {
$EDITOR='pvim'
} elseif (Test-CommandExists vim) {
$EDITOR='vim'
} elseif (Test-CommandExists vi) {
$EDITOR='vi'
} elseif (Test-CommandExists code) {
$EDITOR='code'
} elseif (Test-CommandExists notepad) {
$EDITOR='notepad'
} elseif (Test-CommandExists notepad++) {
$EDITOR='notepad++'
} elseif (Test-CommandExists sublime_text) {
$EDITOR='sublime_text'
}
Set-Alias -Name vim -Value $EDITOR

# Set Some Option for PSReadLine to show the history of our typed commands

Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -EditMode Windows
Set-PsFzfOption -PSReadlineChordProvider 'Ctrl+t' -PSReadlineChordReverseHistory 'Ctrl+r'
Set-PsFzfOption -TabExpansion

function ll { Get-ChildItem -Path $pwd -File }
function g { Set-Location $HOME\Documents\Github }
function desktop { Set-Location $HOME\Desktop }
function htdoc { Set-Location c:\xampp\htdocs }
function src { Set-Location 'C:\Program Files\Go\src' }
function home { Set-Location 'C:\' }
function linux { Set-Location '\\wsl$\Ubuntu-20.04\home\r' }
function gcom {
git add .
git commit -m "$args"
}
function lazyg {
    git add .
    git commit -m "$args"
git push
}
function Get-PubIP {
(Invoke-WebRequest http://ifconfig.me/ip ).Content
}
function uptime {
#Windows Powershell
Get-WmiObject win32*operatingsystem | Select-Object csname, @{
LABEL = 'LastBootUpTime';
EXPRESSION = { $*.ConverttoDateTime($\_.lastbootuptime) }
}

    #Powershell Core / Powershell 7+ (Uncomment the below section and comment out the above portion)

    <#
        $bootUpTime = Get-WmiObject win32_operatingsystem | Select-Object lastbootuptime
        $plusMinus = $bootUpTime.lastbootuptime.SubString(21,1)
        $plusMinusMinutes = $bootUpTime.lastbootuptime.SubString(22, 3)
        $hourOffset = [int]$plusMinusMinutes/60
        $minuteOffset = 00
        if ($hourOffset -contains '.') { $minuteOffset = [int](60*[decimal]('.' + $hourOffset.ToString().Split('.')[1]))}
        if ([int]$hourOffset -lt 10 ) { $hourOffset = "0" + $hourOffset + $minuteOffset.ToString().PadLeft(2,'0') } else { $hourOffset = $hourOffset + $minuteOffset.ToString().PadLeft(2,'0') }
        $leftSplit = $bootUpTime.lastbootuptime.Split($plusMinus)[0]
        $upSince = [datetime]::ParseExact(($leftSplit + $plusMinus + $hourOffset), 'yyyyMMddHHmmss.ffffffzzz', $null)
        Get-WmiObject win32_operatingsystem | Select-Object @{LABEL='Machine Name'; EXPRESSION={$_.csname}}, @{LABEL='Last Boot Up Time'; EXPRESSION={$upsince}}
        #>


    #Works for Both (Just outputs the DateTime instead of that and the machine name)
    # net statistics workstation | Select-String "since" | foreach-object {$_.ToString().Replace('Statistics since ', '')}

}
function reload-profile {
& $profile
}
function find-file($name) {
Get-ChildItem -recurse -filter "_${name}_" -ErrorAction SilentlyContinue | ForEach-Object {
$place_path = $_.directory
        Write-Output "${place*path}\${*}"
}
}
function unzip ($file) {
    Write-Output("Extracting", $file, "to", $pwd)
    $fullFile = Get-ChildItem -Path $pwd -Filter .\cove.zip | ForEach-Object { $_.FullName }
    Expand-Archive -Path $fullFile -DestinationPath $pwd
}
function zip {
    param(
        [Parameter(Mandatory=$true)]
[string]$name
    )
    $path = (Get-Location).Path
    Compress-Archive -Path "$path\*" -DestinationPath "$path\..\$name.zip"
}
function grep($regex, $dir) {
    if ( $dir ) {
        Get-ChildItem $dir | select-string $regex
        return
    }
    $input | select-string $regex
}
function touch($file) {
"" | Out-File $file -Encoding ASCII
}
function df {
    get-volume
}
function sed($file, $find, $replace) {
    (Get-Content $file).replace("$find", $replace) | Set-Content $file
}
function which($name) {
Get-Command $name | Select-Object -ExpandProperty Definition
}
function export($name, $value) {
    set-item -force -path "env:$name" -value $value;
}
function pkill($name) {
Get-Process $name -ErrorAction SilentlyContinue | Stop-Process
}
function pgrep($name) {
Get-Process $name

# membuka file explorer

}
function e {
explorer .
}

# membuka vscode

function v {
code .
}

# membuka port untuk mengakses apache wsl

function konek {
param(
[Parameter(Mandatory=$true)]
[string]$ip,
        [int]$listenport = 8000,
[int]$connectport = 8000
    )
    $listenaddress = $ip.Trim()
    $connectaddress = $($(wsl hostname -I).Trim())
$cmd = "netsh interface portproxy add v4tov4 listenport=$listenport listenaddress=$listenaddress connectport=$connectport connectaddress=$connectaddress"
Invoke-Expression $cmd
}

# cek port yang terbuka

function cek {
netsh interface portproxy show v4tov4
}

# matikan port

function stop {
netsh interface portproxy reset
}
function web {
$folderName = Split-Path -Leaf (Get-Location)
    $filePath = "C:\xampp\htdocs\$folderName"
    $url = "http://localhost/$folderName/"
$chromePath = "C:\Program Files\Google\Chrome\Application\chrome.exe"
    & $chromePath $url
}
function local {
    $url = "http://localhost/phpmyadmin/"
    $chromePath = "C:\Program Files\Google\Chrome\Application\chrome.exe"
    & $chromePath $url
}
function profile {
    code 'C:\Users\R\Documents\WindowsPowerShell'
}
function xampprun {
    Set-Location 'C:\xampp'
    Start-Process 'apache_start.bat' -WindowStyle Minimized
    Start-Process 'mysql_start.bat' -WindowStyle Minimized
}
function xamppstop {
    Set-Location 'C:\xampp'
    taskkill /f /im httpd.exe
    & '.\mysql\bin\mysqladmin.exe' -u root shutdown
}
function mysql {
    & 'C:\xampp\mysql\bin\mysql.exe' -u root -p
}
function gabung {
    $folder = Get-Location
    $pdfs = Get-ChildItem -Path $folder -Filter *.pdf | Select-Object -ExpandProperty FullName
    $output = Join-Path -Path $folder -ChildPath "output.pdf"
    & "C:\Program Files\gs\gs10.00.0\bin\gswin64c.exe" -sDEVICE=pdfwrite -dNOPAUSE -dBATCH -dSAFER -dQUIET -sOutputFile="$output" $pdfs
}
function opdf {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true, Position=0)]
[string]$InputFile,

        [Parameter(Mandatory=$true, Position=1)]
        [string]$OutputFile
    )

    $arguments = @(
        "-sDEVICE=pdfwrite",
        "-dCompatibilityLevel=1.4",
        "-dPDFSETTINGS=/screen",
        "-dNOPAUSE",
        "-dQUIET",
        "-dBATCH",
        "-sOutputFile=$OutputFile",
        $InputFile
    )

    &"C:\Program Files\gs\gs9.54.0\bin\gswin64c.exe" @arguments

    if (Test-Path $OutputFile) {
        return $true
    }
    else {
        return $false
    }

}
function p2w { # Load Aspose.PDF DLL
Add-Type -Path "C:\Program Files (x86)\Aspose\Aspose.PDF for .NET\Bin\net4.0\Aspose.PDF.dll"

    # Ask for PDF file location and name
    $pdfFile = Read-Host "Enter the PDF file location and name (e.g. C:\Folder\a.pdf)"

    # Load input PDF file
    $doc = New-Object Aspose.Pdf.Document($pdfFile)

    # Create DOCX save option
    $saveOptions = New-Object Aspose.Pdf.DocSaveOptions
    $saveOptions.Format = "DocX"

    # Construct output file name and location
    $outputFolder = Split-Path $pdfFile
    $outputFile = Join-Path $outputFolder "output.docx"

    # Save PDF to DOCX
    $doc.Save($outputFile, $saveOptions)

    Write-Host "PDF file converted to DOCX. Output file: $outputFile"

}
function j {
param(
[string]$DirectoryName
)

    # Change current directory to 'C:\'
    Set-Location 'C:\'

    # Execute PowerShell command 'z' with the directory name
    Invoke-Expression "z $DirectoryName"

}
function linuxstop {
wsl --shutdown Ubuntu-20.04
}
function linuxstat {
wsl --list -v
}
function cirun {
php spark serve
}
function cp { # Get current PowerShell directory
$currentDir = Split-Path -Parent $MyInvocation.MyCommand.Path

    # Ask for file name to copy
    $fileName = Read-Host "Enter the file name to copy"

    # Construct full source file path
    $sourceFilePath = Join-Path $currentDir $fileName

    # Check if source file exists
    if (-not (Test-Path $sourceFilePath)) {
        Write-Host "Error: $fileName not found in $currentDir" -ForegroundColor Red
        return
    }
    # Ask for destination file name
    $destFileName = Read-Host "Enter the destination file name"

    # Construct full destination file path
    $destFilePath = Join-Path $currentDir $destFileName

    # Copy file to destination
    Copy-Item $sourceFilePath $destFilePath

    if (Test-Path $destFilePath) {
        Write-Host "$fileName copied to $destFilePath" -ForegroundColor Green
    } else {
        Write-Host "Error: $fileName copy to $destFilePath failed" -ForegroundColor Red
    }

}
function mv { # Get current PowerShell directory
$currentDir = Split-Path -Parent $MyInvocation.MyCommand.Path

    # Ask for file name to move
    $fileName = Read-Host "Enter the file name to move"

    # Construct full source file path
    $sourceFilePath = Join-Path $currentDir $fileName

    # Check if source file exists
    if (-not (Test-Path $sourceFilePath)) {
        Write-Host "Error: $fileName not found in $currentDir" -ForegroundColor Red
        return
    }

    # Ask for destination file name
    $destFileName = Read-Host "Enter the destination file name"

    # Construct full destination file path
    $destFilePath = Join-Path $currentDir $destFileName

    # Move file to destination
    Move-Item $sourceFilePath $destFilePath

    Write-Host "$fileName moved to $destFilePath"

}
function gr {
$url = "https://github.com/rezapace?tab=repositories"
$chromePath = "C:\Program Files\Google\Chrome\Application\chrome.exe"
& $chromePath $url
}
function hp {
cd C:\scrcpy-win64-v2.0
.\scrcpy -m720 -b30m
}

# untuk mencari history

# function Invoke-PecoHistory {

# $command = Get-History | peco | select -expandproperty CommandLine

# if ($command) {

# Invoke-Expression $command

# }

# }

# Set-PSReadLineKeyHandler -Key Ctrl+f -ScriptBlock ${function:Invoke-PecoHistory}

# open api gpt 3

$script:OpenAI_Key = "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"

function ask
{
param(
[string]$question,
        [int]$tokens = 500,
[switch]$return
)
$key = $script:openai_key
$url = "https://api.openai.com/v1/completions"

    $body = [pscustomobject]@{
        "model" = "text-davinci-003"
        "prompt"      = "$question"
        "temperature"   = .2
        "max_tokens"=$tokens
        "top_p"=1
        "n"=1
        "frequency_penalty"= 1
        "presence_penalty"= 1
    }
    $header = @{
        "Authorization" = "Bearer $key"
        "Content-Type"  = "application/json"
    }
    $bodyJSON  = ($body | ConvertTo-Json -Compress)
    try
    {
        $res = Invoke-WebRequest -Headers $header -Body $bodyJSON -Uri $url -method post
        if ($PSVersionTable.PSVersion.Major -ne 5) {
            $output = ($res | convertfrom-json -Depth 3).choices.text.trim()
        }else{
            $output = ($res | convertfrom-json).choices.text.trim()
        }

        $formattedOutput = "# " + $question + " " + $output

        if ($return)
        {
            return $formattedOutput
        } else
        {
            write-host $formattedOutput
        }
    } catch
    {
        write-error $_.exception
    }

}

# func c untuk clear

function c {
Clear
}

function lupa {
Write-Host "Daftar perintah yang tersedia:" -ForegroundColor Cyan
Write-Host "home = lokasi ke disck c" -ForegroundColor Yellow
Write-Host "local = membuka localhost/phpmyadmin" -ForegroundColor Yellow
Write-Host "profile = menambah kan setingan menggunakan vscode" -ForegroundColor Yellow
Write-Host "zip = menzip file" -ForegroundColor Yellow
Write-Host "unzip = mengekstrak file" -ForegroundColor Yellow
Write-Host "g = github" -ForegroundColor Green
Write-Host "desktop = desktop" -ForegroundColor Green
Write-Host "htdoc = htdoc xampp" -ForegroundColor Magenta
Write-Host "src = src golang" -ForegroundColor Magenta
Write-Host "linux = linux file" -ForegroundColor Red
Write-Host "web = membuka localhost" -ForegroundColor Yellow
Write-Host "konek = membuka port" -ForegroundColor Yellow
Write-Host "cek = cek port yang terbuka" -ForegroundColor Yellow
Write-Host "stop = matikan port" -ForegroundColor Yellow
Write-Host "find-file = find-file($name) menemukan file" -ForegroundColor Yellow
    Write-Host "e           = membuka explorer" -ForegroundColor Yellow
    Write-Host "v           = membuka vscode" -ForegroundColor Yellow
    Write-Host "grep        = grep($regex, $dir) mencari kata" -ForegroundColor Yellow
    Write-Host "gcom        = menambahkan dan meng-commit semua perubahan" -ForegroundColor Yellow
    Write-Host "lazyg       = meng-push perubahan yang sudah dicommit ke repositori Git" -ForegroundColor Yellow
    Write-Host "lazy        = menambahkan dan meng-commit semua perubahan, kemudian meng-push perubahan yang sudah dicommit ke repositori Git" -ForegroundColor Yellow
    Write-Host "xampprun    = menjalankan xampp" -ForegroundColor Yellow
    Write-Host "xamppstop   = menghentikan xampp" -ForegroundColor Yellow
    Write-Host "profile     = menambahkan setingan vscode" -ForegroundColor Yellow
    Write-Host "lupa        = menampilkan perintah" -ForegroundColor Yellow
    Write-Host "mysql       = membuka mysql" -ForegroundColor Yellow
    Write-Host "gabung      = menggabungkan file pdf" -ForegroundColor Yellow
    Write-Host "opdf        = mengoptimasi file pdf" -ForegroundColor Yellow
    Write-Host "p2w         = mengubah file pdf ke word direktory dan nama pdf" -ForegroundColor Yellow
    Write-Host "j           = j($name) menuju folder" -ForegroundColor Yellow
Write-Host "linuxstop = menghentikan linux" -ForegroundColor Yellow
Write-Host "linuxstat = mengecek status linux" -ForegroundColor Yellow
Write-Host "cirun = menjalankan codeigniter" -ForegroundColor Yellow
Write-Host "cp = cp($name) menyalin file" -ForegroundColor Yellow
    Write-Host "mv          = mv($name) memindahkan file" -ForegroundColor Yellow
Write-Host "gr = membuka github reza" -ForegroundColor Yellow
Write-Host "ctrl+h = mencari history" -ForegroundColor Yellow
Write-Host "ask = ask($question) mengajukan pertanyaan" -ForegroundColor Yellow
Write-Host "c = clear" -ForegroundColor Yellow
Write-Host "ctrl+r = mencari history" -ForegroundColor Yellow
Write-Host "ctrl+f = menampilkna direktory folder" -ForegroundColor Yellow
}

## Final Line to set prompt

oh-my-posh init pwsh --config 'C:/tools/stelbent.minimal.omp.json' | Invoke-Expression

# oh-my-posh init pwsh --config ~/jandedobbeleer.omp.json | Invoke-Expression

# oh-my-posh init pwsh --config ~/stelbent.minimal.omp.json | Invoke-Expression

# Import the Chocolatey Profile that contains the necessary code to enable

# tab-completions to function for `choco`.

# Be aware that if you are missing these lines from your profile, tab completion

# for `choco` will not function.

# See https://ch0.co/tab-completion for details.

$ChocolateyProfile = "$env:ChocolateyInstall\helpers\chocolateyProfile.psm1"
if (Test-Path($ChocolateyProfile)) {
    Import-Module "$ChocolateyProfile"
}

```

</details>

file json terminal

<details>

```json
{
  "$help": "https://aka.ms/terminal-documentation",
  "$schema": "https://aka.ms/terminal-profiles-schema",
  "actions": [
    {
      "command": {
        "action": "copy",
        "singleLine": false
      },
      "keys": "ctrl+c"
    },
    {
      "command": "paste",
      "keys": "ctrl+v"
    },
    {
      "command": "find",
      "keys": "ctrl+shift+f"
    },
    {
      "command": {
        "action": "splitPane",
        "split": "auto",
        "splitMode": "duplicate"
      },
      "keys": "alt+shift+d"
    },
    {
      "command": {
        "action": "newTab",
        "index": 4
      },
      "keys": "alt+5"
    },
    {
      "command": {
        "action": "quakeMode"
      },
      "keys": "alt+a"
    },
    {
      "command": "unbound",
      "keys": ""
    },
    {
      "command": "unbound",
      "keys": "win+sc(41)"
    },
    {
      "command": "unbound",
      "keys": "ctrl+alt+t"
    },
    {
      "command": "unbound",
      "keys": "ctrl+alt+x"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+w"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+2"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+3"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+4"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+5"
    },
    {
      "command": "unbound",
      "keys": "ctrl+shift+1"
    },
    {
      "command": "closePane",
      "keys": "alt+w"
    },
    {
      "command": {
        "action": "newTab",
        "index": 1
      },
      "keys": "alt+2"
    },
    {
      "command": {
        "action": "newTab",
        "index": 2
      },
      "keys": "alt+3"
    },
    {
      "command": {
        "action": "newTab",
        "index": 3
      },
      "keys": "alt+4"
    },
    {
      "command": {
        "action": "newTab",
        "index": 0
      },
      "keys": "alt+1"
    }
  ],
  "alwaysShowNotificationIcon": true,
  "copyFormatting": "none",
  "copyOnSelect": false,
  "defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
  "minimizeToNotificationArea": true,
  "profiles": {
    "defaults": {
      "opacity": 50,
      "useAcrylic": true
    },
    "list": [
      {
        "colorScheme": "One Half Dark",
        "commandline": "%SystemRoot%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "elevate": true,
        "font": {
          "face": "CaskaydiaCove NFM"
        },
        "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
        "hidden": false,
        "name": "Windows PowerShell",
        "opacity": 100,
        "padding": "3",
        "useAcrylic": false
      },
      {
        "commandline": "%SystemRoot%\\System32\\cmd.exe",
        "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
        "hidden": false,
        "name": "Command Prompt"
      },
      {
        "colorScheme": "One Half Dark",
        "commandline": "ubuntu2004.exe",
        "cursorShape": "filledBox",
        "font": {
          "face": "CaskaydiaCove NFM",
          "size": 13
        },
        "guid": "{19255fc9-bf2d-4dda-b561-05ee1abe3ce2}",
        "hidden": false,
        "icon": "https://assets.ubuntu.com/v1/49a1a858-favicon-32x32.png",
        "name": "Ubuntu 20.04.5 LTS",
        "opacity": 56,
        "tabTitle": "Ubuntu 20.04.5 LTS",
        "useAcrylic": true
      },
      {
        "colorScheme": "One Half Dark",
        "commandline": "ubuntu2004.exe run fish",
        "cursorShape": "filledBox",
        "font": {
          "face": "CaskaydiaCove NFM",
          "size": 13
        },
        "guid": "{3e26226d-1bba-413b-a138-908d8de89e54}",
        "hidden": false,
        "icon": "https://assets.ubuntu.com/v1/49a1a858-favicon-32x32.png",
        "name": "fish",
        "opacity": 23,
        "tabTitle": "Ubuntu 20.04.5 LTS",
        "useAcrylic": true
      }
    ]
  },
  "schemes": [
    {
      "background": "#1B1D1E",
      "black": "#1B1D1E",
      "blue": "#737174",
      "brightBlack": "#505354",
      "brightBlue": "#919495",
      "brightCyan": "#A3A3A6",
      "brightGreen": "#FFF27D",
      "brightPurple": "#9A9A9D",
      "brightRed": "#FFF78E",
      "brightWhite": "#DADBD6",
      "brightYellow": "#37B4FD",
      "cursorColor": "#FFFFFF",
      "cyan": "#62605F",
      "foreground": "#6F6F6F",
      "green": "#C8BE46",
      "name": "Batman",
      "purple": "#747271",
      "red": "#E6DC44",
      "selectionBackground": "#FFFFFF",
      "white": "#C6C5BF",
      "yellow": "#F4FD22"
    },
    {
      "background": "#1C0C28",
      "black": "#0A4C62",
      "blue": "#90A5BD",
      "brightBlack": "#201637",
      "brightBlue": "#39173D",
      "brightCyan": "#5E6071",
      "brightGreen": "#0A6C7E",
      "brightPurple": "#BC94B7",
      "brightRed": "#C87272",
      "brightWhite": "#0A6C7E",
      "brightYellow": "#7A3188",
      "cursorColor": "#FFFFFF",
      "cyan": "#7E83CC",
      "foreground": "#BABAB9",
      "green": "#5CB1B3",
      "name": "BlueBerryPie",
      "purple": "#9D54A7",
      "red": "#99246E",
      "selectionBackground": "#FFFFFF",
      "white": "#F0E8D6",
      "yellow": "#EAB9A8"
    },
    {
      "background": "#0C0C0C",
      "black": "#0C0C0C",
      "blue": "#0037DA",
      "brightBlack": "#767676",
      "brightBlue": "#3B78FF",
      "brightCyan": "#61D6D6",
      "brightGreen": "#16C60C",
      "brightPurple": "#B4009E",
      "brightRed": "#E74856",
      "brightWhite": "#F2F2F2",
      "brightYellow": "#F9F1A5",
      "cursorColor": "#FFFFFF",
      "cyan": "#3A96DD",
      "foreground": "#CCCCCC",
      "green": "#13A10E",
      "name": "Campbell",
      "purple": "#881798",
      "red": "#C50F1F",
      "selectionBackground": "#FFFFFF",
      "white": "#CCCCCC",
      "yellow": "#C19C00"
    },
    {
      "background": "#012456",
      "black": "#0C0C0C",
      "blue": "#0037DA",
      "brightBlack": "#767676",
      "brightBlue": "#3B78FF",
      "brightCyan": "#61D6D6",
      "brightGreen": "#16C60C",
      "brightPurple": "#B4009E",
      "brightRed": "#E74856",
      "brightWhite": "#F2F2F2",
      "brightYellow": "#F9F1A5",
      "cursorColor": "#FFFFFF",
      "cyan": "#3A96DD",
      "foreground": "#CCCCCC",
      "green": "#13A10E",
      "name": "Campbell Powershell",
      "purple": "#881798",
      "red": "#C50F1F",
      "selectionBackground": "#FFFFFF",
      "white": "#CCCCCC",
      "yellow": "#C19C00"
    },
    {
      "background": "#1c1c1c",
      "black": "#282C34",
      "blue": "#61AFEF",
      "brightBlack": "#5A6374",
      "brightBlue": "#61AFEF",
      "brightCyan": "#37b4fd",
      "brightGreen": "#98C379",
      "brightPurple": "#C678DD",
      "brightRed": "#E06C75",
      "brightWhite": "#DCDFE4",
      "brightYellow": "#37b4fd",
      "cursorColor": "#FFFFFF",
      "cyan": "#56B6C2",
      "foreground": "#DCDFE4",
      "green": "#98C379",
      "name": "One Half Dark",
      "purple": "#C678DD",
      "red": "#E06C75",
      "selectionBackground": "#FFFFFF",
      "white": "#DCDFE4",
      "yellow": "#78b8a6"
    },
    {
      "background": "#FAFAFA",
      "black": "#383A42",
      "blue": "#0184BC",
      "brightBlack": "#4F525D",
      "brightBlue": "#61AFEF",
      "brightCyan": "#56B5C1",
      "brightGreen": "#98C379",
      "brightPurple": "#C577DD",
      "brightRed": "#DF6C75",
      "brightWhite": "#FFFFFF",
      "brightYellow": "#E4C07A",
      "cursorColor": "#4F525D",
      "cyan": "#0997B3",
      "foreground": "#383A42",
      "green": "#50A14F",
      "name": "One Half Light",
      "purple": "#A626A4",
      "red": "#E45649",
      "selectionBackground": "#FFFFFF",
      "white": "#FAFAFA",
      "yellow": "#C18301"
    },
    {
      "background": "#002B36",
      "black": "#002B36",
      "blue": "#268BD2",
      "brightBlack": "#073642",
      "brightBlue": "#839496",
      "brightCyan": "#93A1A1",
      "brightGreen": "#586E75",
      "brightPurple": "#6C71C4",
      "brightRed": "#CB4B16",
      "brightWhite": "#FDF6E3",
      "brightYellow": "#657B83",
      "cursorColor": "#FFFFFF",
      "cyan": "#2AA198",
      "foreground": "#839496",
      "green": "#859900",
      "name": "Solarized Dark",
      "purple": "#D33682",
      "red": "#DC322F",
      "selectionBackground": "#FFFFFF",
      "white": "#EEE8D5",
      "yellow": "#B58900"
    },
    {
      "background": "#FDF6E3",
      "black": "#002B36",
      "blue": "#268BD2",
      "brightBlack": "#073642",
      "brightBlue": "#839496",
      "brightCyan": "#93A1A1",
      "brightGreen": "#586E75",
      "brightPurple": "#6C71C4",
      "brightRed": "#CB4B16",
      "brightWhite": "#FDF6E3",
      "brightYellow": "#657B83",
      "cursorColor": "#002B36",
      "cyan": "#2AA198",
      "foreground": "#657B83",
      "green": "#859900",
      "name": "Solarized Light",
      "purple": "#D33682",
      "red": "#DC322F",
      "selectionBackground": "#FFFFFF",
      "white": "#EEE8D5",
      "yellow": "#B58900"
    },
    {
      "background": "#000000",
      "black": "#000000",
      "blue": "#3465A4",
      "brightBlack": "#555753",
      "brightBlue": "#729FCF",
      "brightCyan": "#34E2E2",
      "brightGreen": "#8AE234",
      "brightPurple": "#AD7FA8",
      "brightRed": "#EF2929",
      "brightWhite": "#EEEEEC",
      "brightYellow": "#FCE94F",
      "cursorColor": "#FFFFFF",
      "cyan": "#06989A",
      "foreground": "#D3D7CF",
      "green": "#4E9A06",
      "name": "Tango Dark",
      "purple": "#75507B",
      "red": "#CC0000",
      "selectionBackground": "#FFFFFF",
      "white": "#D3D7CF",
      "yellow": "#C4A000"
    },
    {
      "background": "#FFFFFF",
      "black": "#000000",
      "blue": "#3465A4",
      "brightBlack": "#555753",
      "brightBlue": "#729FCF",
      "brightCyan": "#34E2E2",
      "brightGreen": "#8AE234",
      "brightPurple": "#AD7FA8",
      "brightRed": "#EF2929",
      "brightWhite": "#EEEEEC",
      "brightYellow": "#FCE94F",
      "cursorColor": "#000000",
      "cyan": "#06989A",
      "foreground": "#555753",
      "green": "#4E9A06",
      "name": "Tango Light",
      "purple": "#75507B",
      "red": "#CC0000",
      "selectionBackground": "#FFFFFF",
      "white": "#D3D7CF",
      "yellow": "#C4A000"
    },
    {
      "background": "#300A24",
      "black": "#171421",
      "blue": "#0037DA",
      "brightBlack": "#767676",
      "brightBlue": "#08458F",
      "brightCyan": "#2C9FB3",
      "brightGreen": "#26A269",
      "brightPurple": "#A347BA",
      "brightRed": "#C01C28",
      "brightWhite": "#F2F2F2",
      "brightYellow": "#A2734C",
      "cursorColor": "#FFFFFF",
      "cyan": "#3A96DD",
      "foreground": "#FFFFFF",
      "green": "#26A269",
      "name": "Ubuntu-20.04-ColorScheme",
      "purple": "#881798",
      "red": "#C21A23",
      "selectionBackground": "#FFFFFF",
      "white": "#CCCCCC",
      "yellow": "#A2734C"
    },
    {
      "background": "#300A24",
      "black": "#171421",
      "blue": "#0037DA",
      "brightBlack": "#767676",
      "brightBlue": "#08458F",
      "brightCyan": "#2C9FB3",
      "brightGreen": "#26A269",
      "brightPurple": "#A347BA",
      "brightRed": "#C01C28",
      "brightWhite": "#F2F2F2",
      "brightYellow": "#A2734C",
      "cursorColor": "#FFFFFF",
      "cyan": "#3A96DD",
      "foreground": "#FFFFFF",
      "green": "#26A269",
      "name": "Ubuntu-22.04-ColorScheme",
      "purple": "#881798",
      "red": "#C21A23",
      "selectionBackground": "#FFFFFF",
      "white": "#CCCCCC",
      "yellow": "#A2734C"
    },
    {
      "background": "#000000",
      "black": "#000000",
      "blue": "#000080",
      "brightBlack": "#808080",
      "brightBlue": "#0000FF",
      "brightCyan": "#00FFFF",
      "brightGreen": "#00FF00",
      "brightPurple": "#FF00FF",
      "brightRed": "#FF0000",
      "brightWhite": "#FFFFFF",
      "brightYellow": "#FFFF00",
      "cursorColor": "#FFFFFF",
      "cyan": "#008080",
      "foreground": "#C0C0C0",
      "green": "#008000",
      "name": "Vintage",
      "purple": "#800080",
      "red": "#800000",
      "selectionBackground": "#FFFFFF",
      "white": "#C0C0C0",
      "yellow": "#808000"
    }
  ],
  "startOnUserLogin": true,
  "tabWidthMode": "compact"
}
```

</details>
