## git log - to check all commits
## git branch - to check all branches present in project
## git branch <branchName> - to create new branch
## git branch --d <branchName> - to delete a branch
## git merge <branchName to be merge in current branch> - to merge a branch
## git checkout <branchName to go inside that branch>- to change the branch
## git add <fileName to be to staged> - to stage single file 
## git add . 
## git commit -m "message" - to commit all changes 
## git log --oneline - it prints all the commits with their commit id 
## git reset <commitId> - it go to given commit id and then removes all the commits after that commit( it remove all the changes made after that commit)
## git revert <commitId> - it switches to just previous commit and add another required commit to that previous commit while keeping connected to latest commit
## git push-- to push the repo     

# 
# 
# to upload a folder of prebuilt project from local to github on master branch 
### cd path/to/your/project
### git init
### git remote add origin https://github.com/yourusername/your-repository-name.git
### git add .
### git commit -m "Initial commit"
### git push -u origin master




#
# 
# How to update an existing repo on github
### go to repo
### git add .
### git commit -m "commit message here" Enter
### git push origin main Enter
