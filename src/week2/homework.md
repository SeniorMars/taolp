# Week 2 Homework - Terminal Text Editors

**Time estimate**: 1.5 hours maximum

**Submission**: Create a directory `week2-[netid]` with your solutions and add it as a submodule to the class repo.

---

## Important Note

This week will feel slower than Week 1. That's expected. You're learning a new way to edit text, and your muscle memory needs time to develop.

If any exercise takes significantly longer than the time estimate, stop and ask for help. The goal is learning, not frustration.

---

## Setup

Create your homework directory:

```bash
mkdir week2-[netid]
cd week2-[netid]
```

---

## Exercise 1: Complete vimtutor (30 minutes)

**Required**

Run the built-in Vim tutorial:

```bash
vimtutor
```

Complete lessons 1 through 4 minimum. If you have time, do all 7 lessons.

**Deliverable**: Create a file `vimtutor_notes.txt` with:
- Which lessons you completed (1-4 minimum, 1-7 ideal)
- Three things you learned that surprised you
- One thing you found confusing or difficult

Save this file using Vim:
1. `vim vimtutor_notes.txt`
2. Press `i` to enter insert mode
3. Type your notes
4. Press `<Esc>` to return to normal mode
5. Type `:wq` to save and quit

---

## Exercise 2: Set Up Your .vimrc (15 minutes)

**Required**

Create a basic Vim configuration file.

**Steps:**

1. Create `~/.vimrc` if it doesn't exist:
   ```bash
   vim ~/.vimrc
   ```

2. Copy the basic config from the Week 2 notes (the "Starter .vimrc" section)

3. Save and test:
   ```bash
   vim test.txt
   ```
   
   Verify you see:
   - Line numbers
   - Relative line numbers
   - Syntax highlighting
   - Your cursor line highlighted

**Deliverable**: Create a file `vimrc_setup.txt` answering:
- What settings did you add to your .vimrc?
- Which setting do you think will be most useful?
- Did you customize anything beyond the starter config? If so, what?

---

## Exercise 3: Install a Plugin Manager and Plugin (20 minutes)

**Required**

Modern Vim users manage plugins with a plugin manager. We'll use vim-plug.

### Part A: Install vim-plug

**For Vim:**
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

**For Neovim:** (or whatever plugin manager you're using)
```bash
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

### Part B: Configure plugins in .vimrc

Add this section to your `~/.vimrc` (above your other settings):

```vim
" Plugins
call plug#begin('~/.vim/plugged')

" Add plugins here
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'

call plug#end()
```

### Part C: Install the plugins

1. Open Vim: `vim`
2. Run the command: `:PlugInstall`
3. Wait for installation to complete
4. Quit and reopen Vim

### Part D: Test fzf

fzf is a fuzzy file finder (like Ctrl-P in VS Code).

1. Navigate to a directory with multiple files
2. Open Vim: `vim`
3. Type `:Files` and press Enter
4. Start typing a filename - see it filter in real-time
5. Press Enter to open a file

**Deliverable**: Create `plugin_setup.txt` with:
- Screenshot or description of fzf working
- What command did you use to install plugins? (`:PlugInstall`)
- What does fzf do and why is it useful?

**Bonus**: Add a keybinding to open fzf with Ctrl-P:
```vim
" Add to your .vimrc
nnoremap <C-p> :Files<CR>
```

---

## Exercise 4: Vim Movement Practice (15 minutes)

**Required**

Download this practice file:

```bash
curl -o movement_practice.txt https://raw.githubusercontent.com/iggredible/Learn-Vim/master/ch05_moving_in_file.md
```

Open it in Vim and complete these tasks using ONLY Vim motions (no arrow keys, no mouse):

1. Jump to the beginning of the file
2. Jump to the end of the file
3. Find the word "vertical" (search forward)
4. Jump to line 50
5. Delete the word under your cursor
6. Delete from cursor to end of line
7. Undo your last change
8. Yank (copy) a line
9. Paste it below

**Deliverable**: Create `movement_log.txt` documenting:
- The command you used for each task above
- Which motion felt most natural?
- Which motion felt most awkward?

**Example format:**
```
1. Jump to beginning: gg
2. Jump to end: G
...
```

---

## Exercise 5: Edit a Shell Script with Vim (20 minutes)

**Required**

Create a script using only Vim and proper Vim workflows.

**Task**: Write a script `word_counter.sh` that:
- Takes a filename as an argument
- Counts total words
- Counts unique words
- Shows the 5 most common words

**Requirements**:
- Use ONLY Vim to create and edit the script
- Practice using `o` to open new lines
- Use `dw` to delete words when fixing mistakes
- Use `cw` to change words
- Use `yy` and `p` to copy/paste lines

**Starter code** (type this in Vim, don't copy-paste):

```bash
#!/usr/bin/env bash
set -euo pipefail

# TODO: Add argument validation

# TODO: Count total words

# TODO: Count unique words  

# TODO: Show top 5 most common words
```

**Expected behavior**:
```bash
$ ./word_counter.sh sample.txt
Total words: 342
Unique words: 156
Top 5 words:
  45 the
  32 and
  28 to
  22 a
  19 of
```

**Hints**:
- Use `tr` to split words
- Use `sort` and `uniq -c`
- Use `head -5` for top 5

**Deliverable**: The completed `word_counter.sh` script

---

## Exercise 6: Visual Mode Practice (15 minutes)

**Required**

Create a file `visual_practice.py` with this messy Python code:

```python
def calculate_total(items):
x = 0
for item in items:
x = x + item
return x

def calculate_average(items):
total = calculate_total(items)
count = len(items)
return total / count

def main():
numbers = [1, 2, 3, 4, 5]
print(calculate_total(numbers))
print(calculate_average(numbers))
```

**Tasks** (using Vim):

1. Fix the indentation in `calculate_total` function
   - Use Visual Line mode (`V`) to select the poorly indented lines
   - Use `>` to indent

2. Add a docstring to `calculate_total`:
   ```python
   """Calculate the sum of all items."""
   ```
   - Use `o` to open a new line below the function definition
   - Type the docstring

3. Comment out the `print` statements in `main`
   - Use Visual Block mode (`Ctrl-v`) to select the start of both lines
   - Use `I` to insert at the beginning
   - Type `# `
   - Press `<Esc>`

4. Duplicate the entire `calculate_average` function
   - Use `V` to select the entire function
   - Use `y` to yank (copy)
   - Navigate below it
   - Use `p` to paste
   - Change the duplicate to `calculate_median` using `cw`

**Deliverable**: The corrected `visual_practice.py` file

---

## Exercise 7: Search and Replace (10 minutes)

**Required**

Create `replace_practice.txt` with:

```
TODO: Fix the login bug
TODO: Update documentation
DONE: Add tests
TODO: Refactor database code
DONE: Deploy to staging
TODO: Review pull request
```

**Tasks** (using Vim search and replace):

1. Replace all "TODO" with "IN_PROGRESS" globally
2. Replace only the first "DONE" with "COMPLETED"
3. Delete all lines containing "database"

**Commands you'll need**:
```vim
:%s/old/new/g        " Replace all in file
:s/old/new/          " Replace first on line
:g/pattern/d         " Delete lines matching pattern
```

**Deliverable**: 
- The modified `replace_practice.txt`
- A file `replace_commands.txt` listing the exact commands you used

---

## Exercise 8 (Bonus): Macros (15 minutes)

**Optional - Extra Credit**

Given this CSV data in `grades.csv`:

```
Alice,85,92,88
Bob,78,85,90
Charlie,92,88,95
David,88,90,87
Eve,95,92,98
```

**Task**: Use a Vim macro to transform it to:

```
Student: Alice, Average: 88.33
Student: Bob, Average: 84.33
Student: Charlie, Average: 91.67
Student: David, Average: 88.33
Student: Eve, Average: 95.00
```

**Approach**:
1. Record a macro that:
   - Extracts the student name
   - Calculates the average (you can use Python or bc)
   - Formats the output line
2. Replay it for all lines

**Hint**: You might need to use external commands from Vim:
```vim
:.!python3 -c "print(sum([85, 92, 88])/3)"
```

**Deliverable**: 
- The transformed `grades_formatted.txt`
- A file `macro_explanation.txt` explaining your macro

---

## Exercise 9 (Bonus): Vim Golf (10 minutes)

**Optional - Extra Credit**

Vim Golf is about solving editing challenges in minimum keystrokes.

**Challenge**: Given this text in `golf.txt`:

```
function_name
```

Transform it to:

```
def function_name():
    pass
```

**Your score**: Number of keystrokes (lower is better)

**Deliverable**: A file `golf_solution.txt` with:
- Your keystroke sequence
- Your total count
- Explanation of what each keystroke does

**Example solution format**:
```
Keystrokes: Idef <Esc>A():<Esc>opass<Esc>
Count: 23
Explanation:
I - Insert at beginning of line
def  - Type "def "
<Esc> - Return to normal mode
...
```

---

## Submission Checklist

Your `week2-[netid]` directory should contain:

### Required Files 
```
week2-netid/
├── vimtutor_notes.txt
├── vimrc_setup.txt
├── plugin_setup.txt
├── movement_log.txt
├── word_counter.sh
├── visual_practice.py
├── replace_practice.txt
└── replace_commands.txt
```

### Bonus Files 
```
├── grades_formatted.txt
├── macro_explanation.txt
├── golf_solution.txt
└── golf_explanation.txt
```

**Before submitting:**
1. Make all scripts executable: `chmod +x *.sh`
2. Test that your .vimrc works: `vim --clean -u ~/.vimrc`
3. Verify plugins load: Open Vim and run `:PlugStatus`
4. Commit and push to your repo

---

## Grading

- Exercises 1-7: Required
- Exercises 8-9: Bonus 

**Completion-based grading**: If you attempted the exercise honestly and followed the requirements, you get full credit for that exercise.

---

## Tips for Success

### Start Small
Don't try to memorize everything. Focus on:
- `hjkl` for movement
- `i` for insert, `<Esc>` to exit
- `dd`, `yy`, `p` for delete/copy/paste
- `:w`, `:q`, `:wq` for file operations

### Use the Help
Vim has amazing built-in help:
```vim
:help motion
:help operator
:help visual-mode
:help :substitute
```

### Check Your Progress
After each exercise, verify you can:
1. Open a file in Vim
2. Navigate without arrow keys
3. Make edits efficiently
4. Save and quit without panic

### Common Mistakes to Avoid

1. **Using arrow keys**: Force yourself to use `hjkl`
2. **Staying in Insert mode**: Spend most time in Normal mode
3. **Not using text objects**: Learn `diw`, `ci(`, `da"`
4. **Ignoring the dot command**: `.` repeats your last change

### When You're Stuck

1. Press `<Esc>` several times (get to Normal mode)
2. Type `:help` followed by what you're trying to do
3. Check the Week 2 notes
4. Google: "vim how to [what you want]"
5. Come to office hours

### Practice Strategy

**Week 2**: Force yourself to use Vim for all homework  
**Week 3**: Use Vim exclusively (no VS Code fallback)  
**Week 4+**: Enjoy being faster than before

---

## Resources

- **Built-in help**: `:help` (from within Vim)
- **Interactive practice**: https://www.openvim.com/
- **Vim Adventures**: https://vim-adventures.com/ (game)
- **Cheat sheet**: https://vim.rtorr.com/
- **Our notes**: Week 2 notes on course website

---

## Final Thoughts

Learning Vim is like learning to touch type. The first week is painful. The second week is frustrating. The third week you start to see it. By the fourth week, you wonder how you ever lived without it.

**Stick with it.**

By Week 4, you'll be editing at the speed you think, and every other editor will feel slow.

**Remember**: 
- Vim is a language for editing
- Operators + motions = powerful combinations
- Modal editing feels weird until it doesn't
- The best way to learn is to force yourself to use it

Good luck, and see you in Week 3!

---

**Submit by**: Next Wednesday via git submodule

**Stuck?** Ask for help early, don't suffer in silence
