# download the github cli using this link
https://github.com/cli/cli/releases/tag/v2.74.2
# after installation is complete open the powershell or gitbash ternmial on your local machine
# pass the follwing commands
gh --version
# If it prints the version (e.g., gh version 2.42.1), then it’s properly installed and on your PATH.
Run GitHub CLI
# Once installed and on PATH, just run:
gh

to see help and commands.
<!-- Your GitHub CLI (gh) is correctly installed and accessible in Git Bash.

You’re all set to start using it to:

Authenticate (gh auth login)

Create repositories

Clone repos

Manage issues & pull requests -->

# run this command to login in to github using cli
gh auth login

# follow and respond to prompt by the cli; example:

<!-- $ gh auth login
? Where do you use GitHub? GitHub.com
? What is your preferred protocol for Git operations on this host? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Login with a web browser

! First copy your one-time code: 4741-4571
Press Enter to open https://github.com/login/device in your browser... 

✓ Authentication complete.
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as abcd -->


# 2. Create a New GitHub Repository
gh repo create my-new-repo --public --source=. --remote=origin --push

This:

Creates a new repo on GitHub

Connects your local folder to it

Pushes your code

# If you're starting from scratch:
<!-- mkdir my-new-repo 
cd my-new-repo
git init
echo "# Hello GitHub CLI" > README.md
git add .
git commit -m "Initial commit"
gh repo create my-new-repo --public --source=. --remote=origin --push

This:

Initializes Git

Creates a new GitHub repo

Pushes your code -->

# Clone an Existing Repo
gh repo clone your-username/repo-name
cd repo-name


# Check Authentication Status
gh auth status

# View Repo in Browser
gh repo view --web

# Make a Change and Push
echo "More features coming soon." >> README.md
git add README.md
git commit -m "Updated README"
git push
