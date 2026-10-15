# The walk through tally, step by step

This file holds the notes of every step of a walk. timewalk shows the notes of the current step in the column on the
right of the page.

- **A command** is a line that starts with `$ `. A click on it types the command into the terminal of the
  step, and you press Enter to run it. With **run on click** on, a click also runs it.
- **Other tabs:** a line that starts with `runs$ ` goes to the **Runs** tab, for a command that takes a
  while. A line that starts with `main$ ` goes to the **Main** tab, in `repo/`.
- **A cue** is a line that starts with `> `. The notes column shows it shaded.
- **Edit** at the top of the column changes the notes of the step, and **Save** writes them into
  this file. Commit it to your fork to keep your notes.
- **`$ just setup` starts every step.** It is the project's own recipe, at the step. It makes the environment
  of the step, and what the step needs.
- **A move keeps your work.** Before a move, timewalk keeps your edits and commits in `worktree/` on a branch
  `timewalk/saved/<step>`, and the page says how to get them back. In a `worktree/` that an older timewalk made, a
  move with edits asks first, and stashes them.

## parts-01 The code, with its test

$ just setup

Code and its test arrive together, so the test checks the code from the first day. Read `tests/test_count.py`,
then `count`.

$ uv run python -m unittest discover -s tests -v
$ git log --oneline --graph --all | head -12

Both tests pass. The graph shows the branch `parts`, which leaves `main` where the project begins.

> Ask: is it easier to read a test before the code, or after?

files:
- diff `tests/test_count.py`: a test of counting, and a test of case.
  show: `self.assertEqual(count("A a"), {"a": 2})`
- diff `src/tally/__init__.py`: `count`, which ignores case from the first day, and the command.
  show: `for word in text.lower().split():`

## parts-02 One more test

$ just setup

An empty text has no words, and `count` must give an empty dictionary for it. A test of an empty text says so. It
passes at once, because `count` already handles it.

$ uv run python -m unittest discover -s tests -v
$ echo "" | uv run tally && echo "(nothing, as the test says)"

The three tests pass, and tally prints nothing for an empty text.

files:
- diff `tests/test_empty.py`: an empty text counts nothing.
  show: `self.assertEqual(count(""), {})`
