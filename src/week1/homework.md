# Week 1 Homework - Shell Scripting

**Time estimate**: 1.5 hours maximum

**Submission**: Create a directory `week1-[netid]` with your solutions and add it as a submodule to the class repo.


```admonish
Make sure you check out week1 installation instructions before starting this homework.
```


---

## Setup

Create your homework directory:

```bash
mkdir week1-[netid]
cd week1-[netid]
```

All scripts should be executable (`chmod +x script.sh`) and include a proper shebang (`#!/usr/bin/env bash`).

---

## Exercise 1: Custom `ls` (15 minutes)

Read `man ls` and write an `ls` command that lists files with the following properties:

* Includes **all files**, including hidden files
* Sizes are in **human-readable format** (e.g., `454M` instead of `454279954`)
* Files are ordered by **recency** (most recently modified first)
* Output is **colorized**

Save your command in a file called `ex1_command.txt`.

**Sample output:**
```
-rw-r--r--   1 user group 1.1M Jan 14 09:53 baz
drwxr-xr-x   5 user group  160 Jan 14 09:53 .
-rw-r--r--   1 user group  514 Jan 14 06:42 bar
-rw-r--r--   1 user group 106M Jan 13 12:12 foo
drwx------+ 47 user group 1.5K Jan 12 18:08 ..
```

**Hint**: Look for flags related to "all", "human-readable", "time", and "color" in the man page.

---

## Exercise 2: Semester Setup Script (20 minutes)

Write a bash script called `semester` that creates a directory structure for organizing coursework.

**Requirements:**

The script should:
1. Take a semester name as an argument (e.g., `fall2025`)
2. Create the following directory structure:
   ```
   fall2025/
   ├── comp140/
   ├── comp182/
   ├── math212/
   └── projects/
   ```
3. Create a `README.md` file in the root directory with:
   * The semester name
   * The current date (use `date`)
   * A placeholder for course descriptions

**Example usage:**
```bash
./semester fall2025
```

**Expected output:**
```
Created semester directory: fall2025
Created course directories: comp140, comp182, math212, projects
Created README.md
```

**Bonus**: Make the course names configurable via additional arguments.

---

## Exercise 3: `marco` and `polo` (20 minutes)

Write two bash functions that work together for directory navigation:

* **`marco`** - Saves the current working directory
* **`polo`** - Returns you to the directory where you last executed `marco`

**Requirements:**
1. Create a file called `navigation.sh`
2. Implement both functions
3. The saved directory should persist across terminal sessions (hint: save to a file)
4. If `polo` is called before `marco`, print a helpful error message

**Example usage:**
```bash
$ source navigation.sh
$ cd /usr/local/bin
$ marco
Saved current directory
$ cd ~
$ cd Documents
$ pwd
/home/user/Documents
$ polo
$ pwd
/usr/local/bin
```

**Hint**: You'll need to save the directory to a file in your home directory (like `~/.marco_location`).

---

## Exercise 4: Retry Script (25 minutes)

Write a bash script called `retry.sh` that runs a command until it fails, then reports statistics.

**The script should:**
1. Take another script as an argument
2. Run it repeatedly until it exits with a non-zero status
3. Capture both `stdout` and `stderr` to separate files
4. Report:
   * Number of successful runs before failure
   * The final error message
   * Total time elapsed

**Test it with this script** (save as `flaky.sh`):

```bash
#!/usr/bin/env bash

n=$(( RANDOM % 100 ))

if [[ $n -eq 42 ]]; then
   echo "Something went wrong"
   >&2 echo "The error was using magic numbers"
   exit 1
fi

echo "Everything went according to plan"
```

**Example usage:**
```bash
$ ./retry.sh ./flaky.sh
Run 1: success
Run 2: success
Run 3: success
...
Run 47: FAILED

Statistics:
- Successful runs: 46
- Failed on run: 47
- Total time: 2.3 seconds

Error output:
The error was using magic numbers

Standard output from failed run:
Something went wrong
```

**Hints:**
* Use a while loop with a counter
* Check `$?` after each run
* Use `date +%s` to track time
* Redirect output: `command > stdout.log 2> stderr.log`

---

## Exercise 5: HTML Archive Creator (20 minutes)

Write a command (can be a one-liner or script called `archive_html.sh`) that:

1. Recursively finds all HTML files in the current directory
2. Creates a tar.gz archive containing them
3. Handles filenames with spaces correctly
4. Names the archive with today's date: `html_backup_YYYY-MM-DD.tar.gz`

**Requirements:**
* Must work even if files have spaces in their names
* Should use `find` combined with `xargs` or `find -exec`
* Should create the archive in the current directory

**Test setup:**
```bash
mkdir -p test/web/{pages,styles}
touch test/web/index.html
touch test/web/pages/"about us.html"
touch test/web/pages/contact.html
touch test/web/styles/main.css
```

**Expected result:**
```bash
$ ./archive_html.sh
Found 3 HTML files
Created archive: html_backup_2025-01-20.tar.gz
```

**Hints:**
* `find . -name "*.html"` finds HTML files
* Use `find -print0` with `xargs -0` for handling spaces
* Or use `find -exec tar ...`
* Use `date +%Y-%m-%d` for the date format

---

## Exercise 6: Log Analyzer (20 minutes)

Write a script called `analyze_logs.sh` that processes a log file and produces a summary report.

**Given this sample log file** (save as `server.log`):

```
2025-01-20 10:23:15 INFO Server started on port 8080
2025-01-20 10:23:18 INFO Connected to database
2025-01-20 10:24:32 WARNING High memory usage: 85%
2025-01-20 10:25:01 ERROR Failed to connect to redis
2025-01-20 10:25:15 INFO User login: alice@rice.edu
2025-01-20 10:26:42 ERROR Database timeout
2025-01-20 10:27:03 INFO User login: bob@rice.edu
2025-01-20 10:28:15 WARNING Disk space low: 5% remaining
2025-01-20 10:29:01 ERROR Connection refused
2025-01-20 10:30:12 INFO User login: charlie@rice.edu
```

**Your script should output:**

```bash
$ ./analyze_logs.sh server.log

Log Analysis Report
===================
Total lines: 10
INFO messages: 5
WARNING messages: 2
ERROR messages: 3

Most recent error:
2025-01-20 10:29:01 ERROR Connection refused

Unique users logged in: 3
```

**Requirements:**
* Use `grep`, `wc`, and other text processing tools
* Extract and count different log levels
* Find the most recent error
* Count unique user logins

**Hints:**
* `grep -c "pattern"` counts matches
* `tail -1` gets the last line
* Use `cut` or `awk` to extract specific fields
* `sort | uniq | wc -l` counts unique values

---

## Exercise 7 (Bonus/Advanced): Recent File Finder (10 minutes)

Write a script or one-liner that finds and lists the most recently modified files in a directory tree.

**Requirements:**
1. Recursively search from current directory
2. Find the 10 most recently modified files
3. Display: modification time, file size (human-readable), and file path
4. Exclude hidden files and directories

**Example output:**
```bash
$ ./recent_files.sh
Jan 20 14:32  1.2M  ./reports/analysis.pdf
Jan 20 14:15  456K  ./data/results.csv
Jan 20 13:58   12K  ./scripts/process.py
...
```

**Hints:**
* Use `find` with `-type f` and `-mtime`
* Combine with `ls -lht` or use `find -printf`
* Use `head` to limit results
* May need to exclude paths matching `*/.*`

---

## Submission Checklist

Your `week1-[netid]` directory should contain:

```
week1-netid/
├── ex1_command.txt
├── semester
├── navigation.sh
├── retry.sh
├── flaky.sh (provided)
├── archive_html.sh
├── analyze_logs.sh
├── server.log (provided)
└── recent_files.sh (bonus)
```

**Before submitting:**
1. Make all scripts executable: `chmod +x *.sh semester`
2. Test each script works as described
3. Add comments explaining tricky parts
4. Run `shellcheck` if available: `shellcheck *.sh`

**Submit by**: Adding your directory as a git submodule and pushing to your repo

---

## Grading

* Exercises 1-4: **Required** (90%)
* Exercises 5-6: **Required** (10%)
* Exercise 7: **Bonus** (5% extra credit)

**Remember**: If any exercise takes more than the estimated time, stop and ask for help in office hours. The goal is learning, not frustration!

---

## Tips

* Test incrementally - don't write the whole script at once
* Use `echo` statements to debug
* Check exit codes: `echo $?`
* Quote your variables: `"$var"` not `$var`
* Read error messages carefully
* Use `shellcheck` to catch common mistakes

**Good luck, and remember: the best programmers are lazy programmers who automate everything!**
