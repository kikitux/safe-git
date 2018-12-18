# safe-git

## AWS creds on local repo

To check for AWS creds manually, on a git repo, you can use.

```
git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}" $(git rev-list --all)
```

## AWS creds on git project, as hook

`.git/hooks/pre-commit` with the following content

```
#!/bin/sh

FILES=$(git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}")
if [ $? -eq 0 ];then
  echo "err: commit include regex for AWS"
  echo ${FILES}
  echo "use git rm to remove files and avoid commiting your creds"
  echo ""

  for f in $FILES; do
    echo git rm --cached ${f%%:*}
  done

  echo ""
  exit 1
fi
```

ensure the file have the right permissions

```
chmod +x .git/hooks/pre-commit
```

## truffleHog

### How to install

```
pip install truffleHog
```

### Scan local repo

```
trufflehog --regex --entropy false --rules <(curl -sL https://raw.githubusercontent.com/kikitux/safe-git/master/regexes.json) .
```

### Scan remote repo

```
trufflehog --regex --entropy false --rules <(curl -sL https://raw.githubusercontent.com/kikitux/safe-git/master/regexes.json) http://github.com/user/repo
```
