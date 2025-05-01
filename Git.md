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
### set up proxy for git
Git proxy can be set/unset globally
- to set up global proxy
* run command 
  ```
  git config --global http.proxy <http_proxy>
  git config --global https.proxy <https_proxy>
  ```
- to unset global proxy
  ```
  git config --global --unset http.proxy
  git config --global --unset https.proxy 
  ```
- to set up local proxy
- to unset local proxy
### Remove files from commit history
`git-filter-repo` is a command line tool to remove the sensitive or large files from commit history both locally and on
remote.
1. install `git-filter-repo` using homebrew `brew install git-filter-repo`
2. clone the repo running `git clone --mirror <repo_url>`
3. remove the files, `cd` to the cloned repo, run below command to remove the file, run multi times if there are multi
   files
   ```
   git filter-repo --invert-paths --path <path-to-the-file>
  Parsed 197 commits
  New history written in 0.11 seconds; now repacking/cleaning...
  Repacking your repo and cleaning out old unneeded objects
  Enumerating objects: 210, done.
  Counting objects: 100% (210/210), done.
  Delta compression using up to 12 threads
  Compressing objects: 100% (127/127), done.
  Writing objects: 100% (210/210), done.
  Building bitmaps: 100% (48/48), done.
  Total 210 (delta 98), reused 144 (delta 75), pack-reused 0
  Completely finished after 0.64 seconds. 
  ```
4. add the files to git ignore to not track running `echo "path-to-the-file" >> .gitignore`
5. push to the remote by running `git push origin --force --all`

