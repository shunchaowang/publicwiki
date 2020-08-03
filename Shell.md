# SSH Without a Password
* Generate SSH pairs on local machine
    + Create .ssh dir: `mkdir -p ~/.ssh`
    + Verify .ssh has permission 0700
    + Generate SSH key: `ssh-keygen`, this sould result in two file under ~/.ssh/
        - id_rsa is the private key, should be kept secret
        - id_rsa.pub is the public key, it will be used to append to the remote
* Create .ssh folder on remote machine
    * `ssh <user>@<remote> mkdir .ssh` 
* Append local id_rsa.pub to remote machine
    * `cat ~/.ssh/id_rsa.pub | ssh <user>@<remote> 'cat >> .ssh/authorized_keys'`  
    * Verify ~/.ssh/authorized_keys has permission 0600
    * Verify `ssh <user>@<remote>` is not prompting for password
* Create an alias to access remote machine
    * Add an alias to bash or zsh rc file `alias ssh-<remote>="ssh <user>@<remote>"` 
***
# Brackets and Parentheses in Bash
* Brackets **[]**
    * if `[ condition ]` # test condition
    * if `[[ condition ]]` # bash buildin test condition
    * `array[i]` # array munipulation
    * `[a-z]` # range of characters in a regular expression
* Curly Brackets **{}**
    * `${variable}` # parameter substitution
    * `${!variable}` # indirect variable reference
    * `{ cmd1; cmd2; ... ; cmdN }` # block of code
    * `{string1, string2, ..., stringN}` # brack expansion
    * `{a..z}` # extended brack expansion
    * `{ }` # text replacement, after find and xargs
* Parentheses __()__
    * `( cmd1; cmd2; ..., cmdN )` # command group executed within a subshell
    * `array=(element1 element2 ... elementN)` # array initialization
    * `result=$(cmd)` # command substitution like '``'
    * `>(cmd)` # process substitution
    * `<(cmd)` # process substitution
* Double Parentheses __(())__
    * `(( var=7 ))` # integer arithmetic
    * `var=$(( 20 + 5 ))` # integer arithmetic with variable assignment
    * `(( var++ )) (( var— ))` # c-style variable increment/decrement
    * `(( var0 = var1 < 98 ? 9 : 21 ))` # c-style trinary operation
***
# Pass Env Virables to sudo
> run `sudo visudo`, find the line `Defaults        env_reset`, add one line after it `Defaults env_keep += "<e0> <e1>"`
> this will make sure the `e0 e1` passed to sudo command
# Set environments system wide
> put the variable into /etc/environment
# use SSH to forward the port
> SSH forwarding is a good way to test things, example:
`ssh -L localhost:3838:bmi-faqs-vt01.bmi.osumc.edu:3838 <user@>bmi-faqs-vt01.bmi.osumc.edu`
then got to `http://localhost:3838` and will see the content from r-shiny.
The command opens a connection to the bmi-faqs-vt01.bmi.osumc.edu jump server, and forwards any connection to port 3838
on the local machine to port 3838 on bmi-faqs-vt01.bmi.osumc.edu. `localhost:` can be `127.0.0.1` or omited. Details
about the command: `ssh -L localhost:<port>:<remote server>:<port> <jump server>` the jump server should have access to
remote server to to ssh forwarding. <user@> is needed if the ssh user to the jump server is different than the local
shell user.

# Check User Info
To check the user's login info including the groups by running command: id <user>

