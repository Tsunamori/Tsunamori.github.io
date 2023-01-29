---
title: 敏感信息泄漏
toc: true
date: 2021-04-14 14:42:34
tags: Summary
categories: [100 Web security, 110 Bug bounty, 111 Ideas/WP]
---

# Recon 

## Github recon

1. steps
    1. Check github with company name for API keys or passswords.
    1. Enumerate the employees of the company from linkedin and twitter and check their repositories on github for sensitive information.
    1. Check source code of main website and subdomains for github links in the html comments or anywhere. Search using ctl-F and search for keyword github

1. tools
    1. https://github.com/BishopFox/GitGot
    1. https://github.com/hisxo/gitGraber
    1. https://github.com/tillson/git-hound
    1. https://securitytrails.com/blog/github-dorks

1. Github dorks
* GitHub Dorks for Finding Files
    filename:manifest.xml
    filename:travis.yml
    filename:vim_settings.xml
    filename:database
    filename:prod.exs NOT prod.secret.exs
    filename:prod.secret.exs
    filename:.npmrc _auth
    filename:.dockercfg auth
    filename:WebServers.xml
    filename:.bash_history
    filename:sftp-config.json
    filename:sftp.json path:.vscode
    filename:secrets.yml password
    filename:.esmtprc password
    filename:passwd path:etc
    filename:dbeaver-data-sources.xml
    path:sites databases password
    filename:config.php dbpasswd
    filename:prod.secret.exs
    filename:configuration.php JConfig password
    filename:.sh_history
    shodan_api_key language:python
    filename:shadow path:etc
    JEKYLL_GITHUB_TOKEN
    filename:proftpdpasswd
    filename:.pgpass
    filename:idea14.key
    filename:hub oauth_token
    HEROKU_API_KEY language:json
    HEROKU_API_KEY language:shell
    SF_USERNAME salesforce
    filename:.bash_profile aws
    extension:json api.forecast.io
    filename:.env MAIL_HOST=smtp.gmail.com
    filename:wp-config.php
    extension:sql mysql dump
    filename:credentials aws_access_key_id
    filename:id_rsa or filename:id_dsa
GitHub Dorks for Finding Languages
    language:python username
    language:php username
    language:sql username
    language:html password
    language:perl password
    language:shell username
    language:java api
    HOMEBREW_GITHUB_API_TOKEN language:shell
GiHub Dorks for Finding API Keys, Tokens and Passwords
    api_key
    “api keys”
    authorization_bearer:
    oauth
    auth
    authentication
    client_secret
    api_token:
    “api token”
    client_id
    password
    user_password
    user_pass
    passcode
    client_secret
    secret
    password hash
    OTP
    user auth
GitHub Dorks for Finding Usernames
    user:name (user:admin)
    org:name (org:google type:users)
    in:login ( in:login)
    in:name ( in:name)
    fullname:firstname lastname (fullname: )
    in:email (data in:email)
GitHub Dorks for Finding Information using Dates
    created:<2012–04–05
    created:>=2011–06–12
    created:2016–02–07 location:iceland
    created:2011–04–06..2013–01–14  in:username
GitHub Dorks for Finding Information using Extension
    extension:pem private
    extension:ppk private
    extension:sql mysql dump
    extension:sql mysql dump password
    extension:json [api.forecast.io] (http://api.forecast.io/)
    extension:json [mongolab.com] (http://mongolab.com/)
    extension:yaml [mongolab.com] (http://mongolab.com/)
    [WFClient] Password= extension:ica
    extension:avastlic “[support.avast.com] (http://support.avast.com/)”
    extension:json googleusercontent client_secret

https://kathan19.gitbook.io/howtohunt/sensitive-info-leaks/github_dorks_all

## Google recon

1. Google dorks https://kathan19.gitbook.io/howtohunt/sensitive-info-leaks/google_dorks

## Shodan recon

1. Big IP shodan Search:-
`http.title:"BIG-IP&reg;-Redirect" org:Org`
1. CVE 2020-3452
`http.html_hash:-628873716 “set-cookie: webvpn;”`
1. CVE CVE-2019-11510
`http.html:/dana-na/`

## 图片地理信息泄漏

1. EXIF geo data not stripped (sensitive info leaked)
    * tools
      http://exif.regex.info/exif.cgi