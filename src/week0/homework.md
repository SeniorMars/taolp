# Week 0 Homework

## Installation
Please follow the instructions in the [Week 0 Installation Guide](../installation/week0.md) to install the necessary software and tools for this week.

## GitHub Repository Access
To gain access to the team's repository, please either fill out [this form](https://forms.gle/7xPFEEouiEviU9vo8) or send me your GitHub username directly. Access the repository sections here: 
- [Week 0 Section 1](https://github.com/RTAOLP/week0_section1)
- [Week 0 Section 2](https://github.com/RTAOLP/week0_section2)

## Notes
For instructions on using the command line interface (CLI), refer to the [Week 0 Notes](./notes.md). Please note that while the basics of bash will be covered in our next lesson, you are encouraged to experiment on your own in the meantime.

## Homework Overview
This week's homework consists of three parts, focusing more on reading and exercises than coding. Parts 2 and 3 are graded based on completion. All submissions should be made via GitHub to your specific section's repository.

### Submitting Homework
- **Repository Setup**: Submit all homework through your section's repo using the git submodule setup described in the [week0 installation guide](../installation/week0.md#git-and-github---submodule-setup).
- **Submodule Naming**: Name your submodule `lastname-firstname`.
- **Committing**: Commit all your work to your own repo. I will update the main repo during grading. You do not need to commit to the main repo once your submodule is set up.
- **Time Limit**: If any assignment takes more than an hour, feel free to skip it.

### Part 1: Observation and Installation
1. **Observation Task**: Note any differences between your setup and a clear session. Upload your observations to the Week 0 Notes under the filename "part_1_notes.txt".
2. **Installation Task**: Install neofetch and capture its output:
    ```bash
    $ git clone https://github.com/dylanaraps/neofetch
    $ cd neofetch
    $ make PREFIX=./ install
    ```
   Upload the output screenshot to the Week 0 Notes as "neofetch.img". Example output:
   ![Neofetch](../images/neofetch.png)
   ```bash
   git add part_1_notes.txt neofetch.img
   git commit -m "part 1"
   git push
   ```

### Part 2: Video Analysis
- **Task**: Watch a video segment from [this video](https://youtu.be/tc4ROCJYbm0?t=300) from 5:00 to 19:00. Take notes and submit them as "part_2_notes.txt". If possible, watching the entire video is recommended.
   ```bash
   git add part_2_notes.txt
   git commit -m "part 2 notes"
   git push
   ```

### Part 3: Article Review
- **Task**: Read [this article by Paul Graham](http://www.paulgraham.com/hp.html) and note anything significant. Submit your notes as "part_3_notes.txt".
   ```bash
   git add part_3_notes.txt
   git commit -m "part 3 notes"
   git push
   ```

**Completion**: Submitting your assignments will contribute positively to your grade. Ensure you follow the guidelines to ensure your submissions are recorded correctly.
