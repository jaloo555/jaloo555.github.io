---
layout: post
title:  "Reconaissance Basics"
date:   2019-07-16 18:38:45 +0800
categories: infosec
---
> Overview on Red Team reconaissance

## Google dorking - what to do after dorking?

- Once we search for specific filetypes, we can get a good picture of the organisation by EXIF data (authors).

The following is an example building a user list:

```bash
metagoofil -d sans.org -t doc,pdf,xls -l 200 -o sans_files -f sans_results.html
```

## Robots

- *robots.txt* often contains a list of web pages that the company doesn't want to be indexed in search engines, it contains useful info.

## Wordlists

- Once we intercept password hashes going over the network. We can steal these and cracking them with a wordlist. Here's a common command to scrape websites for wordlists.

```bash
cewl -v -d 1 -w words.txt (target_url)
```

## Finding prior breaches

- User's often reuse passwords. Hence, previously breached passwords can be extremely valuable... We can check this site to get access to prior breached passwords and test them - https://haveibeenpwned.com/

## Whois

- We can also use *whois target_website* to search for who is responsible for the domain name.
- To get the website of a person, we can also reverse-dns lookup

