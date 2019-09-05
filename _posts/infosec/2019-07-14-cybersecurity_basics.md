---
layout: post
title:  "Networking Basics"
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

# Emails
## General overview - SMTP
- Connection-oriented, text-based protocol
- SMTP transactions
  - *MAIL* establishes return path, bounce address, sender
  - *RCPT* establishes address of recipient
  - *DATA* contains email header and content

1. Send an email
2. Transmitted to outgoing mail server
3. Outgoing mail server looks at the server handling the domain the email is going to
4. Transmit to incoming mail server
5. Incoming mail server looks up the email account
6. Recipient email client syncs inbox

## Protocols for syncing email folder with server
- Post Office Protocol 3 (POP3): Email is stored until you sync with the server, then it is deleted
  - Risk losing emails
  - Can't have multiple email accounts
- Internet Message Access Protocol (IMAP): Email stored and remains on server until deleted
  - Can track state on each email
  - Can sync message state across devices
  - Server-side search
- Microsoft exchange
  - Restricted Windows Server

## Spoofing
- SMTP doesn't require authentication, anyone can change the FROM address
- Sender Policy Framework (SPF): in the DNS config for your domain, you put a record of all the IP addresses of mail servers allowed to send emails from your domain
  - If an email is sent with your from address but not from one of the ips, it is spoofed
- DKIM: Putting a public key in the text record in your DNS setting. When your email server sends an email, it will sign the email with a public key and can be verified by checking the DNS records for the public key

# Misc

### Google dorking

> We can use google to search for unsecured security cameras, passwords, software, documents etc.

- Searching for sites -> i.e. site:bbc.co.uk hacker
- Searching for files -> i.e. filetype:pdf fun
- Wildcard -> i.e. something is *
- Quotes "" allows us to search verbatim