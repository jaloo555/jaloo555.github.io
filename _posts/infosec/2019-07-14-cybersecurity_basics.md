---
layout: post
title:  "Cybersecurity Basics"
date:   2019-07-14 11:38:45 +0800
categories: infosec
---

> Notes on basic cybersecurity knowledge

# Networking

## IPv4
- Internet protocol version 4, in the format xxx.xxx.xxx.xxx, for example: 192.168.0.1
- The address is 4 bytes long, with each period separating a byte (32 bits in total)

## NAT (Network Address Translation)
- Taking a bunch of IP addresses and designate them as private
- Allows same private ip's in different networks (temporary solution for running out of ipv4 addresses)
- Public IP -> NAT -> Different computers with different private ip's under the same public ip
- Acts as a layer of defense since attackers can send a connection request to a public IP but the router would not know which computer to send it to

## IPv6
- Internet protocol version 6, an example would be 2001:0db8:0000:0000:0000:ff00:0042:8329. Shortened, it looks like this: 2001:db8::ff00:42:8329.
- 16 bytes, so 128 bits

## Subnet
- Splitting up networks into segments
- IP addresses are split into network identifier and host identifier
  - i.e. 192.168.0.1 w/ mask 255.255.0.0 -> 192.168 is network identifier, 0.1 is host identifier, allows 2 bytes (2ˆ16) devices
- Classless Inter-Domain Routing (CIDR)
  - 192.168.0.0 with a subnet mask of 255.255.0.0 then in CIDR notation that would be: 192.168.0.0/16
  - /16 is the number of bits a network identifier is

## Private IP Ranges

### IPv4
- 10.0.0.0/8
  - IP addresses: 10.0.0.0 -- 10.255.255.255
- 172.16.0.0/12
  - IP addresses: 172.16.0.0 -- 172.31.255.255
- 192.168.0.0/16
  - IP addresses: 192.168.0.0 - 192.168.255.255

> 127.0.0.1 is the localhost address

### IPv6
- Since NAT isn't needed, we don't need private ip addresses. Nevertheless, ::1 is localhost
- Subnets on IPv6 may have larger range than the entire IPv4 range and would be hard to search

# Misc

## File headers

- Commonly termed magic bytes / file signatures.
- Tells us the type of file at the beginning of a file
- A plain text file doesn't have a file header 
- 50 4B 03 04 (for a normal zip archive)
- 50 4B 05 06 (for an empty zip archive)
- 50 4B 07 08 (for a spanned zip archive)
- jpg files FF D8 FF DB or FF D8 FF E0 or FF D8 FF E1
- Uses pairs because 2 hex characters = 1 byte (8 bits) of data58 68 63 31 4a 69 36 43 64 7a

## Google dorking

> We can use google to search for unsecured security cameras, passwords, software, documents etc.

- Searching for sites -> i.e. site:bbc.co.uk hacker
- Searching for files -> i.e. filetype:pdf fun
- Wildcard -> i.e. something is *
- Quotes "" allows us to search verbatim