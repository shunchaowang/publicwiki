### Restore SVN Dump of Single Project 
1. Create local svn repo path
`svnadmin create repo_path` # this will create a repo_path
2. Load dump file to local svn repo
`svnadmin load repo_path < file.dump` # this will load file.dump into repo_path with all svn history, trunk and branches
3. Check out local svn into local project path
`svn checkout file:///repo_path project_path` # this will check out the repo_path project into project_path
