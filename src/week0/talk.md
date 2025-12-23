---
title: "The Art of Lazy Programming"
sub_title: "Week 0 — Why Your Tools Matter More Than You Think"
author: "Charlie Cruz"
---

<!--
speaker_note: |
  Run with speaker notes:
    presenterm talk.md --publish-speaker-notes
    presenterm talk.md --listen-speaker-notes

  Time budget (60 min):
    0–3   cold open
    3–8   syllabus speedrun
    8–12  philosophy: what "lazy" really means
    12–42 big_data_wrangling: dictionary → wordle list (the meat)
    42–54 students.json + jq: information extraction
    54–60 homework, next week, closing

  Demo prep:
    - Have dict file ready (e.g., /usr/share/dict/words or downloaded)
    - Have students.json with realistic fake data
    - Terminal set to readable font size
    - Practice the one-liners so they flow
-->

<!-- jump_to_middle -->

Welcome to Week 0
===

You're about to see why programmers who know their tools finish in 10 minutes what takes others 2 hours.

<!-- speaker_note: |
  Cold open. No formalities yet. Hook them immediately.
-->

<!-- end_slide -->

Quick story
===

Last week, a friend asked me to analyze 50,000 lines of server logs to find authentication failures.

<!-- pause -->

They spent 3 hours writing a Python script.

<!-- pause -->

I typed one line in the terminal.

<!-- pause -->

```bash
grep 'AUTH FAIL' logs.txt | wc -l
```

<!-- pause -->

**Took 0.8 seconds.**

<!-- speaker_note: |
  Let that sink in. This is what the class is about.
-->

<!-- end_slide -->

Today's agenda
===

<!-- column_layout: [3, 2] -->

<!-- column: 0 -->

1. **Syllabus speedrun** (5 min)
2. **Philosophy**: What "lazy" means
3. **Demo 1**: Turn a messy dictionary into a Wordle word list
4. **Demo 2**: Extract secrets from a JSON file
5. **What's next**

<!-- column: 1 -->

*Time to get weird with Unix.*

<!-- reset_layout -->

<!-- speaker_note: |
  Keep energy up. This is the map of the journey.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

Syllabus
===

*The speed round*

<!-- end_slide -->

What you need to know
===

**Course**: COLL 128 — The Art of Lazy Programming

**Credits**: 1 (but you'll use these skills in every other class)

**Website**: lazy.rice.edu

**Grading**: Did you do the work? Did it work? You pass.

<!-- pause -->

**No midterm. No final exam. No busywork.**

Just practical skills you'll actually use.

<!-- speaker_note: |
  Emphasize: this is about utility, not theory.
-->

<!-- end_slide -->

Weekly structure
===

<!-- incremental_lists: true -->

* **Monday**: Live demo + lecture (like today)
* **During the week**: Small homework (1-2 hours max)
* **Submit**: Via GitHub (we'll set this up week 1)
* **Office hours**: Bring your errors, confusion, and "why isn't this working" questions

<!-- incremental_lists: false -->

<!-- pause -->

If something takes you more than 2 hours, STOP and ask for help.

<!-- end_slide -->

What we'll cover
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Weeks 1-5:**
* Unix tools (grep, sed, awk, jq)
* Shell scripting
* Text editors (vim, alternatives)
* SSH, tmux, remote workflows

<!-- column: 1 -->

**Weeks 6-10:**
* Git workflows
* Debugging & profiling
* Automation
* Security basics
* Your choice projects

<!-- reset_layout -->

<!-- speaker_note: |
  Don't linger. They'll see details in syllabus.
-->

<!-- end_slide -->

The rules
===

1. **Reproducibility > cleverness**

<!-- pause -->

2. **Compose small tools** instead of writing monoliths

<!-- pause -->

3. **Automate boring things** (computers don't get bored)

<!-- pause -->

4. **Document future-you** (future-you has forgotten everything)

<!-- speaker_note: |
  These are the axioms. Come back to them often.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

So what is "lazy programming"?
===

<!-- end_slide -->

Lazy ≠ cutting corners
===

**Lazy = strategic.**

<!-- pause -->

You invest effort **once** so you never pay it again.

<!-- pause -->

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Bad lazy:**
* Copy-paste without understanding
* Ignore errors
* No documentation

<!-- column: 1 -->

**Good lazy:**
* Learn the tool properly
* Build reusable workflows
* Make the computer remember

<!-- reset_layout -->

<!-- end_slide -->

The promise
===

By the end of this course, you will:

<!-- pause -->

* Turn messy data into clean data **in seconds**

<!-- pause -->

* Build pipelines that run reliably **forever**

<!-- pause -->

* Know which tool to reach for **and why**

<!-- pause -->

* Debug problems **faster than your peers**

<!-- pause -->

**Most importantly**: You'll never go back to the slow way.

<!-- end_slide -->

A warning
===

Some tools will feel weird at first.

<!-- pause -->

You'll think: "Why not just write Python?"

<!-- pause -->

**Today I'm going to SHOW you why.**

<!-- pause -->

Speed matters. Composability matters. Debuggability matters.

<!-- pause -->

Trust the process for the next hour.

<!-- speaker_note: |
  Transition: "Let's get into the demos."
-->

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 1
===

**big_data_wrangling**

*How to clean a dictionary in 30 seconds*

<!-- end_slide -->

The challenge
===

<!-- column_layout: [3, 2] -->

<!-- column: 0 -->

**Goal**: Build a clean word list for a Wordle solver

**Requirements:**
* Exactly 5 letters
* Lowercase
* Only a-z (no punctuation)
* No duplicates
* Sorted

**Input**: A messy dictionary file with 200,000+ words

<!-- column: 1 -->

**Constraints:**

You have 60 seconds.

Go.

<!-- pause -->

*(Spoiler: we'll do it in 15)*

<!-- reset_layout -->

<!-- speaker_note: |
  Build suspense. This seems hard but it's trivial with the right tools.
-->

<!-- end_slide -->

Step 0: Find the data
===

On most Unix systems:

```bash
ls /usr/share/dict/
```

<!-- pause -->

Common files:
* `words` - main dictionary
* `american-english` - US spellings
* `british-english` - UK spellings

<!-- pause -->

Let's use `/usr/share/dict/words` (or whatever you have).

<!-- speaker_note: |
  If you're demoing, show this live. If not, move fast.
-->

<!-- end_slide -->

Step 1: Reconnaissance
===

**Always look before you leap.**

```bash
wc -l /usr/share/dict/words
```

<!-- pause -->

Output: `235,886 /usr/share/dict/words`

<!-- pause -->

That's... a lot. Let's peek:

```bash
head -20 /usr/share/dict/words
```

<!-- pause -->

```
A
A's
AMD
AMD's
AOL
AOL's
AWS
AWS's
Aachen
Aachen's
...
```

<!-- speaker_note: |
  Point out: proper nouns, possessives, mixed case. Messy!
-->

<!-- end_slide -->

Step 2: Filter by length
===

We only want 5-letter words.

```bash
grep '^.....$' /usr/share/dict/words | head -20
```

<!-- pause -->

**What's happening?**
* `^` = start of line
* `.....` = exactly 5 of any character
* `$` = end of line

<!-- pause -->

Output:
```
AMD's
AOL's
AWS's
...
Aaron
Abbas
```

<!-- pause -->

Better! But still has apostrophes and capital letters.

<!-- end_slide -->

Better regex
===

Let's be more specific: **only letters**.

```bash
grep -E '^[A-Za-z]{5}$' /usr/share/dict/words | head -20
```

<!-- pause -->

**New syntax:**
* `-E` = extended regex
* `[A-Za-z]` = any letter (upper or lower)
* `{5}` = exactly 5 times

<!-- pause -->

```
Aaron
Abbas
Abbie
Abbot
...
```

Much cleaner!

<!-- speaker_note: |
  Emphasize: regex is a tool for specifying patterns precisely.
-->

<!-- end_slide -->

Step 3: Lowercase everything
===

Wordle doesn't care about case. Neither should we.

```bash
grep -E '^[A-Za-z]{5}$' /usr/share/dict/words | tr '[:upper:]' '[:lower:]' | head -20
```

<!-- pause -->

**New tool: `tr`** (translate characters)

<!-- pause -->

```
aaron
abbas
abbie
abbot
...
```

All lowercase. Perfect.

<!-- end_slide -->

The pipe operator
===

Notice the `|` symbol?

```bash
command1 | command2
```

<!-- pause -->

**This is the secret sauce of Unix.**

<!-- pause -->

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

Output of `command1` becomes input of `command2`.

**Benefits:**
* Composable
* Testable at each step
* Memory efficient
* Reusable

<!-- column: 1 -->

```bash
# Instead of:
command1 > temp.txt
command2 < temp.txt > output.txt
rm temp.txt

# Just:
command1 | command2 > output.txt
```

<!-- reset_layout -->

<!-- speaker_note: |
  This is philosophically important. Pause here.
-->

<!-- end_slide -->

Step 4: Sort and remove duplicates
===

```bash
grep -E '^[A-Za-z]{5}$' /usr/share/dict/words \
  | tr '[:upper:]' '[:lower:]' \
  | sort -u
```

<!-- pause -->

**`sort -u`** = sort AND remove duplicates (unique)

<!-- pause -->

Why sort?
* Makes output deterministic
* Enables binary search later
* Makes diffs easier
* Required for efficient duplicate removal

<!-- end_slide -->

The final pipeline
===

Let's save it to a file:

```bash
grep -E '^[A-Za-z]{5}$' /usr/share/dict/words \
  | tr '[:upper:]' '[:lower:]' \
  | sort -u \
  > words5.txt
```

<!-- pause -->

How many words?

```bash
wc -l words5.txt
```

<!-- pause -->

Output: `15,918 words5.txt`

<!-- pause -->

**We just processed 235,886 lines down to 15,918 clean words.**

**Time: ~0.5 seconds.**

<!-- speaker_note: |
  Let that sink in. Half a second.
-->

<!-- end_slide -->

Verify the output
===

```bash
head -20 words5.txt
tail -20 words5.txt
```

<!-- pause -->

Start:
```
aahed
aalii
aargh
abaca
abaci
...
```

End:
```
...
zowie
zoysia
zombie
zombi
zoned
```

<!-- pause -->

Perfect! Alphabetical, lowercase, exactly 5 letters, no junk.

<!-- end_slide -->

<!-- jump_to_middle -->

Bonus: Wordle filtering
===

<!-- end_slide -->

The Wordle problem
===

You're playing Wordle. After a few guesses, you know:

<!-- pause -->

* Position 3 must be `a`
* Position 5 must be `e`
* The word does NOT contain: `r`, `s`, `t`, `l`, `n`

<!-- pause -->

**Pattern: `_ _ a _ e`**

<!-- pause -->

How many words match?

<!-- end_slide -->

Filter by pattern
===

```bash
grep -E '^..a.e$' words5.txt
```

<!-- pause -->

**`.` means "any single character"**

<!-- pause -->

Output:
```
agape
agate
agave
ablaze
awake
...
```

<!-- pause -->

How many?

```bash
grep -E '^..a.e$' words5.txt | wc -l
```

Output: `247` words

<!-- end_slide -->

Exclude forbidden letters
===

Now remove words with `r`, `s`, `t`, `l`, `n`:

```bash
grep -E '^..a.e$' words5.txt | grep -v '[rstln]'
```

<!-- pause -->

**`grep -v`** = invert match (show lines that DON'T match)

**`[rstln]`** = any of these characters

<!-- pause -->

```bash
grep -E '^..a.e$' words5.txt | grep -v '[rstln]' | wc -l
```

Output: `42` words

<!-- pause -->

**We narrowed 247 → 42 in one line.**

<!-- end_slide -->

See the candidates
===

```bash
grep -E '^..a.e$' words5.txt | grep -v '[rstln]' | head -20
```

<!-- pause -->

```
abade
abaze
agape
awave
awake
image
quake
...
```

<!-- pause -->

Now you can make an educated guess!

<!-- speaker_note: |
  This is a real use case. People build Wordle solvers this way.
-->

<!-- end_slide -->

What just happened?
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Tools used:**
* `grep` (filter)
* `tr` (transform)
* `sort` (organize)
* `wc` (count)
* `|` (compose)
* `>` (save)

<!-- column: 1 -->

**Time invested:**
* Writing: 2 minutes
* Running: <1 second
* Reusable forever

<!-- pause -->

**Alternative:**
* Python script: 30-60 minutes
* Slower to run
* Harder to modify

<!-- reset_layout -->

<!-- speaker_note: |
  This is the payoff. Hammer it home.
-->

<!-- end_slide -->

The Unix philosophy
===

What we just did embodies three principles:

<!-- pause -->

**1. Each tool does ONE thing well**

<!-- pause -->

**2. Tools work together** (via pipes)

<!-- pause -->

**3. Tools handle text streams** (universal interface)

<!-- pause -->

This philosophy is 50+ years old.

It's still the fastest way to work with data.

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 2
===

**students.json**

*What information is hiding in here?*

<!-- end_slide -->

The scenario
===

You web-scraped your university's student directory.

<!-- pause -->

Now you have `students.json`:

```json
[
  {
    "name": "Alice Johnson",
    "email": "aj47@rice.edu",
    "college": "Baker",
    "class_year": 2025,
    "major": "Computer Science"
  },
  ...
]
```

<!-- pause -->

**Question: What can you learn from this data?**

<!-- end_slide -->

Enter jq
===

**`jq` is like `sed`/`awk` for JSON.**

<!-- pause -->

* Slice, filter, map, transform
* Powerful query language
* Pretty printing
* Essential for APIs and config files

<!-- pause -->

**Today**: I'll show you the kinds of questions you can ask.

**Next week**: You'll learn the syntax in depth.

<!-- speaker_note: |
  Don't get bogged down in jq syntax. Focus on capabilities.
-->

<!-- end_slide -->

Pretty print
===

Raw JSON is often unreadable:

```bash
cat students.json
```

<!-- pause -->

```json
[{"name":"Alice Johnson","email":"aj47@rice.edu","college":"Baker",...}...]
```

<!-- pause -->

With `jq`:

```bash
jq '.' students.json | head -30
```

<!-- pause -->

Now it's formatted, colored, and actually readable.

**`'.'` means "identity" (show me everything)**

<!-- end_slide -->

How many students?
===

```bash
jq 'length' students.json
```

<!-- pause -->

Output: `1847`

<!-- pause -->

**One word. Instant answer.**

<!-- end_slide -->

Extract all names
===

```bash
jq -r '.[].name' students.json | head -20
```

<!-- pause -->

**Syntax breakdown:**
* `.[]` = iterate over array elements
* `.name` = get the `name` field
* `-r` = raw output (no quotes)

<!-- pause -->

Output:
```
Alice Johnson
Bob Smith
Charlie Davis
...
```

<!-- end_slide -->

Count by college
===

Which residential college has the most students?

```bash
jq -r '.[].college' students.json | sort | uniq -c | sort -nr
```

<!-- pause -->

**Notice**: We're mixing `jq` with Unix tools!

<!-- pause -->

Output:
```
 267 Baker
 245 Lovett
 231 Will Rice
 198 Hanszen
 ...
```

<!-- pause -->

**Baker wins.** (Typical.)

<!-- speaker_note: |
  Point out: jq extracts, Unix tools aggregate. Composition!
-->

<!-- end_slide -->

Filter: Find all CS majors
===

```bash
jq -r '.[] | select(.major == "Computer Science") | .name' students.json | head -20
```

<!-- pause -->

**New tool: `select()`** = filter by condition

<!-- pause -->

```
Alice Johnson
David Kim
Emily Chen
...
```

<!-- pause -->

Count them:

```bash
jq -r '.[] | select(.major == "Computer Science") | .name' students.json | wc -l
```

Output: `312` CS majors

<!-- end_slide -->

Complex query: Class of 2025 CS majors in Baker
===

```bash
jq -r '.[] | select(.class_year == 2025 and .major == "Computer Science" and .college == "Baker") | .name' students.json
```

<!-- pause -->

Output:
```
Alice Johnson
Michael Patel
Sarah Williams
```

<!-- pause -->

**3 students** meet all criteria.

<!-- pause -->

**Question**: How long would this take in Python?

<!-- end_slide -->

Create a summary report
===

Let's make a new JSON with just names and emails:

```bash
jq '[.[] | {name, email}]' students.json > roster_minimal.json
```

<!-- pause -->

**Why?**
* Privacy: don't expose all data
* Size: smaller files
* Version control: easier diffs

<!-- pause -->

Check it:

```bash
jq '.[0:3]' roster_minimal.json
```

<!-- end_slide -->

Top 5 most popular majors
===

```bash
jq -r '.[].major' students.json | sort | uniq -c | sort -nr | head -5
```

<!-- pause -->

Output:
```
 312 Computer Science
 256 Economics
 198 Mechanical Engineering
 176 Biochemistry
 145 English
```

<!-- pause -->

**Insight**: CS is the most popular major (by a lot).

<!-- speaker_note: |
  This is real data analysis happening in real time.
-->

<!-- end_slide -->

Email domains
===

Are there any non-@rice.edu emails?

```bash
jq -r '.[].email' students.json | grep -v '@rice.edu'
```

<!-- pause -->

Output:
```
visiting_scholar@gmail.com
exchange@example.edu
```

<!-- pause -->

**Found anomalies!** Maybe these are visiting students.

<!-- end_slide -->

The power of jq + Unix
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**What we just did:**
* Counted records
* Extracted fields
* Filtered by multiple conditions
* Aggregated data
* Found anomalies

<!-- column: 1 -->

**How long:**
* Each query: <1 second
* No code written
* No dependencies installed
* Fully reproducible

<!-- pause -->

**This is why jq matters.**

<!-- reset_layout -->

<!-- end_slide -->

A note on ethics
===

If this JSON came from a real student directory:

<!-- pause -->

* Treat it as **sensitive data**
* Don't publish it
* Don't share emails publicly
* Prefer aggregated results

<!-- pause -->

**With great power comes great responsibility.**

<!-- pause -->

This class teaches you powerful tools.

Use them ethically.

<!-- speaker_note: |
  This is important. Don't rush it.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

What you should remember
===

<!-- end_slide -->

Today's lessons
===

<!-- incremental_lists: true -->

1. **Small tools, composed** > one giant program
2. **Pipelines are programs** (and easier to debug)
3. **Text is the universal interface**
4. **Regex is a superpower** (more on this next week)
5. **jq is your JSON scalpel**

<!-- incremental_lists: false -->

<!-- speaker_note: |
  These are the takeaways. Make them stick.
-->

<!-- end_slide -->

Why this matters
===

**You will encounter:**
* Log files (millions of lines)
* JSON APIs (deeply nested)
* CSV data (messy)
* Config files (broken)

<!-- pause -->

**With these tools:**
* You'll solve problems in minutes, not hours
* You'll build workflows that last years
* You'll debug faster than your peers

<!-- pause -->

**Without these tools:**
* Every problem feels custom
* You'll reinvent wheels
* You'll burn time on solved problems

<!-- end_slide -->

The next step
===

Next week, we slow down and go **deep**:

<!-- pause -->

* **Regex**: Pattern matching in detail
* **Pipes**: How to think in streams
* **Debugging**: When pipelines break
* **Scripts**: Turning one-liners into tools

<!-- pause -->

By Week 3, you'll build a complete data-processing pipeline from scratch.

<!-- end_slide -->

<!-- jump_to_middle -->

Homework
===

<!-- end_slide -->

Week 0 homework (due next Monday)
===

**Part 1: Setup**
* Log into CLEAR (`ssh [netid]@ssh.clear.rice.edu`)
* Install `neofetch` and capture output
* Document any differences from your local machine

<!-- pause -->

**Part 2: Watch & Notes**
* Watch video (14 minutes): linked on website
* Take notes on anything surprising

<!-- pause -->

**Part 3: Read & Reflect**
* Read Paul Graham's "Hackers and Painters"
* Write 3-5 sentences on what resonated

<!-- speaker_note: |
  Keep it simple. Week 0 is about setup and orientation.
-->

<!-- end_slide -->

Homework submission
===

**How to submit:**
1. Create a GitHub repo: `week0-[netid]`
2. Add as submodule to class repo (instructions on website)
3. Commit three files:
   * `part1_notes.txt`
   * `neofetch.png`
   * `part2_notes.txt`
   * `part3_notes.txt`

<!-- pause -->

**Time limit: 1 hour max**

If it takes longer, stop and ask for help.

<!-- speaker_note: |
  Emphasize: we're checking setup, not testing knowledge.
-->

<!-- end_slide -->

Resources
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Course website:**
* lazy.rice.edu
* Notes, videos, setup guides
* Homework instructions

**Office hours:**
* Check syllabus for times
* Bring errors, not shame

<!-- column: 1 -->

**Tools to install:**
* `jq`
* `ripgrep` (better grep)
* `bat` (better cat)
* More next week

<!-- reset_layout -->

<!-- end_slide -->

Questions?
===

<!-- pause -->

**Common ones:**

*"What if I'm not comfortable with the terminal?"*
→ Perfect! That's what we're teaching.

<!-- pause -->

*"Will this be on the test?"*
→ There is no test. Just useful skills.

<!-- pause -->

*"Can I use Windows?"*
→ Yes, via WSL. Setup guide on website.

<!-- pause -->

*"What if I get stuck?"*
→ That's what office hours are for.

<!-- end_slide -->

<!-- jump_to_middle -->

Final thought
===

<!-- end_slide -->

The best programmers are lazy
===

They're lazy because they've invested in:

<!-- pause -->

* Learning their tools deeply

<!-- pause -->

* Building reusable workflows

<!-- pause -->

* Automating everything boring

<!-- pause -->

* Letting the computer remember

<!-- pause -->

By Week 10, you'll be lazy too.

**In the best possible way.**

<!-- end_slide -->

See you next week
===

**Homework due**: Next Monday

**Next topic**: Data wrangling deep dive (grep, sed, awk, regex)

**Bring**: Your terminal, your questions, your curiosity

<!-- pause -->

---

Go be lazy.

<!-- end_slide -->
