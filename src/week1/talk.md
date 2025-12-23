---
title: "The Art of Lazy Programming"
sub_title: "Week 1 — Shell Scripting: From Commands to Programs"
author: "SeniorMars"
---

<!--
speaker_note: |
  Time budget (60 min):
    0–3   cold open: why scripts beat typing
    3–8   Week 0 recap + "scripts are just saved commands"
    8–23  Demo 1: rename-sanitize.sh (quoting + loops + dry-run)
    23-38 Demo 2: project-scaffold.sh (templates + args + idempotence)
    38-52 Demo 3: fetch-and-cache.sh (conditionals + caching + exit codes)
    52-60 best practices + homework + wrap

  Demo prep:
    - Terminal with good font size
    - Have messy files ready for demo 1
    - Show errors intentionally (teaching moments)
    - Type most things live (not just paste)
-->

<!-- jump_to_middle -->

Three Problems
===

<!-- pause -->

**Problem 1:** Your downloads folder has files like:
```
My Document (final) (2).pdf
photo-IMG_1234 copy.jpg
report   draft--FINAL.docx
```

<!-- pause -->

**Problem 2:** Every new project, you manually create the same folders and files.

<!-- pause -->

**Problem 3:** You keep re-downloading the same file because you forgot where you saved it.

<!-- pause -->

**Today**: We automate all three with shell scripts.

<!-- speaker_note: |
  Hook them with pain points they've all experienced.
-->

<!-- end_slide -->

Last week recap
===

Week 0: You learned to chain commands with pipes.

```bash
grep '^[A-Za-z]{5}$' dict.txt | tr '[:upper:]' '[:lower:]' | sort -u
```

<!-- pause -->

**Today**: We turn these one-liners into reusable programs.

<!-- pause -->

Scripts are just:
- Commands you type once
- Save to a file
- Run whenever you need them

<!-- pause -->

The lazy programmer's motto: **Type it once, run it forever.**

<!-- end_slide -->

What you'll learn today
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Skills:**
- Variables and arguments
- Quoting (why it matters)
- Conditionals
- Loops
- Functions
- Error handling

<!-- column: 1 -->

**Patterns:**
- Dry-run mode
- Idempotent scripts
- Caching
- Safe defaults
- Logging

<!-- reset_layout -->

<!-- pause -->

By the end: you'll write scripts that are safer and more reliable than most code you've seen.

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 1
===

**rename-sanitize.sh**

*Your downloads folder is a nightmare. Let's fix it.*

<!-- end_slide -->

The problem
===

You download files from the internet:

```bash
$ ls Downloads/
My Document (final) (2).pdf
photo-IMG_1234 copy.jpg
Screenshot 2025-01-20 at 3.47.23 PM.png
research paper   draft--FINAL.docx
```

<!-- pause -->

Spaces. Parentheses. Multiple dashes. Chaos.

<!-- pause -->

**Goal:** Normalize all filenames to:
- Lowercase
- No spaces (use underscores)
- No weird punctuation
- Safe to use in scripts

<!-- end_slide -->

First attempt (naive)
===

<!-- column_layout: [3, 2] -->

<!-- column: 0 -->

```bash
#!/bin/bash
# BAD VERSION - DO NOT USE

for file in *.pdf; do
    newname=$(echo $file | tr ' ' '_')
    mv $file $newname
done
```

<!-- column: 1 -->

**Problems:**
- What if filename has spaces?
- No undo button
- Silent failures
- No verification

<!-- reset_layout -->

<!-- pause -->

Let's build a better version, step by step.

<!-- speaker_note: |
  This is intentionally bad. Show why quoting matters.
-->

<!-- end_slide -->

Step 1: Script skeleton
===

```bash
#!/usr/bin/env bash

set -euo pipefail  # Exit on errors, undefined vars, pipe failures

echo "File renaming tool"
echo "Usage: $0 [directory]"
```

<!-- pause -->

**Key parts:**
- `#!/usr/bin/env bash` - Portable shebang
- `set -euo pipefail` - Safety first
- `$0` - Script name (special variable)

<!-- pause -->

Save as `rename-sanitize.sh` and make executable:
```bash
chmod +x rename-sanitize.sh
```

<!-- end_slide -->

Step 2: Accept a directory argument
===

```bash
#!/usr/bin/env bash
set -euo pipefail

DIR="${1:-.}"  # Use first argument, or current directory if none

echo "Processing directory: $DIR"

if [[ ! -d "$DIR" ]]; then
    echo "Error: $DIR is not a directory" >&2
    exit 1
fi
```

<!-- pause -->

**New concepts:**
- `${1:-.}` - Default value (argument 1, or `.` if empty)
- `[[ ! -d "$DIR" ]]` - Test if NOT a directory
- `>&2` - Print to stderr
- `exit 1` - Exit with error code

<!-- speaker_note: |
  Explain: stdin, stdout, stderr. Exit codes matter for automation.
-->

<!-- end_slide -->

Step 3: Loop over files (WRONG WAY)
===

```bash
# DON'T DO THIS
for file in $DIR/*; do
    echo "$file"
done
```

<!-- pause -->

**What's wrong?**

Test it:
```bash
touch "test file.txt"
./rename-sanitize.sh .
```

<!-- pause -->

Output:
```
test
file.txt
```

<!-- pause -->

**The space broke it!** This is why quoting matters.

<!-- end_slide -->

Step 3: Loop over files (RIGHT WAY)
===

```bash
# DO THIS
for file in "$DIR"/*; do
    echo "$file"
done
```

<!-- pause -->

Now:
```
./test file.txt
```

<!-- pause -->

**Rule:** Always quote variables.

**Exception:** When you WANT word splitting (rare).

<!-- speaker_note: |
  This is THE critical lesson. Emphasize it.
-->

<!-- end_slide -->

Step 4: Extract filename
===

```bash
for file in "$DIR"/*; do
    # Skip if not a regular file
    [[ -f "$file" ]] || continue
    
    # Get just the filename (no path)
    basename=$(basename "$file")
    
    echo "Processing: $basename"
done
```

<!-- pause -->

**New tool:** `basename` strips the directory path.

```bash
basename "/path/to/My File.txt"  # Output: My File.txt
```

<!-- end_slide -->

Step 5: Transform the name
===

```bash
for file in "$DIR"/*; do
    [[ -f "$file" ]] || continue
    
    basename=$(basename "$file")
    
    # Transform: lowercase, spaces to underscores, remove parens
    newname=$(echo "$basename" | \
              tr '[:upper:]' '[:lower:]' | \
              tr ' ' '_' | \
              tr -d '()')
    
    echo "$basename → $newname"
done
```

<!-- pause -->

**Tools used:**
- `tr '[:upper:]' '[:lower:]'` - Lowercase
- `tr ' ' '_'` - Replace spaces
- `tr -d '()'` - Delete parentheses

<!-- end_slide -->

Test the transformations
===

Create some test files:

```bash
touch "Test File (2).txt"
touch "SCREAMING_NAME.pdf"
touch "normal-file.txt"
```

<!-- pause -->

Run the script:
```bash
$ ./rename-sanitize.sh .
Test File (2).txt → test_file_2.txt
SCREAMING_NAME.pdf → screaming_name.pdf
normal-file.txt → normal-file.txt
```

<!-- pause -->

**Good!** But it's not actually renaming yet.

That's intentional...

<!-- end_slide -->

Step 6: Dry-run mode
===

**Never rename files without checking first.**

```bash
DRY_RUN=true  # Set to false to actually rename

for file in "$DIR"/*; do
    [[ -f "$file" ]] || continue
    
    basename=$(basename "$file")
    newname=$(echo "$basename" | tr '[:upper:]' '[:lower:]' | \
              tr ' ' '_' | tr -d '()')
    
    if [[ "$basename" != "$newname" ]]; then
        echo "$basename → $newname"
        
        if [[ "$DRY_RUN" == "false" ]]; then
            mv "$DIR/$basename" "$DIR/$newname"
        fi
    fi
done
```

<!-- pause -->

**Pattern:** Show what WOULD happen before doing it.

<!-- end_slide -->

Step 7: Add a flag for real execution
===

```bash
#!/usr/bin/env bash
set -euo pipefail

DRY_RUN=true
DIR="${1:-.}"

# Check for --execute flag
if [[ "${2:-}" == "--execute" ]]; then
    DRY_RUN=false
    echo "⚠️  REAL MODE - Files will be renamed!"
else
    echo "Dry-run mode (use --execute to actually rename)"
fi

# ... rest of script
```

<!-- pause -->

**Safe default:** Dry-run unless explicitly told otherwise.

<!-- end_slide -->

The complete script
===

```bash
#!/usr/bin/env bash
set -euo pipefail

DRY_RUN=true
DIR="${1:-.}"

[[ "${2:-}" == "--execute" ]] && DRY_RUN=false

[[ -d "$DIR" ]] || { echo "Error: Not a directory: $DIR" >&2; exit 1; }

echo "Processing: $DIR (dry-run: $DRY_RUN)"

for file in "$DIR"/*; do
    [[ -f "$file" ]] || continue
    
    basename=$(basename "$file")
    newname=$(echo "$basename" | tr '[:upper:]' '[:lower:]' | \
              tr ' ' '_' | tr -d '()')
    
    if [[ "$basename" != "$newname" ]]; then
        echo "$basename → $newname"
        [[ "$DRY_RUN" == "false" ]] && mv "$DIR/$basename" "$DIR/$newname"
    fi
done

echo "Done!"
```

<!-- end_slide -->

Demo: Running it
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Dry-run (default):**
```bash
$ ./rename-sanitize.sh ~/Downloads
My Doc (2).pdf → my_doc_2.pdf
Test File.txt → test_file.txt

Dry-run mode
```

<!-- column: 1 -->

**Real execution:**
```bash
$ ./rename-sanitize.sh ~/Downloads --execute
My Doc (2).pdf → my_doc_2.pdf
Test File.txt → test_file.txt

⚠️ Files renamed!
```

<!-- reset_layout -->

<!-- pause -->

**Lesson:** Dry-run is a professional pattern. Use it everywhere.

<!-- speaker_note: |
  Show this live if possible. Let them see the safety.
-->

<!-- end_slide -->

What we learned (Demo 1)
===

<!-- incremental_lists: true -->

- **Quoting variables** prevents disasters
- **`set -euo pipefail`** catches errors early
- **Special variables** (`$0`, `$1`, `$2`)
- **Conditionals** (`[[ ]]`)
- **Loops** (`for file in ...`)
- **Command substitution** (`$(command)`)
- **Dry-run pattern** (show before do)

<!-- incremental_lists: false -->

<!-- pause -->

**Key insight:** Scripts should be safer than manual work, not riskier.

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 2
===

**project-scaffold.sh**

*Stop manually creating the same folders every time.*

<!-- end_slide -->

The problem
===

Every project starts the same way:

```bash
$ mkdir my-project
$ cd my-project
$ mkdir src data docs tests
$ touch README.md
$ echo "# My Project" > README.md
$ touch .gitignore
$ echo "*.pyc" >> .gitignore
$ echo "__pycache__/" >> .gitignore
...
```

<!-- pause -->

**This is tedious.**

<!-- pause -->

**Worse:** You forget things. Every project is slightly different.

<!-- pause -->

**Solution:** Automate it.

<!-- end_slide -->

Goal: One command to set up everything
===

We want:

```bash
$ ./project-scaffold.sh my-awesome-project
Created: my-awesome-project/
  ├── src/
  ├── data/
  ├── docs/
  ├── tests/
  ├── README.md
  ├── .gitignore
  └── notes.md

Project ready!
```

<!-- pause -->

Bonus: If you run it twice, it doesn't break things.

**Idempotence** - safe to run multiple times.

<!-- end_slide -->

Step 1: Parse arguments
===

```bash
#!/usr/bin/env bash
set -euo pipefail

# Check for project name argument
if [[ $# -eq 0 ]]; then
    echo "Usage: $0 PROJECT_NAME" >&2
    echo "Example: $0 my-cool-project" >&2
    exit 1
fi

PROJECT_NAME="$1"

echo "Creating project: $PROJECT_NAME"
```

<!-- pause -->

**New concept:** `$#` - Number of arguments passed

If zero arguments, print usage and exit.

<!-- end_slide -->

Step 2: Create directories safely
===

```bash
PROJECT_DIR="$PROJECT_NAME"

# Create project directory
if [[ -d "$PROJECT_DIR" ]]; then
    echo "Warning: $PROJECT_DIR already exists"
    echo "Adding missing files..."
else
    mkdir "$PROJECT_DIR"
    echo "Created: $PROJECT_DIR/"
fi

# Create subdirectories
mkdir -p "$PROJECT_DIR"/{src,data,docs,tests}

echo "Created subdirectories"
```

<!-- pause -->

**Key tool:** `mkdir -p`
- Creates parent directories as needed
- Doesn't error if directory exists
- Perfect for idempotent scripts

<!-- end_slide -->

Brace expansion reminder
===

This:
```bash
mkdir -p "$PROJECT_DIR"/{src,data,docs,tests}
```

<!-- pause -->

Expands to:
```bash
mkdir -p "$PROJECT_DIR"/src "$PROJECT_DIR"/data \
         "$PROJECT_DIR"/docs "$PROJECT_DIR"/tests
```

<!-- pause -->

**Brace expansion** saves typing.

Other examples:
```bash
touch file{1,2,3}.txt          # file1.txt file2.txt file3.txt
cp file.{txt,bak}              # cp file.txt file.bak
mv project/{old,new}_name.py   # rename
```

<!-- end_slide -->

Step 3: Create README with template
===

Here's where it gets interesting:

```bash
README="$PROJECT_DIR/README.md"

if [[ ! -f "$README" ]]; then
    cat > "$README" << 'EOF'
# PROJECT_NAME_PLACEHOLDER

## Overview
Describe your project here.

## Setup
Installation instructions.

## Usage
How to use this project.

## License
MIT
EOF
    
    # Replace placeholder with actual name
    sed -i "s/PROJECT_NAME_PLACEHOLDER/$PROJECT_NAME/g" "$README"
    
    echo "Created: README.md"
fi
```

<!-- end_slide -->

Here documents explained
===

**Here document** (`<<EOF ... EOF`) creates multi-line input:

```bash
cat > file.txt << 'EOF'
Line 1
Line 2
Variable: $HOME
EOF
```

<!-- pause -->

**Key points:**
- `'EOF'` (quoted) - Variables NOT expanded
- `EOF` (unquoted) - Variables ARE expanded
- Useful for creating templates

<!-- pause -->

Compare:
```bash
cat << EOF
Home: $HOME
EOF
# Output: Home: /home/user

cat << 'EOF'
Home: $HOME
EOF
# Output: Home: $HOME
```

<!-- end_slide -->

Step 4: Create .gitignore
===

```bash
GITIGNORE="$PROJECT_DIR/.gitignore"

if [[ ! -f "$GITIGNORE" ]]; then
    cat > "$GITIGNORE" << 'EOF'
# Python
__pycache__/
*.pyc
*.pyo
*.egg-info/
.venv/

# Editor
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Data
data/*.csv
data/*.json
!data/README.md
EOF
    
    echo "Created: .gitignore"
fi
```

<!-- pause -->

**Pattern:** Check if file exists before creating.

<!-- end_slide -->

Step 5: Add notes file
===

```bash
NOTES="$PROJECT_DIR/notes.md"

if [[ ! -f "$NOTES" ]]; then
    cat > "$NOTES" << EOF
# Notes for $PROJECT_NAME

Started: $(date +"%Y-%m-%d")

## Todo
- [ ] Task 1
- [ ] Task 2

## Ideas

## Questions

EOF
    
    echo "Created: notes.md"
fi
```

<!-- pause -->

**Notice:** `$(date)` gets expanded because `EOF` is unquoted.

<!-- end_slide -->

The complete script
===

```bash
#!/usr/bin/env bash
set -euo pipefail

[[ $# -eq 0 ]] && { echo "Usage: $0 PROJECT_NAME" >&2; exit 1; }

PROJECT_NAME="$1"
PROJECT_DIR="$PROJECT_NAME"

echo "Setting up project: $PROJECT_NAME"

# Create directories
[[ -d "$PROJECT_DIR" ]] || mkdir "$PROJECT_DIR"
mkdir -p "$PROJECT_DIR"/{src,data,docs,tests}

# README
if [[ ! -f "$PROJECT_DIR/README.md" ]]; then
    cat > "$PROJECT_DIR/README.md" << EOF
# $PROJECT_NAME

Created: $(date +"%Y-%m-%d")
EOF
fi

# .gitignore
[[ -f "$PROJECT_DIR/.gitignore" ]] || \
    echo -e "__pycache__/\n*.pyc\n.DS_Store" > "$PROJECT_DIR/.gitignore"

# notes
[[ -f "$PROJECT_DIR/notes.md" ]] || \
    echo "# Notes - $(date)" > "$PROJECT_DIR/notes.md"

echo "✓ Project ready: $PROJECT_DIR"
```

<!-- end_slide -->

Demo: Running it
===

```bash
$ ./project-scaffold.sh demo-project
Setting up project: demo-project
Created directories
Created: README.md
Created: .gitignore
Created: notes.md
✓ Project ready: demo-project
```

<!-- pause -->

Check what was created:
```bash
$ tree demo-project
demo-project/
├── README.md
├── data/
├── docs/
├── notes.md
├── src/
└── tests/

$ cat demo-project/README.md
# demo-project

Created: 2025-01-20
```

<!-- speaker_note: |
  Show this live. Let them see the instant gratification.
-->

<!-- end_slide -->

Test idempotence
===

Run it again:

```bash
$ ./project-scaffold.sh demo-project
Warning: demo-project already exists
Adding missing files...
✓ Project ready: demo-project
```

<!-- pause -->

**No errors. No duplicates. Safe to re-run.**

<!-- pause -->

Delete the README and run again:
```bash
$ rm demo-project/README.md
$ ./project-scaffold.sh demo-project
Created: README.md
✓ Project ready: demo-project
```

<!-- pause -->

**Idempotent:** Brings project to correct state, regardless of current state.

<!-- end_slide -->

What we learned (Demo 2)
===

<!-- incremental_lists: true -->

- **Argument validation** (`$#`, usage messages)
- **mkdir -p** for safe directory creation
- **Brace expansion** for efficiency
- **Here documents** for templates
- **Idempotence** (safe to run multiple times)
- **`[[ -f ]]`** to check file existence

<!-- incremental_lists: false -->

<!-- pause -->

**Key insight:** Good scripts are robust, not clever.

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 3
===

**fetch-and-cache.sh**

*Stop re-downloading the same file.*

<!-- end_slide -->

The problem
===

You're working on a script that needs a data file from the internet:

```bash
#!/bin/bash
# Process word list

curl -s https://example.com/words.txt | grep "^a" | wc -l
```

<!-- pause -->

**Every time you run this:**
- Downloads the file
- Uses bandwidth
- Takes time
- Hits the server

<!-- pause -->

**Better:** Download once, cache locally, reuse.

<!-- end_slide -->

Goal: Smart caching
===

We want:

```bash
$ ./fetch-and-cache.sh https://example.com/words.txt
Downloading...
Cached: /tmp/cache/words.txt

$ ./fetch-and-cache.sh https://example.com/words.txt
Using cached file: /tmp/cache/words.txt
```

<!-- pause -->

Bonus features:
- Respect cache expiry (24 hours)
- Force refresh with flag
- Return the cached file path (for piping)

<!-- end_slide -->

Step 1: Set up caching
===

```bash
#!/usr/bin/env bash
set -euo pipefail

CACHE_DIR="${HOME}/.cache/fetch-cache"
CACHE_TTL=86400  # 24 hours in seconds

# Create cache directory
mkdir -p "$CACHE_DIR"

echo "Cache directory: $CACHE_DIR"
```

<!-- pause -->

**Design decisions:**
- Cache in user's home directory
- Use hidden directory (`.cache`)
- Make it configurable (environment variable)

<!-- pause -->

Users can override:
```bash
CACHE_DIR=/tmp/cache ./fetch-and-cache.sh URL
```

<!-- end_slide -->

Step 2: Parse arguments
===

```bash
FORCE_REFRESH=false
URL=""

# Parse arguments
while [[ $# -gt 0 ]]; do
    case "$1" in
        --force|-f)
            FORCE_REFRESH=true
            shift
            ;;
        *)
            URL="$1"
            shift
            ;;
    esac
done

# Validate URL provided
if [[ -z "$URL" ]]; then
    echo "Usage: $0 [--force] URL" >&2
    exit 1
fi
```

<!-- pause -->

**New pattern:** `while` loop with `case` for argument parsing.

<!-- end_slide -->

Case statement explained
===

```bash
case "$1" in
    --force|-f)
        FORCE_REFRESH=true
        ;;
    --help|-h)
        print_help
        ;;
    *)
        URL="$1"
        ;;
esac
```

<!-- pause -->

**Like `if/elif/else` but for pattern matching:**
- Matches first pattern that fits
- `|` means OR
- `*)` is the default case
- `;;` ends each case

<!-- end_slide -->

Step 3: Generate cache filename
===

```bash
# Convert URL to safe filename
# https://example.com/data.txt → example.com_data.txt

CACHE_FILE="$CACHE_DIR/$(echo "$URL" | \
    sed 's|https\?://||' | \
    tr '/' '_' | \
    tr -d '?&=')"

echo "Cache file: $CACHE_FILE"
```

<!-- pause -->

**Transformations:**
1. Remove `http://` or `https://`
2. Replace `/` with `_`
3. Remove query parameters (`?`, `&`, `=`)

<!-- pause -->

Examples:
```
https://example.com/data.txt  → example.com_data.txt
http://api.com/v1/users       → api.com_v1_users
```

<!-- end_slide -->

Step 4: Check if cache is fresh
===

```bash
is_cache_fresh() {
    local file="$1"
    
    # File doesn't exist
    [[ ! -f "$file" ]] && return 1
    
    # Get file age in seconds
    if [[ "$OSTYPE" == "darwin"* ]]; then
        # macOS
        local file_time=$(stat -f %m "$file")
    else
        # Linux
        local file_time=$(stat -c %Y "$file")
    fi
    
    local current_time=$(date +%s)
    local age=$((current_time - file_time))
    
    # Fresh if age < TTL
    [[ $age -lt $CACHE_TTL ]]
}
```

<!-- pause -->

**Function** for reusable logic.

**Return codes:** 0 = fresh, 1 = stale

<!-- end_slide -->

Functions in bash
===

```bash
# Define function
greet() {
    local name="$1"  # local variable
    echo "Hello, $name"
}

# Call function
greet "Alice"  # Output: Hello, Alice
```

<!-- pause -->

**Key points:**
- No `function` keyword needed (but allowed)
- Arguments: `$1`, `$2`, etc. (just like scripts)
- `local` makes variables function-scoped
- `return` sets exit code (0-255)
- Functions run in current shell (can modify environment)

<!-- end_slide -->

Step 5: Download or use cache
===

```bash
if [[ "$FORCE_REFRESH" == "true" ]]; then
    echo "Force refresh requested" >&2
    rm -f "$CACHE_FILE"
fi

if is_cache_fresh "$CACHE_FILE"; then
    echo "Using cached file (age: $(( ($(date +%s) - $(stat -c %Y "$CACHE_FILE")) / 3600 ))h)" >&2
else
    echo "Downloading: $URL" >&2
    
    if curl -fsSL "$URL" -o "$CACHE_FILE"; then
        echo "Cached: $CACHE_FILE" >&2
    else
        echo "Download failed!" >&2
        rm -f "$CACHE_FILE"  # Remove partial download
        exit 1
    fi
fi

# Output the cached file path (for piping)
echo "$CACHE_FILE"
```

<!-- pause -->

**Notice:** Logs to stderr, output to stdout.

This lets you use it in pipelines!

<!-- end_slide -->

Why stderr vs stdout matters
===

**stdout** (1): Data/results
**stderr** (2): Logs/messages

<!-- pause -->

This lets you do:

```bash
# Get the file and process it
words=$(./fetch-and-cache.sh https://example.com/words.txt)
grep "^a" "$words" | wc -l
```

<!-- pause -->

Or:
```bash
# Pipe the cached file directly
cat $(./fetch-and-cache.sh URL) | process
```

<!-- pause -->

**The script talks to you (stderr) while outputting data (stdout).**

Professional scripts separate these!

<!-- speaker_note: |
  This is subtle but important. Emphasize the pipeline pattern.
-->

<!-- end_slide -->

The complete script
===

```bash
#!/usr/bin/env bash
set -euo pipefail

CACHE_DIR="${HOME}/.cache/fetch-cache"
CACHE_TTL=86400
mkdir -p "$CACHE_DIR"

FORCE_REFRESH=false
URL=""

while [[ $# -gt 0 ]]; do
    case "$1" in
        -f|--force) FORCE_REFRESH=true; shift ;;
        *) URL="$1"; shift ;;
    esac
done

[[ -z "$URL" ]] && { echo "Usage: $0 [-f] URL" >&2; exit 1; }

CACHE_FILE="$CACHE_DIR/$(echo "$URL" | sed 's|https\?://||' | tr '/' '_')"

is_fresh() {
    [[ -f "$1" ]] || return 1
    local age=$(( $(date +%s) - $(stat -c %Y "$1") ))
    [[ $age -lt $CACHE_TTL ]]
}

[[ "$FORCE_REFRESH" == "true" ]] && rm -f "$CACHE_FILE"

if is_fresh "$CACHE_FILE"; then
    echo "Using cache" >&2
else
    echo "Downloading..." >&2
    curl -fsSL "$URL" -o "$CACHE_FILE" || { rm -f "$CACHE_FILE"; exit 1; }
fi

echo "$CACHE_FILE"
```

<!-- end_slide -->

Demo: Using it
===

**First run:**
```bash
$ ./fetch-and-cache.sh https://raw.githubusercontent.com/dwyl/english-words/master/words_alpha.txt
Downloading: https://...
Cached: /home/user/.cache/fetch-cache/raw.githubusercontent.com_...

$ wc -l /home/user/.cache/fetch-cache/raw.githubusercontent.com_...
370103
```

<!-- pause -->

**Second run (immediate):**
```bash
$ ./fetch-and-cache.sh https://...
Using cached file (age: 0h)
/home/user/.cache/fetch-cache/raw.githubusercontent.com_...
```

<!-- pause -->

**Force refresh:**
```bash
$ ./fetch-and-cache.sh --force https://...
Force refresh requested
Downloading: https://...
```

<!-- end_slide -->

Use it in pipelines
===

**Process a remote file without thinking about caching:**

```bash
# Get words starting with 'z'
cat $(./fetch-and-cache.sh $URL) | grep "^z" | wc -l
```

<!-- pause -->

**Or wrap it in another script:**

```bash
#!/usr/bin/env bash
# analyze-words.sh

WORDS=$(./fetch-and-cache.sh https://example.com/words.txt)

echo "Total words: $(wc -l < "$WORDS")"
echo "Words with 'q': $(grep -c 'q' "$WORDS")"
echo "Longest word: $(awk '{print length, $0}' "$WORDS" | sort -n | tail -1)"
```

<!-- pause -->

**The cache is invisible to the user.**

<!-- end_slide -->

What we learned (Demo 3)
===

<!-- incremental_lists: true -->

- **Functions** for reusable logic
- **Case statements** for argument parsing
- **File age checking** with `stat`
- **Conditionals** (`if`/`else`)
- **Exit codes** for function returns
- **stderr vs stdout** separation
- **Caching pattern** (check, download, serve)

<!-- incremental_lists: false -->

<!-- pause -->

**Key insight:** Good scripts think ahead and save future-you time.

<!-- end_slide -->

<!-- jump_to_middle -->

Best Practices
===

*Lessons from all three demos*

<!-- end_slide -->

The script template
===

Every script should start like this:

```bash
#!/usr/bin/env bash

set -euo pipefail

# Brief description
# Usage: ./script.sh [options] arguments

# ... your code ...
```

<!-- pause -->

**`set -euo pipefail` breakdown:**
- `set -e` - Exit if any command fails
- `set -u` - Exit if undefined variable used
- `set -o pipefail` - Catch errors in pipes

<!-- pause -->

**Together:** Catch bugs before they cause damage.

<!-- end_slide -->

Always quote variables
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**BAD:**
```bash
file="my file.txt"
rm $file
```

Tries to delete `my` and `file.txt`

<!-- column: 1 -->

**GOOD:**
```bash
file="my file.txt"
rm "$file"
```

Deletes `my file.txt`

<!-- reset_layout -->

<!-- pause -->

**Exception:** When you want word splitting (rare):
```bash
flags="-l -a -h"
ls $flags  # Intended
```

But usually:
```bash
flags=("-l" "-a" "-h")
ls "${flags[@]}"  # Better
```

<!-- end_slide -->

Check before destructive actions
===

```bash
# BAD
rm -rf "$directory"

# GOOD
if [[ -d "$directory" ]]; then
    echo "About to delete: $directory"
    rm -rf "$directory"
else
    echo "Directory not found: $directory" >&2
    exit 1
fi
```

<!-- pause -->

**Even better:** Dry-run mode (like Demo 1).

<!-- end_slide -->

Validate inputs early
===

```bash
#!/usr/bin/env bash
set -euo pipefail

# Check argument count
[[ $# -lt 1 ]] && { echo "Usage: $0 FILE" >&2; exit 1; }

FILE="$1"

# Validate file exists
[[ -f "$FILE" ]] || { echo "Error: File not found: $FILE" >&2; exit 1; }

# Validate file readable
[[ -r "$FILE" ]] || { echo "Error: Cannot read: $FILE" >&2; exit 1; }

# Now safe to process
# ...
```

<!-- pause -->

**Fail fast:** Catch problems before doing work.

<!-- end_slide -->

Use functions for clarity
===

**Instead of:**
```bash
# Long script with repeated logic
grep "ERROR" log1.txt | wc -l
grep "ERROR" log2.txt | wc -l
grep "ERROR" log3.txt | wc -l
```

<!-- pause -->

**Do:**
```bash
count_errors() {
    local file="$1"
    grep -c "ERROR" "$file" || echo 0
}

count_errors log1.txt
count_errors log2.txt
count_errors log3.txt
```

<!-- pause -->

**Benefits:** DRY (Don't Repeat Yourself), testable, readable.

<!-- end_slide -->

Use shellcheck
===

**Always run your scripts through shellcheck:**

```bash
shellcheck myscript.sh
```

<!-- pause -->

**Example warnings:**
```
Line 5: rm $file
        ^-- SC2086: Double quote to prevent word splitting

Line 12: if [ $count -eq 0 ]; then
            ^-- SC2086: Double quote to prevent word splitting
            ^-- SC2003: Use [[ ]] instead of [ ]
```

<!-- pause -->

**Fix the issues:**
```bash
rm "$file"
if [[ "$count" -eq 0 ]]; then
```

<!-- pause -->

**ShellCheck is like a patient teacher.** Listen to it.

<!-- end_slide -->

Document your scripts
===

```bash
#!/usr/bin/env bash
# Script: fetch-and-cache.sh
# Description: Downloads and caches files from URLs
# Usage: ./fetch-and-cache.sh [--force] URL
#
# Options:
#   --force    Force re-download even if cached
#
# Examples:
#   ./fetch-and-cache.sh https://example.com/data.txt
#   ./fetch-and-cache.sh --force https://example.com/data.txt
#
# Cache location: ~/.cache/fetch-cache
# Cache TTL: 24 hours

set -euo pipefail

# ... code ...
```

<!-- pause -->

**Future-you will thank present-you.**

<!-- end_slide -->

Summary
===

**Today you learned:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Concepts:**
- Variables and quoting
- Arguments and flags
- Conditionals
- Loops
- Functions
- Exit codes

<!-- column: 1 -->

**Patterns:**
- Dry-run mode
- Idempotent scripts
- Caching
- Input validation
- stderr vs stdout

<!-- reset_layout -->

<!-- pause -->

**Most important:** Scripts should make your life easier, not harder.

<!-- end_slide -->

<!-- jump_to_middle -->

Homework
===

<!-- end_slide -->

Week 1 Homework
===

**Time limit: 1.5 hours**

**7 exercises** (5 required, 2 bonus)

<!-- pause -->

**Exercises:**
1. Custom `ls` command (15 min)
2. Semester setup script (20 min)
3. `marco` and `polo` navigation (20 min)
4. Retry script (25 min)
5. HTML archive creator (20 min)
6. Log analyzer (20 min)
7. **Bonus:** Recent file finder (10 min)

<!-- pause -->

Full details on the course website.

<!-- end_slide -->

Key homework tips
===

<!-- incremental_lists: true -->

- **Start with the template** (shebang + `set -euo pipefail`)
- **Test incrementally** (don't write everything then run)
- **Use shellcheck** before submitting
- **Quote your variables**
- **Check `$?` to debug** exit codes
- **Stop if it takes too long** - ask for help!

<!-- incremental_lists: false -->

<!-- pause -->

**Remember:** The goal is learning, not perfection.

<!-- end_slide -->

Next week
===

**Week 2: Data Wrangling**

We'll dive deep into:
- **Regular expressions** (pattern matching on steroids)
- **sed** (stream editor)
- **awk** (text processing language)
- **Real-world data cleaning**

<!-- pause -->

**Preview:** We'll process real log files, CSV data, and messy text.

<!-- pause -->

**For next time:** Install `ripgrep` and `jq` (instructions on website).

<!-- end_slide -->

Final thoughts
===

Shell scripting is not about being fancy.

<!-- pause -->

It's about:
- **Automating boring tasks**
- **Making your work reproducible**
- **Building tools you actually use**

<!-- pause -->

Start small:
- Automate one annoying thing this week
- Turn a repeated command into a script
- Share it with a teammate

<!-- pause -->

**The best script is the one you actually use.**

<!-- end_slide -->

Questions?
===

**Common ones:**

*"When should I use bash vs Python?"*
→ Bash for shell tasks, Python for logic/data structures.

<!-- pause -->

*"How do I debug when my script breaks?"*
→ `bash -x script.sh` shows every command.

<!-- pause -->

*"Is it okay to use Google?"*
→ Yes! But understand what you're copying.

<!-- pause -->

**Office hours:** Bring your broken scripts!

<!-- end_slide -->

<!-- jump_to_middle -->

Go automate something
===

**Homework due**: Next Monday

**Practice**: Turn one annoying task into a script this week

**Next**: Data wrangling with sed, awk, and regex

<!-- pause -->

---

**Remember: The lazy programmer is the efficient programmer.**

<!-- end_slide -->
