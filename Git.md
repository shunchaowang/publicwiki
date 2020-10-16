### Bash script to compare local repo with remote repo
```
#!/bin/sh
git fetch
UPSTREAM=${1:-'@{u}'}
LOCAL=$(git rev-parse @)
REMOTE=$(git rev-parse "$UPSTREAM")
BASE=$(git merge-base @ "$UPSTREAM")

if [ $LOCAL = $REMOTE ]; then
    echo "Up-to-date"
elif [ $LOCAL = $BASE ]; then
    echo "Need to pull"
elif [ $REMOTE = $BASE ]; then
    echo "Need to push"
else
    echo "Diverged"
fi
```
### git log --oneline --graph --decoreate --all
* `log` show commit logs
* `--oneline` shorthand for '--pretty=oneline --abbrev-commit' used together
* `-graph` Draw a text-based graphical representation of the commit history on the left hand side of the output.
    This may cause extra lines to be printed in between commits, in order for the graph history to be
    drawn properly
* `--no-decorate, --decorate[=short|full|no]` Print out the ref names of any commits that are shown. If short is specified, the ref name prefixes
    `refs/heads/`, `refs/tags/` and `refs/remotes/` will not be printed. If full is specified, the full ref
    name (including prefix) will be printed. The default option is **short**
* `--all` pretent as all ht erefs in refs/ are listed on the command line as <commit>
### git --no-pager log --since='2020-06-11' --before='10-31-2020'
* `--no-pager` must be placed as the first argument
* `--since='2020-06-11'` shows all logs since the date
* `--before='10-31-2020'` shows all logs before the date

