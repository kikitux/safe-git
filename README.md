# safe-git

## Check for AWS creds in git

- Enable git templates:
```bash
git config --global init.templatedir '~/.git-templates'
```

- Create a directory to hold the global hooks:
```bash
mkdir -p ~/.git-templates/hooks
```

- Create `pre-commit` hook in ~/.git-templates/hooks
```bash
vi ~/.git-templates/hooks/pre-commit
```

add the following content
```bash
#!/usr/bin/env bash

FILES=$(git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}")
if [ $? -eq 0 ];then
  echo "err: commit include regex for AWS"
  echo ${FILES}
  echo "use git rm to remove files and avoid commiting your creds"
  echo ""

  for f in ${FILES}; do
    echo git rm --cached ${f%%:*}
  done

  echo ""
  exit 1
fi
```

or download the file

```
curl -L -o ~/.git-templates/hooks/pre-commit https://raw.githubusercontent.com/kikitux/safe-git/master/pre-commit
```

- Make sure the hook is executable.
```bash
chmod a+x ~/.git-templates/hooks/pre-commit
```

### test it works

```bash
mkdir catch-aws
cd catch-aws
git init
echo 'AKIAAAAAAAAAAAAAAAAA' | tee bad.txt
git add bad.txt
git commit -m "test"
```

This should fail to commit, and you should see a message like this:

```
$ git commit -m "test"
err: commit include regex for AWS
bad.txt:AKIAAAAAAAAAAAAAAAAA
use git rm to remove files and avoid commiting your creds

git rm --cached bad.txt
```

### for existing repos

If you have an existing git repo, you can re-initialize so the new hook get copied:
```bash
git init
```


## Check AWS creds on local repo

To check for AWS creds manually, on a git repo, you can use.

```
git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}" $(git rev-list --all)
```
