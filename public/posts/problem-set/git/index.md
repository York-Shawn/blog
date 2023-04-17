# Git Problem Set


<!--more-->
## 1. ssh can't connect to github

Description: error `kex_exchange_identification: Connection closed by remote host` poped up when pushing to github. Got the same error message by running ` ssh -T git@github.com` 

Solution: Use another VPN

## 2. fast-forward when pushing to remote

Solution: merge or fetch before push.

{{< admonition type=tip open=true >}}
`git push -f ...` also  works, but the repercussion might be irreversible and perilous
{{< /admonition>}}
