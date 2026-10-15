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

## step-01 Before any test

$ just setup

tally counts words, and nothing checks it. Read `count` in `src/tally/__init__.py`, and ask what could go wrong.

$ echo "a b a" | uv run tally
$ echo "The the" | uv run tally

The second command counts "The" and "the" as two words. Nothing says whether that is right.

> Ask: write down one test you would add, before we look at ours.

files:
- file `src/tally/__init__.py`: `count`, and the command that uses it.
  show: `for word in text.split():`

## step-02 Two tests

$ just setup

A test gives `count` a text, and compares what it gives back with what we expect. `tests/test_count.py` holds two
tests, and `tests/test_empty.py` one. The test of case failed until `count` ignored case, with one word, `lower()`.

$ uv run python -m unittest discover -s tests -v
$ echo "The the" | uv run tally

All three tests pass, and the command now prints `2 the` for "The the".

files:
- diff `tests/test_count.py`: a test of counting, and a test of case.
  show: `self.assertEqual(count("A a"), {"a": 2})`
- diff `tests/test_empty.py`: an empty text counts nothing.
- diff `src/tally/__init__.py`: the word that answers the test of case.
  show: `for word in text.lower().split():`

## step-05 Still green

$ just setup

Much later, `top` comes with a test of its own, and the old tests still pass. The types and the docstring that came
in between did not change a test. The tests check what the code does, and not how it says it.

$ uv run python -m unittest discover -s tests -v
$ uv run tally --top 3 data/corpus.txt

The four tests pass, and the command prints the three most common words of the corpus.

files:
- file `tests/test_top.py`: the new test, of `top`.
- diff `src/tally/__init__.py`: types, a docstring, `top` and the option `--top`.
- diff `pyproject.toml`: ruff, and its line length.
- diff `uv.lock`: the exact version of ruff.
- diff `justfile`: `just setup` makes the corpus and the report.
- diff `scripts/corpus.py`: the script that writes the corpus.
- diff `.gitignore`: git does not hold the corpus.
