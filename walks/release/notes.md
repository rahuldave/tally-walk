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

## release-00 Where the release starts

$ just setup

tally counts the words of a text, and two files of tests check it. Run the tests, to see where we begin.

$ uv run python -m unittest discover -s tests -q

All three tests pass.

## release-01 Release 0.2.0

$ just setup

A release gives users a new version. Before it, we tidy the tests, and after it, the lockfile must name the new
version too.

### release-01.1 Tests of edge cases

The file `tests/test_empty.py` tests one edge case, an empty text. A text of spaces alone is another edge case. One
file can test both, so it gets a name that fits both, `tests/test_edge.py`, and a second test.

patch$ tests/test_empty.py
patch$ tests/test_edge.py

::: do
The patch renames `tests/test_empty.py` to `tests/test_edge.py`. It also renames the class to `Edge`, and adds the
test `test_spaces`.
:::

::: watch
The file `tests/test_empty.py` is now `tests/test_edge.py`. Its class is `Edge`, and its new test `test_spaces`
expects no words in a text of spaces, tabs and a newline.
:::

$ uv run python -m unittest discover -s tests -q

All four tests pass.

files:
- diff `tests/test_edge.py`: a new name, and a second test.
  show: `def test_spaces(self):`

### release-01.2 Version 0.2.0

Users tell one release from another by its version. The version is a line of `pyproject.toml`.

patch$ pyproject.toml

::: do
The patch changes one line of `pyproject.toml`, `version`, from `0.1.0` to `0.2.0`.
:::

::: watch
In `pyproject.toml`, the line `version` now says `0.2.0`.
:::

$ grep -n '^version' pyproject.toml

It prints `3:version = "0.2.0"`. The lockfile, `uv.lock`, still names version 0.1.0 of tally, so it is out of date.
The next move brings it up to date.

files:
- diff `pyproject.toml`: the new version.
  show: `version = "0.2.0"`

### release-01.3 The lock, and the list of changes

`uv.lock` records the exact version of each package of the project, tally too. `uv lock` writes it again from
`pyproject.toml`:

do$ uv lock

A user of a release wants to know what changed. A new file, `CHANGES.md`, says it for 0.2.0.

patch$ CHANGES.md

::: do
The patch makes the file `CHANGES.md`, with a heading for 0.2.0 and one sentence.
:::

::: watch
The new file `CHANGES.md` has a heading for 0.2.0, and says what changed in the tests.
:::

$ uv lock --check && grep -A1 '^name = "tally"' uv.lock

`uv lock --check` prints nothing, because the lockfile is up to date. Then `grep` prints the entry of tally, with
`version = "0.2.0"`.

files:
- diff `uv.lock`: the version of tally.
  show: `version = "0.2.0"`
- diff `CHANGES.md`: the changes of 0.2.0.
