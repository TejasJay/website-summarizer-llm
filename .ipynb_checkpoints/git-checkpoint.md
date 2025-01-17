# GitHub CLI Commands for Repository Setup and Push

This guide will walk you through the steps to authenticate with GitHub, create a repository, and push your project to GitHub.

## 1. **Login to GitHub CLI**

Authenticate the GitHub CLI to use your GitHub account.

gh auth login
gh repo create your-repository-name --public
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:TejasJay/website-summarizer-llm.git
git push -u origin main