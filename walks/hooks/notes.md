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

## hooks-00 Where the hooks start

$ just setup

tally counts the words of a text. Nothing checks the code before a `git commit`, so a file with a syntax error can
go into git. The recipes of the `justfile` show what the project can do now.

$ just --list

It lists the recipes of tally, and none of them checks the code.

## hooks-01 The hook

$ just setup

A check that each person must remember to run is a check that someone forgets. We write the check as a recipe.
Then a hook runs the recipe before each `git commit`, and a last recipe tells git to use the hook.

### hooks-01.1 A recipe that checks the code

A file with a syntax error breaks tally, and nothing finds it before it goes into git. A recipe `check` compiles
every Python file of tally. Python then reports a syntax error at once, without running the code.

patch$ justfile

::: do
The patch adds the recipe `check` at the end of the `justfile`.
:::

::: watch
At the end of the `justfile`, the new recipe `check` runs `py_compile`, the compiler of Python, on each file of
`src/tally`.
:::

$ just check && echo "the code compiles"

It prints `the code compiles`, because every file compiles.

files:
- diff `justfile`: the recipe compiles every Python file.
  show: `uv run python -m py_compile src/tally/*.py`

### hooks-01.2 A hook script

`just check` works only when someone runs it. A hook runs it every time. git runs the file `pre-commit` of its
folder of hooks before each `git commit`, and stops the `git commit` when the file fails. The hook,
`hooks/pre-commit`, has three lines. They name the shell that runs it, say what it does, and run the check. The
file comes whole, as a patch.

patch$ hooks/pre-commit

::: do
Read its last line, `just check`. It is the whole of what the hook does.
:::

::: watch
The new file `hooks/pre-commit` runs `just check`, and nothing else. When `just check` fails, the hook fails too.
:::

$ sh hooks/pre-commit; echo "the hook said $?"

The code compiles, so the hook says 0.

files:
- file `hooks/pre-commit`: three lines that git runs before a `git commit`.
- diff `hooks/pre-commit`: the hook only runs the check.
  show: `just check`

### hooks-01.3 Make it runnable

git runs a hook only if the hook is executable, so we make it executable.

do$ chmod +x hooks/pre-commit

Run the hook by hand on a broken file, and see it fail. Then remove the file:

$ printf 'def (\n' > src/tally/broken.py; ./hooks/pre-commit; echo "the hook said $?"
$ rm src/tally/broken.py

The hook says 1, so git would stop the `git commit`.

### hooks-01.4 Install it

git does not copy hooks with a repository, and does not look in the folder `hooks` unless it is told to. So each
copy of the project installs the hook. A recipe `install-hook` sets `core.hooksPath`, the setting of git that names
the folder of hooks. The recipe changes the settings of git, so it is a recipe of its own, and not a part of
`just setup`. `just setup` only runs `uv sync`, which makes the environment.

patch$ justfile

::: do
The patch adds the recipe `install-hook`, with its comment, at the end of the `justfile`.
:::

::: watch
At the end of the `justfile`, the new recipe `install-hook` runs one line, `git config core.hooksPath hooks`.
:::

$ just install-hook && git config core.hooksPath

It prints `hooks`, so git now runs the hook before each `git commit`.

files:
- diff `justfile`: one line of git config, in a recipe of its own.
  show: `git config core.hooksPath hooks`

## hooks-02 Tests in the hook

$ just setup

Code that compiles can still count wrong. The tests find that, so the hook must run them too. First we write a
test, then the hook runs the tests after the check.

### hooks-02.1 A first test

The hook can run tests only if there are tests. A first test, in `tests/test_count.py`, checks that `count` counts
the words of `a b a`.

patch$ tests/test_count.py

::: do
The patch makes the file `tests/test_count.py`, with the class `Count` and its test.
:::

::: watch
The new file `tests/test_count.py` has one test, `test_words`. It expects `{"a": 2, "b": 1}` for `a b a`.
:::

$ uv run python -m unittest discover -s tests -q

The test passes.

files:
- diff `tests/test_count.py`: one test of counting.
  show: `self.assertEqual(count("a b a"), {"a": 2, "b": 1})`

### hooks-02.2 The hook runs the tests too

The hook compiles the code, but a failing test does not stop a `git commit`. One more line in the hook runs the
tests after the check.

patch$ hooks/pre-commit

::: do
The patch adds one line at the end of `hooks/pre-commit`, after the line `just check`.
:::

::: watch
At the end of `hooks/pre-commit`, the new line runs the tests after `just check`.
:::

$ sh hooks/pre-commit && echo "the hook passed"

The hook compiles the code, runs the tests, and prints `the hook passed`.

files:
- diff `hooks/pre-commit`: the tests run after the check.
  show: `uv run python -m unittest discover -s tests -q`
