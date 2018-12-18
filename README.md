# safe-git

## AWS creds on local repo

```
git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}" $(git rev-list --all)
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
