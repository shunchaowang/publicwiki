## Configure postgresql on Mac
1. Install by homebrew `brew install postgresql`
2. Start the service `brew services start postgresql`
3. Log in to the postgres db as admin `psql postgres`
## Add new role and grant permission
Once in the postgres prompt, run the below commands to create role and grant permission
- This will create a new role named at pguser `create role pguser with login password 'password';`
- Grant the createdb permission to pguser `alter role pguser CREATEDB;`
- Exit as admin `\q` and relog in as pguser `psql postgres -U pguser`
