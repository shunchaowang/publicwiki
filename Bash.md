[##](##) If git bash on Windows runs very slowly, try to reset PS1. In ~/.bashrc file add this line:
PS1="\[\033]0;$TITLEPREFIX:$PWD\007\]\n\[\033[32m\]\u@\h \[\033[35m\]$MSYSTEM \[\033[33m\]\w\[\033[36m\]\[\033[0m\]\n$ "

#Bach Command Learned Daily
## look for the **content** in the **java** source files, recommend to use direct `grep` approach
- direct `grep` with `-exec`
`find . -type f -name '*.java' -exec grep -l 'content' {} +`  
`{}` is replaced by the file path. `+` means `find` will pass multiple filenames at once to `grep` (similar to `xargs`)
`-l` only lists matching filename
to run the grep ignoring the case, add the flag `-i` to run it case insensitive. `-I` is case sensitive by default.
this is similar to `find . -type f -name '*.java' | xargs grep 'content'`
- one file at a time
`find . -type f -name '*.java' -exec grep -l 'content' {} \;`
The `\;` runs `grep` once per file (less efficient than `+`, but more straightforward)
`-l` only lists matching filename, without `-l` the content in the files will be listed as well

## Append a local file to the file on the remote server
1. We can use `scp` to copy the file to the remote server, then append the content to the file.
2. The intuitive way is to `pipe` the `cat` output to the `ssh` command chain. The example is to copy my public key to
   the remote server for passwordless remote login.
```
cat ~/.ssh/<publicKey>.pub | ssh <user>@<host> 'cat >> ~/.ssh/authorized_keys'
```
