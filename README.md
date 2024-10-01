# Obsidian - One vault with multiple repos on different instances

1. Use this as the main folder
2. Create folders as repos on every Github instance you want
3. Add the folder of each device to the gitignore, optionally every file in this folder.
4. Create the pre-commit and pre-push hooks in .git/hooks/

Create the file `pre-commit`. You need to update the function calls.
```shell
#!/bin/sh

# Function to commit changes in a sub-repo
commit_subrepo() {
subrepo_path=$1
commit_message=$2
commit_mail=$3

git config --global user.email $commit_mail
cd "$subrepo_path"
if [ -n "$(git status --porcelain)" ]; then
git add .
git commit -m "$commit_message"
else
echo "No changes to commit in $subrepo_path"
fi
cd - > /dev/null
}

# Commit changes in the learning sub-repo
commit_subrepo "folder1" "Auto-commit from main repo" "asdf@asdf.de"

# Commit changes in the work sub-repo
commit_subrepo "folder2" "Auto-commit from main repo" "asdf@asdf.de"

git config --global user.email "asdf@asdf.de"

# Ensure the script exits with a zero status
exit 0

```

Create the file `pre-push`. You need to update the function calls.
```shell
#!/bin/sh

# Function to push changes in a sub-repo
push_subrepo() {
subrepo_path=$1

cd "$subrepo_path"
git push
cd - > /dev/null
}

# Push changes in the learning sub-repo
push_subrepo "folder1"

# Push changes in the work sub-repo
push_subrepo "folder2"

# Ensure the script exits with a zero status
exit 0

```
5. Create shellcommands for obsidian integration
https://github.com/Taitava/obsidian-shellcommands
As there is no hook for pulling, we need a shellcommand for that. It is also used for comitting and pushing as with the normal obsidian sync there seems to be problem.

Create a pull script for every device:
```shell
git pull && cd folder1 && git pull && cd ../folder2 && git pull
```

Create one push script:
```
git push
```

Create one commit script:
```shell
git add . && git commit -m "Automatic vault update"
```

For the push and and commit script the hooks are called.
