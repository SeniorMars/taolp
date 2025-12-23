# Week 1 Notes - Shell Scripting

Week 1 builds on the Unix tools from Week 0 by showing you how to combine them into reusable, powerful scripts. Instead of typing the same commands repeatedly, you'll learn to write small programs that automate your workflow.

## Introduction

So far you've executed individual commands and chained them with pipes. But what if you want to:
* Run the same sequence of commands every day?
* Make decisions based on conditions?
* Process multiple files with the same logic?
* Save your workflows for teammates to use?

This is where shell scripting comes in. **Shell scripts are the next step in complexity**: they're programs optimized for performing shell-related tasks. They make creating command pipelines, saving results, and reading input easier than general-purpose programming languages.

We'll focus on **bash scripting** since it's the most common, but the concepts apply to other shells too.

## Basic Syntax

### Variables

To assign a variable in bash:

```bash
foo=bar
```

To access it, use `$`:

```bash
echo $foo
# Output: bar
```

**Critical gotcha**: Spaces matter!

```bash
foo=bar      # ✓ Correct
foo = bar    # ✗ Wrong - tries to run 'foo' with arguments '=' and 'bar'
```

In shell scripts, **space is the argument separator**. This is the #1 source of beginner errors.

### Strings: Single vs Double Quotes

Quotes change how bash interprets your strings:

```bash
foo=bar

echo "$foo"   # Output: bar (variable substituted)
echo '$foo'   # Output: $foo (literal string)
```

**Rules:**
* `"double quotes"` - Variables are expanded, escape sequences work
* `'single quotes'` - Everything is literal, no substitution

**Best practice**: Use double quotes for variables, single quotes for literal strings.

```bash
name="world"
echo "Hello, $name!"     # Hello, world!
echo 'Hello, $name!'     # Hello, $name!
```

### Comments

```bash
# This is a comment
echo "This runs"  # This is also a comment
```

## Control Flow

### If Statements

```bash
if [[ condition ]]; then
    # do something
elif [[ other_condition ]]; then
    # do something else
else
    # default case
fi
```

**Example:**

```bash
if [[ -f "myfile.txt" ]]; then
    echo "File exists"
else
    echo "File not found"
fi
```

**Common test conditions:**

| Test | Meaning |
|------|---------|
| `[[ -f file ]]` | File exists and is a regular file |
| `[[ -d dir ]]` | Directory exists |
| `[[ -z string ]]` | String is empty |
| `[[ -n string ]]` | String is not empty |
| `[[ str1 == str2 ]]` | Strings are equal |
| `[[ num1 -eq num2 ]]` | Numbers are equal |
| `[[ num1 -lt num2 ]]` | num1 less than num2 |
| `[[ num1 -gt num2 ]]` | num1 greater than num2 |

**Pro tip**: Use `[[ ]]` (double brackets) instead of `[ ]` (single brackets). Double brackets are:
* More powerful (support `&&`, `||`, pattern matching)
* Safer (better handling of empty variables)
* More forgiving with syntax

### For Loops

**Iterate over a list:**

```bash
for item in apple banana cherry; do
    echo "I like $item"
done
```

**Iterate over files:**

```bash
for file in *.txt; do
    echo "Processing $file"
    wc -l "$file"
done
```

**Iterate over command output:**

```bash
for user in $(cat users.txt); do
    echo "Hello, $user"
done
```

**C-style for loop:**

```bash
for ((i=0; i<10; i++)); do
    echo "Number $i"
done
```

### While Loops

```bash
count=0
while [[ $count -lt 5 ]]; do
    echo "Count: $count"
    ((count++))
done
```

**Read from file line by line:**

```bash
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt
```

### Case Statements

For pattern matching (like `switch` in other languages):

```bash
case "$1" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Unknown command: $1"
        ;;
esac
```

## Functions

Functions let you organize and reuse code:

```bash
greet() {
    echo "Hello, $1!"
}

greet "Alice"  # Output: Hello, Alice!
greet "Bob"    # Output: Hello, Bob!
```

**Example: Create directory and cd into it:**

```bash
mcd() {
    mkdir -p "$1"
    cd "$1"
}

mcd my_project  # Creates and enters my_project/
```

**Functions with multiple arguments:**

```bash
backup_file() {
    local source="$1"
    local dest="$2"
    cp "$source" "${dest}.bak"
    echo "Backed up $source to ${dest}.bak"
}

backup_file config.txt config
```

## Special Variables

Bash uses special variables to access script metadata and arguments:

| Variable | Meaning |
|----------|---------|
| `$0` | Name of the script |
| `$1, $2, ..., $9` | Positional arguments |
| `$@` | All arguments as separate strings |
| `$*` | All arguments as a single string |
| `$#` | Number of arguments |
| `$?` | Exit code of previous command |
| `$$` | Process ID (PID) of current script |
| `$!` | PID of last background command |
| `!!` | Entire last command (useful with `sudo !!`) |
| `$_` | Last argument of previous command |

**Example script using special variables:**

```bash
#!/bin/bash

echo "Script name: $0"
echo "First argument: $1"
echo "All arguments: $@"
echo "Number of arguments: $#"
echo "Process ID: $$"
```

**Running it:**

```bash
$ ./myscript.sh hello world
Script name: ./myscript.sh
First argument: hello
All arguments: hello world
Number of arguments: 2
Process ID: 12345
```

## Exit Codes and Error Handling

Every command returns an **exit code** (0 = success, non-zero = error):

```bash
grep "pattern" file.txt
echo $?  # 0 if found, 1 if not found
```

### Conditional Execution

Use exit codes to chain commands:

```bash
# AND operator (&&) - run second command only if first succeeds
mkdir mydir && cd mydir

# OR operator (||) - run second command only if first fails
grep "error" log.txt || echo "No errors found"

# Semicolon (;) - run commands sequentially regardless
cd /tmp; ls; pwd
```

**Examples:**

```bash
# If backup succeeds, delete original
tar -czf backup.tar.gz mydir && rm -rf mydir

# Try command, show error if it fails
./deploy.sh || echo "Deployment failed!"

# Try multiple fallbacks
command1 || command2 || command3 || echo "All failed"
```

### Set Error Handling

Make scripts safer with these flags:

```bash
#!/bin/bash
set -e  # Exit on any error
set -u  # Exit on undefined variable
set -o pipefail  # Exit if any command in pipeline fails

# Combine them:
set -euo pipefail
```

**Example:**

```bash
#!/bin/bash
set -e

# Script stops if any command fails
cd /important/directory
rm *.tmp
./critical_task.sh
```

### Explicit Error Checking

```bash
if ! command; then
    echo "Command failed!"
    exit 1
fi
```

Or:

```bash
command
if [[ $? -ne 0 ]]; then
    echo "Error occurred"
    exit 1
fi
```

## Command Substitution

Capture command output in a variable:

```bash
# Modern syntax (preferred)
current_date=$(date)
files=$(ls *.txt)

# Old syntax (still works)
current_date=`date`
```

**Example:**

```bash
#!/bin/bash

echo "Starting program at $(date)"
echo "Running on $(hostname)"
echo "Current directory has $(ls | wc -l) items"
```

**In conditionals:**

```bash
if [[ $(whoami) == "root" ]]; then
    echo "Running as root"
fi
```

## Process Substitution

Treat command output as a file:

```bash
diff <(ls dir1) <(ls dir2)
```

This executes both `ls` commands and provides their outputs as temporary files to `diff`.

**Example: Compare remote and local files:**

```bash
diff <(ssh server 'cat /etc/config') /etc/config
```

## Redirection

### Standard Streams

* `stdin` (0) - Standard input
* `stdout` (1) - Standard output  
* `stderr` (2) - Standard error

### Redirect Output

```bash
# Redirect stdout to file (overwrite)
command > output.txt

# Redirect stdout to file (append)
command >> output.txt

# Redirect stderr to file
command 2> error.txt

# Redirect both stdout and stderr
command &> all_output.txt
command > output.txt 2>&1

# Discard output
command > /dev/null
command 2> /dev/null
command &> /dev/null
```

### Redirect Input

```bash
# Read from file
command < input.txt

# Here document (multi-line input)
cat << EOF
This is line 1
This is line 2
EOF

# Here string (single line input)
grep "pattern" <<< "search this string"
```

**Example: Script with all redirections:**

```bash
#!/bin/bash

# Save output and errors separately
./my_command > output.log 2> error.log

# Append to existing logs
./another_command >> output.log 2>> error.log

# Run silently
./quiet_command &> /dev/null
```

## Globbing and Wildcards

Bash expands patterns before running commands:

### Basic Wildcards

| Pattern | Matches |
|---------|---------|
| `*` | Any string (including empty) |
| `?` | Any single character |
| `[abc]` | One of: a, b, or c |
| `[a-z]` | Any lowercase letter |
| `[0-9]` | Any digit |
| `[!abc]` | Anything except a, b, or c |

**Examples:**

```bash
ls *.txt           # All .txt files
rm file?.log       # file1.log, fileA.log, etc.
cp [A-Z]*.py src/  # Files starting with uppercase
```

### Brace Expansion

Create multiple similar strings:

```bash
# Expand to: file1.txt file2.txt file3.txt
echo file{1,2,3}.txt

# Expand to: file1.txt file2.txt ... file10.txt
echo file{1..10}.txt

# Convert images
convert image.{png,jpg}  # Same as: convert image.png image.jpg

# Create directory structure
mkdir -p project/{src,test,docs}

# Backup with timestamp
cp config.json config.json.{bak,$(date +%Y%m%d)}
```

**Combine with variables:**

```bash
prefix="test"
touch {$prefix}_{1..5}.txt  # test_1.txt through test_5.txt
```

### Advanced Globbing

Enable with `shopt -s globstar`:

```bash
# Match files in subdirectories
ls **/*.py  # All .py files in any subdirectory
```

## Script Template

Here's a good template to start your scripts:

```bash
#!/usr/bin/env bash

# Script: my_script.sh
# Description: What this script does
# Usage: ./my_script.sh [options] arguments

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Default values
VERBOSE=false
OUTPUT_DIR="./output"

# Functions
usage() {
    cat << EOF
Usage: $0 [-v] [-o OUTPUT_DIR] input_file

Options:
    -v              Verbose mode
    -o OUTPUT_DIR   Output directory (default: $OUTPUT_DIR)
    -h              Show this help message

Example:
    $0 -v -o /tmp data.txt
EOF
    exit 1
}

log() {
    if [[ $VERBOSE == true ]]; then
        echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" >&2
    fi
}

# Parse arguments
while getopts "vo:h" opt; do
    case $opt in
        v) VERBOSE=true ;;
        o) OUTPUT_DIR="$OPTARG" ;;
        h) usage ;;
        *) usage ;;
    esac
done

shift $((OPTIND-1))  # Remove parsed options

# Check for required arguments
if [[ $# -lt 1 ]]; then
    echo "Error: Missing input file" >&2
    usage
fi

INPUT_FILE="$1"

# Validate input
if [[ ! -f "$INPUT_FILE" ]]; then
    echo "Error: File not found: $INPUT_FILE" >&2
    exit 1
fi

# Main logic
log "Processing $INPUT_FILE"
mkdir -p "$OUTPUT_DIR"

# Your code here

log "Done!"
```

## Best Practices

### 1. Quote Variables

Always quote variables to handle spaces and special characters:

```bash
# BAD
rm $file

# GOOD
rm "$file"

# BAD
for f in $(ls *.txt); do

# GOOD
for f in *.txt; do
```

### 2. Use `[[` Instead of `[`

```bash
# Prefer this
if [[ -f "$file" ]]; then

# Over this
if [ -f "$file" ]; then
```

### 3. Check for Errors

```bash
# Don't assume commands succeed
mkdir "$dir" || exit 1

# Or use set -e
set -e
mkdir "$dir"
```

### 4. Use shellcheck

Install and run [shellcheck](https://www.shellcheck.net/) on your scripts:

```bash
shellcheck myscript.sh
```

It catches common mistakes like:
* Unquoted variables
* Unreachable code
* Syntax errors
* Portability issues

### 5. Make Scripts Executable

```bash
chmod +x myscript.sh
```

And use a shebang:

```bash
#!/usr/bin/env bash
```

Use `#!/usr/bin/env bash` instead of `#!/bin/bash` for portability.

### 6. Use Functions

Break complex scripts into functions:

```bash
setup() {
    mkdir -p "$WORK_DIR"
    cd "$WORK_DIR"
}

process_file() {
    local file="$1"
    # ... processing logic
}

cleanup() {
    rm -rf "$TEMP_DIR"
}

# Main execution
setup
for file in *.txt; do
    process_file "$file"
done
cleanup
```

### 7. Handle Cleanup with Traps

Ensure cleanup happens even on errors:

```bash
cleanup() {
    rm -rf "$TEMP_DIR"
    echo "Cleaned up temporary files"
}

trap cleanup EXIT  # Run cleanup when script exits

TEMP_DIR=$(mktemp -d)
# ... do work ...
# cleanup runs automatically
```

## Debugging Scripts

### Enable Debug Mode

```bash
# Show commands as they execute
bash -x script.sh

# Or in script
set -x
# ... commands to debug
set +x
```

### Use `echo` Liberally

```bash
echo "DEBUG: variable value is $var"
echo "DEBUG: about to run critical command"
```

### Check Exit Codes

```bash
command
echo "Exit code: $?"
```

### Validate Early

```bash
# Check preconditions
[[ -f "$input" ]] || { echo "Input file missing"; exit 1; }
[[ -w "$output_dir" ]] || { echo "Output dir not writable"; exit 1; }
```

## Common Patterns

### Process Files Safely

```bash
# Handle filenames with spaces
find . -name "*.txt" -print0 | while IFS= read -r -d '' file; do
    process "$file"
done
```

### Retry Logic

```bash
retry() {
    local max_attempts=5
    local attempt=1
    
    until "$@"; do
        if [[ $attempt -ge $max_attempts ]]; then
            echo "Failed after $max_attempts attempts"
            return 1
        fi
        echo "Attempt $attempt failed. Retrying..."
        ((attempt++))
        sleep 2
    done
}

retry curl -f https://example.com/api
```

### Parallel Processing

```bash
# Process files in parallel
for file in *.txt; do
    process_file "$file" &  # & runs in background
done
wait  # Wait for all background jobs
```

### Lock Files

```bash
LOCKFILE="/tmp/myscript.lock"

if [[ -f "$LOCKFILE" ]]; then
    echo "Script already running"
    exit 1
fi

touch "$LOCKFILE"
trap "rm -f '$LOCKFILE'" EXIT

# ... do work ...
```

## Scripts vs Functions

**When to use a script:**
* Reusable across different projects
* Can be any language (Python, Ruby, etc.)
* Needs to be invoked from cron or other tools
* Standalone tool

**When to use a function:**
* Specific to your shell workflow
* Needs to modify shell environment (change directory, set variables)
* Faster to load (loaded once with shell config)
* Part of your personal productivity toolkit

**Example function for your `.bashrc`:**

```bash
# Quickly find and cd to a directory
f() {
    local dir=$(find . -type d -name "*$1*" | head -1)
    if [[ -n "$dir" ]]; then
        cd "$dir"
    else
        echo "Directory not found"
    fi
}
```

## Resources

* [Bash Manual](https://www.gnu.org/software/bash/manual/)
* [ShellCheck](https://www.shellcheck.net/) - Linter for shell scripts
* [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
* [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/)
* [explainshell.com](https://explainshell.com/) - Explains shell commands

## Summary

Shell scripting is about:
* **Automation** - Don't repeat yourself
* **Composition** - Combine small tools
* **Robustness** - Handle errors gracefully
* **Readability** - Future you will thank present you

Start small: automate one annoying task. Build from there. The goal isn't to become a bash expert—it's to make your daily work easier.
