---
title: "The Art of Lazy Programming"
sub_title: "Week 2 — Vim: Edit at the Speed of Thought"
author: "SeniorMars"
---

<!--
speaker_note: |
  Time budget (60 min):
    0–5   cold open: the Vim challenge
    5–12  survival guide (don't panic)
    12-28 Demo 1: fixing broken code (movement + operators)
    28-42 Demo 2: cleaning messy CSV data (visual mode + macros)
    42-54 Demo 3: XML to JSON conversion (advanced macros)
    54-60 homework + resources + encouragement

  Demo prep:
    - Have all demo files ready
    - Terminal at readable size
    - Practice the keystrokes (they should flow)
    - Make intentional mistakes to show recovery
    - Keep energy up during "slow" parts
-->

<!-- jump_to_middle -->

A Challenge
===

I'm going to edit a file.

<!-- pause -->

Watch the screen.

<!-- pause -->

Try to guess what keys I'm pressing.

<!-- pause -->

**Ready?**

<!-- speaker_note: |
  Open broken_fizzbuzz.py. Fix it using only Vim motions:
  - G to end, O to add main call
  - gg to top, /range, cw to change 0 to 1
  - /fizz, n to find second, cw to change to buzz
  - 3G, A to append, type " and i % 15 == 0"
  Show it running fixed. Total time: ~15 seconds.
-->

<!-- end_slide -->

What just happened?
===

I fixed 5 bugs in about 15 seconds.

<!-- pause -->

No mouse. No arrow keys. No menus.

<!-- pause -->

Just keystrokes that compose like a language.

<!-- pause -->

**Today: You'll learn to do the same.**

<!-- end_slide -->

Why does this matter?
===

You've been using `nano` or VS Code Remote SSH.

<!-- pause -->

Both are fine.

<!-- pause -->

But you're going to spend years editing code on remote servers.

<!-- pause -->

**Vim is:**
- On every Linux/Unix machine (always available)
- Faster than reaching for the mouse
- A way of thinking about editing

<!-- pause -->

Once you learn it, you'll want Vim keybindings everywhere.

<!-- end_slide -->

The learning curve
===

Let's be honest: Vim is hard at first.

<!-- pause -->

**Timeline:**
- Hour 1-2: Painful, slower than nano
- Hour 10: Still awkward but seeing potential
- Hour 20: About as fast as before
- Hour 40+: Noticeably faster

<!-- pause -->

**Today's goal:** Get you to hour 10 by showing what's possible.

**This week's goal:** Force yourself to use it exclusively.

<!-- speaker_note: |
  Set expectations. This is an investment that pays off.
-->

<!-- end_slide -->

Agenda
===

Three demos that build your Vim mental model:

<!-- pause -->

1. **Fix broken code** - Movement and operators
2. **Clean messy data** - Visual mode and macros
3. **Convert XML to JSON** - Advanced automation

<!-- pause -->

Plus: Survival guide, best practices, and homework.

<!-- pause -->

By the end, you'll understand why Vim users can't shut up about it.

<!-- end_slide -->

<!-- jump_to_middle -->

Survival Guide
===

*Don't panic*

<!-- end_slide -->

The panic button
===

**Stuck in Vim?**

<!-- pause -->

Press `<Esc>` a few times.

<!-- pause -->

Type `:q!` and press `Enter`.

<!-- pause -->

You're out.

<!-- pause -->

**Memorize this.** Everything else is optional.

<!-- end_slide -->

The four modes
===

Vim has different modes for different tasks:

<!-- pause -->

**Normal mode** - Navigate and manipulate (default, where you live)

<!-- pause -->

**Insert mode** - Type text like a regular editor

<!-- pause -->

**Visual mode** - Select text

<!-- pause -->

**Command mode** - Run commands (`:w`, `:q`, etc.)

<!-- pause -->

**Key insight:** You spend most time in Normal mode, where every key is a command.

<!-- end_slide -->

Basic survival
===

**To edit a file:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

1. `vim filename.txt`
2. Press `i` (enter Insert mode)
3. Type your text
4. Press `<Esc>` (back to Normal)
5. Type `:wq` (write and quit)

<!-- column: 1 -->

**Essential commands:**

```
:w    - Save (write)
:q    - Quit
:wq   - Save and quit
:q!   - Quit without saving
```

<!-- reset_layout -->

<!-- pause -->

**Congratulations, you can now use Vim!**

(Inefficiently, but it works.)

<!-- end_slide -->

Movement: No arrow keys
===

In Normal mode, use `hjkl`:

```
     k
     ↑
  h ← → l
     ↓
     j
```

<!-- pause -->

**Why?** Your fingers stay on the home row. Faster.

<!-- pause -->

**Word movement:**
- `w` - Next word
- `b` - Back word
- `e` - End of word

<!-- pause -->

**Line movement:**
- `0` - Start of line
- `$` - End of line

<!-- end_slide -->

Operators: Actions
===

Vim's power: **operators + motions**

<!-- pause -->

**Common operators:**

| Operator | Action |
|----------|--------|
| `d` | Delete (cut) |
| `c` | Change (delete + insert) |
| `y` | Yank (copy) |
| `p` | Paste |

<!-- pause -->

**Combine them:**
- `dw` - Delete word
- `d$` - Delete to end of line
- `dd` - Delete entire line
- `cw` - Change word

<!-- pause -->

**Every operator works with every motion.**

This is why Vim is a language.

<!-- end_slide -->

Let's see it in action
===

Demo time.

<!-- speaker_note: |
  Transition to first demo. Build excitement.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 1
===

**Fixing Broken Code**

*Movement, operators, and composability*

<!-- end_slide -->

The broken code
===

Here's a broken FizzBuzz implementation:

```python
def fizz_buzz(limit):
    for i in range(limit):
        if i % 3 == 0:
            print('fizz')
        if i % 5 == 0:
            print('fizz')
        if i % 3 and i % 5:
            print(i)

def main():
    fizz_buzz(10)
```

<!-- pause -->

**Problems:**
1. Main is never called
2. Starts at 0 instead of 1
3. Prints "fizz" twice for multiples of 15
4. Should print "buzz" for multiples of 5
5. Wrong logic for when to print the number

<!-- end_slide -->

Watch: No mouse, no arrow keys
===

<!-- speaker_note: |
  Open fizzbuzz.py in Vim. Talk through each keystroke as you do it:

  1. Fix "main never called":
     - G (jump to end)
     - o (open line below)
     - Type: if __name__ == "__main__":
     - Enter, type: main()
     - <Esc>

  2. Fix "starts at 0":
     - gg (jump to start)
     - /range (search for "range")
     - w (next word - now on "limit")
     - i (insert before)
     - Type: 1,
     - <Esc>

  3. Fix "fizz twice" (should be buzz):
     - /fizz (search)
     - n (next occurrence)
     - cw (change word)
     - Type: buzz
     - <Esc>

  4. Fix multiples of 15:
     - gg (top)
     - 3j (down 3 lines, or /if.*3)
     - A (append at end of line)
     - Type: and i % 5 == 0
     - <Esc>

  5. Fix the print(i) condition:
     - /if.*and (search for the condition line)
     - ci( (change inside parentheses)
     - Type: i % 3 != 0 and i % 5 != 0
     - <Esc>

  Save and run:
     - :w
     - :!python3 %
-->

**I'll explain each keystroke as I go.**

<!-- end_slide -->

What you just saw
===

**Keystrokes used:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Movement:**
- `G` - End of file
- `gg` - Start of file
- `/pattern` - Search
- `n` - Next match
- `w` - Next word

<!-- column: 1 -->

**Operators:**
- `o` - Open line below
- `i` - Insert
- `A` - Append at end of line
- `cw` - Change word
- `ci(` - Change inside parens

<!-- reset_layout -->

<!-- pause -->

**No mouse. No arrow keys. Just composition.**

<!-- end_slide -->

Key insight: Text objects
===

The magic: `ci(` - "change inside parentheses"

<!-- pause -->

**Format:** `{operator}{a/i}{object}`

<!-- pause -->

**Text objects:**
- `iw` - Inner word
- `i"` - Inside quotes
- `i(` - Inside parentheses
- `i{` - Inside braces
- `it` - Inside HTML tag

<!-- pause -->

**Examples:**
- `di"` - Delete text inside quotes
- `ca(` - Change text AND parentheses
- `yi{` - Yank text inside braces

<!-- pause -->

**This is why Vim is fast.** You think in objects, not characters.

<!-- end_slide -->

Practice opportunity
===

Try this yourself:

```python
result = calculate_total(items, tax_rate, discount)
```

<!-- pause -->

**Tasks:**
1. Delete `tax_rate,` → cursor on `tax`, then `daw,` (delete a word + comma)
2. Change `items` to `products` → `cw` on `items`
3. Change what's inside parentheses → `ci(` anywhere inside

<!-- pause -->

**Notice:** You describe what you want in Vim's language.

<!-- speaker_note: |
  Let this sink in. This is the mental model shift.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 2
===

**Cleaning Messy Data**

*Visual mode and macros*

<!-- end_slide -->

The messy CSV
===

You get this data from a client:

```
Alice Johnson | 85, 92, 88
Bob   Smith   | 78,85, 90
Charlie Davis| 92 ,88,95
David  Lee    |88,90,87
Eve   Wilson  |   95, 92,98
```

<!-- pause -->

**Problems:**
- Inconsistent spacing
- Weird delimiter (should be comma)
- Spaces around numbers
- No header row

<!-- pause -->

**Goal:** Clean it up for processing.

<!-- end_slide -->

Step 1: Add header
===

**Goal:** Insert a header row.

<!-- speaker_note: |
  In Vim:
  - gg (top of file)
  - O (open line above)
  - Type: Name,Grade1,Grade2,Grade3
  - <Esc>
-->

**Keystrokes:**
```
gg          - Jump to top
O           - Open line above
(type header)
<Esc>       - Back to Normal mode
```

<!-- pause -->

**Result:**
```
Name,Grade1,Grade2,Grade3
Alice Johnson | 85, 92, 88
...
```

<!-- end_slide -->

Step 2: Fix delimiters
===

**Goal:** Replace ` | ` with `,`

<!-- pause -->

**Use search and replace:**

```vim
:%s/ | /,/g
```

<!-- pause -->

**Breakdown:**
- `%` - Entire file
- `s/old/new/` - Substitute
- `g` - Global (all on line)

<!-- pause -->

**Result:**
```
Name,Grade1,Grade2,Grade3
Alice Johnson,85, 92, 88
Bob   Smith,78,85, 90
...
```

<!-- end_slide -->

Step 3: Remove extra spaces
===

**Goal:** Remove multiple spaces between names.

<!-- pause -->

**Visual Block mode magic:**

<!-- speaker_note: |
  Show this live:
  1. Cursor on first space in "Alice  Johnson"
  2. Ctrl-v (visual block)
  3. j j j j (select down)
  4. l l l (select right to cover spaces)
  5. x (delete)
-->

```
Ctrl-v      - Visual block mode
(move to select column of spaces)
x           - Delete
```

<!-- pause -->

**Or use substitute:**

```vim
:%s/  \+/ /g    " Replace 2+ spaces with 1
```

<!-- end_slide -->

Step 4: Clean up grade spacing
===

**Goal:** Remove spaces around commas in grades.

<!-- pause -->

```vim
:%s/ ,/,/g      " Remove space before comma
:%s/, /,/g      " Remove space after comma
```

<!-- pause -->

**Result:**
```
Name,Grade1,Grade2,Grade3
Alice Johnson,85,92,88
Bob Smith,78,85,90
Charlie Davis,92,88,95
David Lee,88,90,87
Eve Wilson,95,92,98
```

<!-- pause -->

**Perfect!**

<!-- end_slide -->

But wait, there's a better way
===

What if you have 1000 files like this?

<!-- pause -->

**Macros**: Record and replay editing sequences.

<!-- pause -->

**How to record:**
1. `q{letter}` - Start recording (e.g., `qa`)
2. Do your edits
3. `q` - Stop recording
4. `@{letter}` - Replay (e.g., `@a`)
5. `@@` - Replay last macro

<!-- pause -->

Let me show you.

<!-- end_slide -->

Macro demo
===

**Goal:** Convert each line to format: `Name: Grade1, Grade2, Grade3`

<!-- speaker_note: |
  Starting with: Alice Johnson,85,92,88
  
  Record macro in register 'a':
  qa              - Start recording
  ^               - Start of line
  f,              - Find first comma
  r:              - Replace with colon
  a               - Append
  (space)         - Add space
  <Esc>           - Normal mode
  f,              - Find next comma
  a               - Append
  (space)         - Add space
  <Esc>           - Normal mode
  f,              - Find next comma  
  a               - Append
  (space)         - Add space
  <Esc>           - Normal mode
  j               - Move to next line
  q               - Stop recording
  
  Then: 4@a to run on remaining 4 lines
-->

**Watch as I:**
1. Record editing one line
2. Replay it on all other lines

<!-- pause -->

**Keystrokes:** (I'll explain as I go)

<!-- end_slide -->

What you just saw
===

**Power of macros:**

<!-- pause -->

- Record once
- Replay infinite times
- Combine with counts: `100@a`
- Save time on repetitive edits

<!-- pause -->

**Common pattern:**
```
qa          - Start recording to 'a'
(edits)     - Make your changes
j           - Move to next line
q           - Stop recording
999@a       - Replay 999 times (or until end)
```

<!-- pause -->

**This is why Vim users never do repetitive edits manually.**

<!-- end_slide -->

Visual Block mode power
===

One more visual block trick:

<!-- speaker_note: |
  Show this with a different example:
  
  Starting with:
  var1 = 10
  var2 = 20
  var3 = 30
  
  Goal: Add # comments:
  var1 = 10  # First
  var2 = 20  # Second
  var3 = 30  # Third
  
  1. Cursor on first line, $
  2. Ctrl-v (visual block)
  3. 2j (down 2 lines)
  4. A (append after)
  5. Type: "  # First"
  6. <Esc>
  
  Show that it appears on all lines!
-->

**Adding the same text to multiple lines:**

```
Ctrl-v      - Visual block
(select lines)
I or A      - Insert at start/end
(type text)
<Esc>       - Apply to all lines
```

<!-- pause -->

**This is magic for:**
- Adding comments
- Indenting columns
- Editing tables
- Formatting data

<!-- end_slide -->

<!-- jump_to_middle -->

Demo 3
===

**XML to JSON Conversion**

*Advanced macros and Vim as a programming language*

<!-- end_slide -->

The challenge
===

You have this XML:

```xml
<people>
    <person>
        <name>Alice</name>
        <email>alice@example.com</email>
    </person>
    <person>
        <name>Bob</name>
        <email>bob@example.com</email>
    </person>
    <person>
        <name>Charlie</name>
        <email>charlie@example.com</email>
    </person>
</people>
```

<!-- end_slide -->

The goal
===

Convert to JSON:

```json
[
  {
    "name": "Alice",
    "email": "alice@example.com"
  },
  {
    "name": "Bob",
    "email": "bob@example.com"
  },
  {
    "name": "Charlie",
    "email": "charlie@example.com"
  }
]
```

<!-- pause -->

**Could you write a Python script?** Sure.

**Could you use regex?** Maybe.

**But with Vim macros?** Seconds.

<!-- end_slide -->

The approach
===

**Strategy:**
1. Delete the outer `<people>` tags
2. Convert each `<person>` to `{`
3. Convert each field to JSON format
4. Add commas and closing braces

<!-- pause -->

**We'll build this incrementally with macros.**

<!-- speaker_note: |
  This is complex. Go slow. Explain each step.
-->

<!-- end_slide -->

Step 1: Remove outer tags
===

**Delete first and last line:**

<!-- speaker_note: |
  In Vim:
  ggdd    - Go to top, delete line
  Gdd     - Go to bottom, delete line
-->

```
gg          - Jump to top
dd          - Delete line
G           - Jump to bottom  
dd          - Delete line
```

<!-- pause -->

**Now we have:**
```
    <person>
        <name>Alice</name>
        <email>alice@example.com</email>
    </person>
    ...
```

<!-- end_slide -->

Step 2: Format one person
===

**Macro to format the first person:**

<!-- speaker_note: |
  This is the complex part. Build it step by step:
  
  Goal: Transform
    <person>
        <name>Alice</name>
        <email>alice@example.com</email>
    </person>
  
  To:
  {
    "name": "Alice",
    "email": "alice@example.com"
  },
  
  Macro approach:
  qe                    - Start recording to 'e'
  
  # Change <person> to {
  /person<CR>           - Find <person>
  S{<Esc>               - Substitute line with {
  
  # Format name line
  j                     - Down to name line
  ^                     - Start of line
  f><space>             - Find > after <name>
  i"name": "<Esc>       - Insert before content
  f<<space>             - Find < of </name>
  C",<Esc>              - Change to end of line with ",
  
  # Format email line
  j                     - Down to email line
  ^f><space>            - Find > after <email>
  i"email": "<Esc>      - Insert before content
  f<<space>             - Find < of </email>
  C"<Esc>               - Change to end with "
  
  # Change </person> to },
  j                     - Down to </person>
  S},<Esc>              - Substitute with },
  
  j                     - Move to next person
  q                     - Stop recording
  
  Then: 2@e to run on remaining 2 people
-->

**I'll record this macro, then explain it:**

```
qe          - Start recording to 'e'
(complex edits - I'll show)
q           - Stop
```

<!-- pause -->

**Then replay:**
```
2@e         - Run on remaining 2 people
```

<!-- end_slide -->

The macro breakdown
===

**What the macro does:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

1. Find `<person>`, change to `{`
2. Format name: add quotes and comma
3. Format email: add quotes
4. Change `</person>` to `},`
5. Move to next person

<!-- column: 1 -->

**Key commands:**
- `S` - Substitute entire line
- `C` - Change to end of line
- `f{char}` - Find character
- `i`, `a` - Insert before/after

<!-- reset_layout -->

<!-- pause -->

**The power:** You "program" the transformation once, then replay.

<!-- end_slide -->

Step 3: Final touches
===

**Add array brackets:**

```vim
gg          - Top of file
O           - Open line above
[           - Type opening bracket
<Esc>       - Normal mode
G           - End of file
o           - Open line below
]           - Type closing bracket
```

<!-- pause -->

**Fix last comma:**

```vim
?},         - Search backward for },
x           - Delete the comma
```

<!-- pause -->

**Done!**

<!-- end_slide -->

What just happened
===

**We converted XML to JSON:**
- No external tools
- No programming
- Just Vim commands composed together

<!-- pause -->

**Time:** ~2 minutes for first conversion

**Then:** Record macro, replay on 1000 files instantly

<!-- pause -->

**This is why Vim users love macros.**

They turn Vim into a programmable text processor.

<!-- speaker_note: |
  Drive home the point: This is about automation and leverage.
-->

<!-- end_slide -->

Alternative approach: Global commands
===

**Vim's `:g` command** (global) is also powerful:

```vim
:g/pattern/command
```

<!-- pause -->

**Examples:**

```vim
:g/TODO/d              " Delete all lines with TODO
:g/^$/d                " Delete all blank lines
:g/debug/normal A #    " Add # to end of debug lines
```

<!-- pause -->

**For our XML conversion:**

```vim
:g/<person>/s/<person>/{/
:g/<\/person>/s/<\/person>/},/
```

<!-- pause -->

**Macros vs `:g` commands:**
- Macros: More visual, easier to build incrementally
- `:g`: More powerful for line-based operations

<!-- end_slide -->

<!-- jump_to_middle -->

Best Practices
===

<!-- end_slide -->

Start simple
===

**Don't try to learn everything.**

<!-- pause -->

**Week 1 focus:**
- `hjkl` for movement
- `i` and `<Esc>`
- `dd`, `yy`, `p`
- `:w`, `:q`

<!-- pause -->

**Week 2 add:**
- `w`, `b`, `e` (word movement)
- `0`, `$` (line movement)
- `dw`, `cw` (operators + motion)

<!-- pause -->

**Week 3 add:**
- Text objects (`ci"`, `di(`)
- Visual mode
- Search and replace

<!-- pause -->

**Week 4+:**
- Macros
- Advanced movements
- Custom configurations

<!-- end_slide -->

Use the help
===

Vim has the best documentation of any editor.

<!-- pause -->

**Essential help commands:**

```vim
:help               " Open help
:help movement      " Movement commands
:help operator      " Operators
:help text-objects  " Text objects
:help :substitute   " Search and replace
:help macros        " Macros
```

<!-- pause -->

**Pro tip:** Use `:help` + Tab for autocomplete

```vim
:help vis<Tab>      " Autocomplete to :help visual-mode
```

<!-- pause -->

**Learn to read Vim help.** It's comprehensive and well-written.

<!-- end_slide -->

Configure gradually
===

**Don't copy someone's entire .vimrc.**

<!-- pause -->

**Start minimal:**
- Line numbers
- Syntax highlighting
- Basic keymaps

<!-- pause -->

**Add one thing per week:**
- Week 1: Basic settings
- Week 2: Better search
- Week 3: Plugin manager
- Week 4: First plugin (fzf)

<!-- pause -->

**Why?** You'll understand what each setting does.

<!-- end_slide -->

The .vimrc philosophy
===

**Your .vimrc should:**

1. Be readable (comments!)
2. Be portable (works on any machine)
3. Grow with you (add as you learn)

<!-- pause -->

**Anti-patterns:**
- Copying 500 lines you don't understand
- Obscure mappings that conflict
- Plugins you never use

<!-- pause -->

**Good pattern:**
- Simple, well-documented
- Each setting has a reason
- Test before committing

<!-- end_slide -->

Common mistakes
===

**Staying in Insert mode too long**

<!-- pause -->

Think in Normal mode. Use Insert mode for short bursts.

Bad: `i` → type 50 lines → `<Esc>`

Good: `i` → type a line → `<Esc>` → navigate → `i` → repeat

<!-- pause -->

**Using the mouse**

Force yourself not to. It's slower than you think.

<!-- pause -->

**Not using the dot command**

`.` repeats your last change. Use it constantly!

<!-- pause -->

**Holding down `h` or `j`**

Learn better movements: `w`, `}`, `/pattern`

<!-- end_slide -->

Vim in the real world
===

**When to use Vim:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

- Editing files on servers
- Quick edits
- Working in terminal
- Pair programming over SSH
- When you need speed

<!-- column: 1 -->

**When to use VS Code/IDE:**

- Exploring new codebases
- Debugging with breakpoints
- Refactoring tools
- GUI-specific features

<!-- reset_layout -->

<!-- pause -->

**Best approach:** Use Vim keybindings in your IDE.

VS Code has an excellent Vim extension.

<!-- end_slide -->

The two-week challenge
===

**Week 2 (this week):**

Use Vim for all homework. Reference the notes. It will be slow.

<!-- pause -->

**Week 3:**

Use Vim exclusively. No VS Code fallback. Force yourself.

<!-- pause -->

**Week 4:**

Notice you're faster. Notice you're annoyed when Vim isn't available.

<!-- pause -->

**By Week 4, you won't want to go back.**

<!-- speaker_note: |
  This is the commitment. Make it clear.
-->

<!-- end_slide -->

Resources
===

**Essential:**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Built-in:**
- `vimtutor` (30 min tutorial)
- `:help` (comprehensive docs)

**Online:**
- [OpenVim](https://www.openvim.com/)
- [Vim Adventures](https://vim-adventures.com/)

<!-- column: 1 -->

**Practice:**
- [Vim Golf](https://www.vimgolf.com/)
- Our Week 2 homework
- Use it daily

**Reading:**
- [Practical Vim](https://pragprog.com/titles/dnvim2/)
- Vim Tips Wiki

<!-- reset_layout -->

<!-- end_slide -->

<!-- jump_to_middle -->

Homework
===

<!-- end_slide -->

Week 2 homework
===

**Time limit: 1.5 hours**

<!-- pause -->

**Required exercises:**
1. Complete `vimtutor` (30 min)
2. Set up `.vimrc` (15 min)
3. Install vim-plug + fzf plugin (20 min)
4. Movement practice (15 min)
5. Edit a script using only Vim (20 min)
6. Visual mode practice (15 min)
7. Search and replace (10 min)

<!-- pause -->

**Bonus:**
- Macros challenge
- Vim Golf

<!-- end_slide -->

Key homework tips
===

**Start with vimtutor**

Don't skip it. It's the best introduction.

<!-- pause -->

**Force yourself**

No VS Code. No nano. Only Vim for all homework.

<!-- pause -->

**Use the notes**

Everything you need is in the Week 2 notes.

<!-- pause -->

**Ask for help**

Office hours are for exactly this. Come early, come often.

<!-- end_slide -->

What you'll gain
===

**After this week:**

<!-- pause -->

- Edit without reaching for the mouse
- Navigate files at thought speed
- Compose commands like sentences
- Automate repetitive edits with macros

<!-- pause -->

**After a month:**

- Vim muscle memory
- Frustrated when Vim bindings aren't available
- Editing faster than you ever imagined

<!-- pause -->

**This is worth the investment.**

<!-- end_slide -->

Next week
===

**Week 3: Data Wrangling**

We'll use Vim to work with:
- Regular expressions (pattern matching)
- sed (stream editor)
- awk (text processing)
- Real messy data

<!-- pause -->

You'll be glad you learned Vim.

<!-- pause -->

All that data wrangling in Vim? Fast as lightning.

<!-- end_slide -->

Final thoughts
===

Vim is not about memorizing commands.

<!-- pause -->

It's about **thinking in a language** for editing.

<!-- pause -->

**Operators** (verbs): `d`, `c`, `y`

**Motions** (nouns): `w`, `$`, `/pattern`

**Text objects**: `iw`, `i"`, `i(`

<!-- pause -->

Compose them: `ci"` = change inside quotes

<!-- pause -->

**That's the insight.**

Everything else is practice.

<!-- end_slide -->

The promise
===

**Week 2 will be frustrating.**

<!-- pause -->

You'll feel slower.

You'll want to reach for the mouse.

You'll question if it's worth it.

<!-- pause -->

**Stick with it.**

<!-- pause -->

By Week 4, you'll edit at the speed you think.

By Week 8, you'll wonder how you ever lived without it.

<!-- pause -->

**Every Vim user felt the same way at first.**

**Every Vim user is glad they persisted.**

<!-- end_slide -->

Questions?
===

**Common ones:**

<!-- pause -->

*"When will I be as fast as you?"*

→ Week 4-6 with daily practice.

<!-- pause -->

*"Should I customize Vim now?"*

→ No. Use vanilla Vim until you feel the pain.

<!-- pause -->

*"Can I use Neovim instead?"*

→ Yes! It's compatible. See installation guide.

<!-- pause -->

*"What if I hate it?"*

→ Try for 2 weeks. If it still doesn't click, we can talk alternatives.

<!-- end_slide -->

<!-- jump_to_middle -->

Go edit something
===

**Today**: Run `vimtutor` (required)

**This week**: Use Vim exclusively for homework

**This month**: Force it in all coding

<!-- pause -->

---

**See you next week, when we wrangle data like wizards.**

<!-- pause -->

**Remember**: The lazy programmer invests in their tools.

<!-- end_slide -->
