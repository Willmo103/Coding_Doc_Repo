# Batch File Documentation Wiki Article

## Introduction
Batch files are scripts used primarily on Windows operating systems to automate tasks by combining multiple
commands into a single file that can be executed automatically. This guide provides a comprehensive overview of
basic syntax, advanced features, and examples to help you create efficient batch files. We assume a foundational
knowledge of PowerShell, Bash, and various programming languages.

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

### Structured Output and Control Structures

#### Loops
Batch files don't natively support complex loops like `for` or `while` with dictionaries or structured data
structures directly, but you can emulate these using conditional statements and iterative logic.

**Example: Simple Loop with Conditional Checks**
```batch
@echo off
setlocal EnableDelayedExpansion

set counter=1
:loop
if %counter% geq 5 goto done
echo Iteration: !counter!
set /a counter+=1
goto loop

:done
echo Loop completed
endlocal
```

**Example: Processing Items in a List (Emulating a Dictionary)**
You can use environment variables or text files to simulate dictionary-like structures.

```batch
@echo off
setlocal EnableDelayedExpansion

:: Example dictionary stored in a file
set "dictFile=dict.txt"

>> "%dictFile%" echo Name=Alice Age=30
>> "%dictFile%" echo Name=Bob Age=25

for /f "delim=: tokens=1,2" %%A in (%dictFile%) do (
    if "%%A"=="Name" (
        set "name=%%B"
    ) else if "%%A"=="Age" (
        set "age=%%B"
        echo Name: !name! - Age: !age!
    )
)

del /q "%dictFile%"  :: Clean up temporary file
endlocal
```

#### Conditionals
Batch files support basic conditional execution using `if` statements.

**Example: Conditional Execution Based on Variable**
```batch
@echo off
set TEST_VALUE=10

if "%TEST_VALUE%" geq "5" (
    echo TEST_VALUE is greater than or equal to 5
) else (
    echo TEST_VALUE is less than 5
)
```

#### Structured Output
While batch files lack native support for complex data structures, you can format output to resemble structured
data using text formatting techniques.

**Example: Tabular Output**
```batch
@echo off
set "names=Name1 Name2 Name3"
set "values=Value1 Value2 Value3"

for %%N in (%names%) do (
    set /a index=%%N %% 3
    if %index% geq 0 (
        set /a index=index-1
        echo %%N: %%values[%index%]
    )
)

:: Assuming 'values' are predefined in variables for simplicity
set "values1=ValueX ValueY ValueZ"
set "values2=ValueA ValueB ValueC"
set "values3=ValueM ValueN ValueO"

:: Accessing structured values (simplified example)
for %%N in (%names%) do (
    set /a index=%%N %% 3
    if %index% geq 0 (
        set "value=!values[%index%]!"
        echo %%N: !value!
    )
)
```
