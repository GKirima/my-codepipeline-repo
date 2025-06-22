## download the github cli using this link
*https://github.com/cli/cli/releases/tag/v2.74.2*<br>

- after installation is complete open the powershell or gitbash ternmial on your local machine<br>
## pass the follwing commands
*gh --version*<br>
- If it prints the version (e.g., gh version 2.42.1), then it’s properly installed and on your PATH.<br>
Run GitHub CLI<br>
### Once installed and on PATH, just run:<br>
*gh*

 
- Your GitHub CLI (gh) is correctly installed and accessible in Git Bash.<br>

### You’re all set to start using it to:<br>

Authenticate (*gh auth login*)<br>

- Create repositories<br>

- Clone repos<br>

- Manage issues & pull requests<br>

### run this command to login in to github using cli<br>
- *gh auth login*<br>

### Follow and respond to prompt by the cli; example:<br>

- $ gh auth login<br>
- ? Where do you use GitHub? *GitHub.com*<br>
- ? What is your preferred protocol for Git operations on this host? *HTTPS*<br>
- ? Authenticate Git with your GitHub credentials? *Yes*<br>
- ? How would you like to authenticate GitHub CLI? *Login with a web browser*<br>

! First copy your one-time code: *1234-5678*<br>
Press *Enter* to open *https://github.com/login/device* in your browser...<br> 

✓ Authentication complete.<br>
- gh config set -h github.com git_protocol https<br>
✓ Configured git protocol<br>
✓ Logged in as *abcd*<br>


### 2. Create a New GitHub Repository<br>
*gh repo create my-new-repo --public --source=. --remote=origin --push*<br>

This:<br>

- Creates a new repo on GitHub<br>

- Connects your local folder to it<br>

- Pushes your code<br>

### If you're starting from scratch:<br>
 *mkdir my-new-repo*<br>
*cd my-new-repo*<br>
*git init*<br>
*echo "# Hello GitHub CLI" > README.md*<br>
*git add .*<br>
*git commit -m "Initial commit"*<br>
*gh repo create my-new-repo --public --source=. --remote=origin --push*<br>

This:<br>

- Initializes Git<br>

- Creates a new GitHub repo<br>

- Pushes your code<br>

### Clone an Existing Repo
- *gh repo clone your-username/repo-name*<br>
- *cd repo-name*<br>


### Check Authentication Status
*gh auth status*<br>

### View Repo in Browser<br>
*gh repo view --web*<br>

### Make a Change and Push<br>
*echo "More features coming soon." >> README.md*<br>
*git add README.md*<br>
*git commit -m "Updated README"*<br>
*git push*<br>
