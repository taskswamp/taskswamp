What is this?
=============

Taskswamp is a scriptlet that creates a `tmux` session with multiple `task`
reports and allows those reports to be easily refreshed.  Each tmux window
shows a single taskwarrior report.

Quick start
===========

To start, create a config file in `~/.taskswamprc`, following the example
below, and then run `./taskswamp`:

    % cat >> ~/.taskswamprc <<'EOF'
    { "taskswamp": -1,
      "refresh_every": 0,
      "windows": [
        { "name": "main", "task": "" },
        { "name": "waiting", "task": "waiting" },
        { "name": "myproject", "task": "project:myproject due:today ls limit:page" }
      ]
    }
    EOF
    % ./taskswamp

If you get an error, make sure you have the [dependencies](#dependencies) installed.

The config file format is json.  The `taskswamp` key must be set to `-1` (this
is a format number for forward compatibility).  The `windows` key must be set
to an array of window specifications, where each specification contains
a `name` (shown in the tmux status bar) and a `task` report to show.
The value of the `task` key is a task command-line, as a single string.
(If you type at the shell prompt `task foo bar baz`, you would put `"task":
"foo bar baz"` in the window specification.)

It is also possible to specify arbitrary commands using a `cmd` key, via
`"cmd": "echo hello world"` or `"cmd": ["echo", "hello", "world"]`.

FAQ
===

How do I refresh the task report?
---------------------------------

If the optional `refresh_every` key is set to a value greater than 0, the task
report is automaticaly refreshed every specified seconds.

Else, press any key except `y`.  (If you press `y`, the last display will remain,
but will not be updated until you use the tmux `respawn-window` colon command.)

Why doesn't `taskswamp` have a usage (`--help`) message, packaging glue, etc?
-----------------------------------------------------------------------------

Because taskswamp has no known users.  It wouldn't be hard to add all these
niceties, once this project has at least one user.

Dependencies
------------

Required: Python 3; Taskwarrior; flock(1); tmux; zsh.

Is `screen` supported?
----------------------

No, but such support would be easy to add.  `screen` can create a detached
session with multiple windows:

    screen -dmS $sessionname
    screen -S $sessionname -X screen $cmd
    ...
    screen -r $sessioname

It's just a matter of writing the glue code to invoke `screen` and to choose
between screen and tmux.

Known issues
------------

The config file is json because that's easiest to parse.

`assert` is used for input validation.  If this project ever has users (which
currently it doesn't), invalid input should result not in an `AssertionError`
traceback but in a normal error message (as in the existing `FileNotFoundError`
handling).

The code spawns a new XTerm.  Options to spawn other terminal emulators (or to
just start tmux) have not been implemented.
