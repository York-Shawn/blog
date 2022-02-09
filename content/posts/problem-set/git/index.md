---
title: "Git Problem Set"
subtitle: ""
date: 2020-10-17T23:05:41+08:00
lastmod: 2020-10-17T23:05:41+08:00
draft: false
author: "Aya"
authorLink: ""

tags: ["Git"]
categories: ["Problem-Set"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "featured-image.webp"

toc:
  enable: true
  auto: false
math:
  enable: false
lightgallery: true
license: '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank">CC BY-NC 4.0</a>'
---

<!--more-->
## 1. ssh can't connect to github

Description: error `kex_exchange_identification: Connection closed by remote host` poped up when pushing to github. Got the same error message by running ` ssh -T git@github.com` 

Solution: Use another VPN

## 2. fast-forward when pushing to remote

Solution: merge or fetch before push.

{{< admonition type=tip open=true >}}
`git push -f ...` also  works, but the repercussion might be irreversible and perilous
{{< /admonition>}}