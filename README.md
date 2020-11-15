# hamster-tool

This repository hosts additional tools for the [Hamster time tracker](https://github.com/projecthamster/hamster).

Currently there is just one script called `htool` which performs data export and loading via XML or TSV intermediate files. The tool uses hamster's sqlite3 interface to hamster.db directly instead of using hamster's DBUS interface, thereby making it easier to work with more than one database.

The major current feature of `htool` is the possibility of **merging two databases** by exporting from the first and loading into the second. For example, you could merge this year's activities from a DB from a second machine present in say \~/Dropbox/hamster.db into your current DB (\~/.local/share/hamster/hamster.db) like this:

`htool --db-dir ~/Dropbox export tsv 2019-01-01 $(date +%y-%m-%d) | htool --log INFO load tsv`

Identical entries present in both databases will be silently skipped. Other incoming activities which overlap an existing activity in the destination DB, even by a single minute, will be logged and skipped. Newly added activities are logged.

**Warning**: Use at your own risk! Make sure to create a back-up copy of the destination DB before testing this tool.


## Installation

Just download the `htool` script to a directory in your path and make sure it is executable.

You will need to have Hamster v3.02 or later installed on your system (or virtual env).

## Usage
```
$ ./htool --help
usage: htool [-h] [-d DB_DIR] [--log {DEBUG,INFO,WARNING,ERROR,CRITICAL}]
             [{export,load}] ...

Extra tools for Hamster time tracker

positional arguments:
  {export,load}         action to perform
  action_args

optional arguments:
  -h, --help            show this help message and exit
  -d DB_DIR, --db-dir DB_DIR
                        directory of hamster.db (default:
                        ~/.local/share/hamster-applet)
  --log {DEBUG,INFO,WARNING,ERROR,CRITICAL}
                        set the logging level (default: WARNING)

Actions:
* export tsv|xml [start-date [end-date]]: Export activities with the
  specified format (default=tsv).

* load tsv|xml [filename]: Import activities from stdin or a file with
  the specified format.

NOTES:
* Date format is YYYY-MM-DD. A "hamster day" starts at the time set in the
  time tracking preferences (default 05:00) and ends one minute earlier the
  next day.

* For export, if start-date is missing, it will default to today. If end-date
  is missing, it will default to start-date.

* For load, exact duplicate activities are skipped, input activities which
  overlap existing activities are skipped and logged, and new activities are
  logged. Use --log INFO to get the message or omit --log for silent usage.
```

## Credits

The data loading function was developed starting from the contribution of @DefteZ
[here](https://github.com/projecthamster/hamster/pull/275).
