# Code Review

Reviewing code can take some time, but it is a great service to your colleagues, and as you get in the habit of looking at other people's code, it's also a great way to learn about new / different ways of doing things! Thus, please always make a good effort to go through all the code and give honest feedback.

## Core Components

Some of the core components of good code are clarity, cleanliness, and efficiency, which translate into a few guiding questions for code review:

1. Is it _easy_ to understand the code? (Essentially, do you understand the purpose of every line without having to do much digging?)
2. Does the code follow general coding standards / guidelines? (See [below](#code-style) for style recommendations.)
3. Is there any code that is duplicated more than twice?
4. Are any functions / methods very long? If yes, does it have too many responsibilities?
5. Notice any glaring inefficiencies? Any potential areas for speed up?

## Code Review Checklist

The questions above lay most of the foundation for what to comment / look out for during code review, but here is a more detailed checklist that you can use (large portions borrowed from the [resources below](#additional-resources)):

1. Best practices
    - [ ] No hard coding of values (use constants / variables instead)
    - [ ] Comments explain why the code is written the way it is, not simply what it does. Anything hacky / temporary is explained. There is also a comment for each function that briefly explain what it does, its arguments, and what it returns (if any)
    - [ ] Expected potential errors are handled intentionally (try/catch, exception handlers)
    - [ ] Multiple if/else blocks have been avoided
    - [ ] Limited / no nested for loops (unless clearly necessary)
    - [ ] Early exits (breaks in loops, return from functions) are used when appropriate
    - [ ] Existing frameworks / packages that has the functionality needed are used (versus custom code)
    - [ ] Frameworks / packages are used according to their defined purpose, not hacky solutions for more trivial problems
    - [ ] Any code taken from somewhere else is acknowledged
2. Clarity
    - [ ] Code is self-explanatory
    - [ ] Variable and function names are descriptive and interpretable (e.g., not aa or banana)
    - [ ] Classes / functions mostly have one single main responsibility
    - [ ] For anything that is not essentially instant, there are messages (sent judiciously) updating the user on what task is being done / has been completed
3. Cleanliness
    - [ ] Whitespace is used appropriately (not excessively), code block start / end points are clear
    - [ ] A proper [style guide](#code-style) has been followed
    - [ ] Library / package imports are at the top of the file
    - [ ] Constants are declared at the beginning of the file (even better, in all CAPS)
    - [ ] Code that is commented out is removed
4. Efficiency / resuability / reproducibility
    - [ ] The same code is never repeated more than twice
    - [ ] In scenarios where data / settings can vary, appropriate hooks (e.g., using argparse in python) and/or necessary configurable files are set up so that code changes are not required
    - [ ] The most suitable / efficient data type for each task is used (dictionaries, lists, data.table, etc.)
    - [ ] Given its intended use case, the code runs in a reasonable timeframe
    - [ ] Any code that uses 'random' functions provides a mean to specify the seed, as well as a default value

## Code Style

Python code should follow [PEP 8](https://www.python.org/dev/peps/pep-0008/), and R code should follow [Google's R Style Guide](https://google.github.io/styleguide/Rguide.html). Linters should be used during development to maintain consistent styling guidelines. (Several guidelines enforce the 80 character maximum line length—this can be relaxed to 100 or 120 characters, but make sure it's consistent within a project; to facilitate this, project-specific formatting guidelines can be that differ from the corresponding style guide can be specified in a [`.editorconfig` file](https://editorconfig.org/), which works with most development environments.)

## Additional Resources

There is a rich body of literature and lots of different opinions that you can find easily online about effective code reviews—here are a couple (with some of the information from this document, but also many more details and explanations), if you are interested in reading more / need more convincing for why code reviews are important:

* [Why code reviews matter (and actually save time)](https://www.atlassian.com/agile/software-development/code-reviews)
* [The Importance of Code Reviews](https://www.sitepoint.com/the-importance-of-code-reviews/)
* [Code Review Checklist - To Perform Effective Code Reviews](https://www.evoketechnologies.com/blog/code-review-checklist-perform-effective-code-reviews/)
* [The Code Review Pyramid](https://www.morling.dev/blog/the-code-review-pyramid/)
