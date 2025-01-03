## Basic Syntax
### Creating a Simple Batch File
1. **Open Notepad** or any text editor.
2. **Write Commands**: Enter commands one per line or separated by `&` for sequential execution.
3. **Save File**: Save the file with a `.bat` extension (e.g., `example.bat`).

#### Example 1: Basic Command Execution
```batch
@echo off
echo Hello, World!
pause
```

#### Example 2: Using Variables
```batch
@echo off
set NAME=Alice
echo Welcome, %NAME%!
```

### Passing Arguments
Arguments allow you to pass data to your batch file when executed.

#### Example 1: Basic Argument Usage
```batch
@echo off
echo You entered: %1
start "" "notepad" %1  ; Opens Notepad with the first argument
```
**Execution**: `example.bat somefile.txt`

#### Example 2: Handling Multiple Arguments
```batch
@echo off
set count=0
:loop
if "%~1"=="" goto done
echo Argument %count%: %~1
set /a count+=1
shift
goto loop
:done
echo Done processing arguments.
```
**Execution**: `example.bat arg1 arg2 arg3`

## Advanced Features

### Running Commands in the Background (BG)
Use `start` with appropriate flags to run commands in the background.

#### Example 1: Running a Command in BG
```batch
@echo off
start "" cmd /c "dir /s > output.txt"  ; Directory listing saved to file
```

#### Example 2: Running Multiple BG Processes
```batch
@echo off
start "" cmd /c "tasklist > process.txt"
start "" cmd /c "ipconfig /all > network.txt"
```

### Logging
Logging helps track what your script does over time.

#### Example 1: Basic Logging
```batch
@echo off
echo [INFO] Script started at %DATE% %TIME% >> log.txt
echo Running task... >> log.txt
tasklist >> log.txt
echo Task listing completed >> log.txt
```

### Making HTTP Requests
Use `powershell` commands embedded within batch files for HTTP requests.

#### Example 1: GET Request Using PowerShell
```batch
@echo off
powershell -Command "Invoke-WebRequest -Uri 'https://api.example.com/data' -OutFile 'output.json'"
```

#### Example 2: POST Request Using PowerShell
```batch
@echo off
powershell -Command @"
$body = @{key='value'; anotherKey='anotherValue'} | ConvertTo-Json
Invoke-RestMethod -Uri 'https://api.example.com/submit' -Method Post -Body $body -ContentType 'application/json'
"@
```

### Sending Files via FTP/SFTP
Batch files can invoke PowerShell for FTP and SFTP operations.

#### Example 1: FTP Upload Using PowerShell
```batch
@echo off
powershell -Command @"
$ftpSession = New-Object System.Net.Sockets.TcpClient('ftp.example.com', 21)
$stream = $ftpSession.GetStream()
$ftp = New-Object Net.FtpWebRequest('ftp://ftp.example.com/path/to/file.txt', 'PUT')
$requestStream = $ftp.GetRequestStream()
[System.IO.File]::ReadAllBytes('localFilePath') | % { $requestStream.WriteByte($_) }
$ftp.GetResponse().GetStream().Close()
$ftpSession.Close()
"@
```

#### Example 2: SFTP Upload Using PowerShell
```batch
@echo off
powershell -Command @"
$host = New-Object System.Net.Sockets.TcpClient('sftp.example.com', 22)
$stream = $host.GetStream()
$sftp = [System.Net.Security.SslStream]::new($stream, $true,
[System.Security.Authentication.SecureSocketLayerPolicy]::RequireServerCertificate)
$sftp.Authenticate((New-Object System.Net.NetworkCredential('username', 'password')))
$fileStream = [System.IO.File]::OpenRead('localFilePath')
$sftp.UploadFile('remoteFilePath', $fileStream)
"@
```

## Conclusion
This guide covers essential aspects of creating batch files, from basic command execution to advanced
functionalities like background processing, logging, HTTP requests, and file transfers via FTP/SFTP. Experiment
with these examples to tailor your scripts to specific needs and integrate them seamlessly into your workflow.

---
