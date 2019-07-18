---
layout: post
title:  "Comptia Security+ Essential Notes"
date:   2019-07-18 15:53:45 +0800
categories: infosec
---
> Essential notes for the Comptia Security+ Exam

This post will contain all my study notes for the comptia security+ exam.

## Goals

The Comptia Security+ exam can be broken down into the following objectives (percentage of coverage included):

1. Threats, Attacks and Vulnerabilities (21%)
2. Technologies and Tools (22%)
3. Architecture and Design (15%)
4. Identity and Access Management (16%)
5. Risk Management (14%)
6. Cryptography and PKI (12%)

> I will complete the book Security+ GCGA in 6 weeks. Since there are approximately 440 pages worth of material and 42 days till I'm ready, it will be ~10 pages a day + 10 more days to practice.

# Chapter 1

## Mastering Security Basics
- Some basic terminology:
  - **CIA security triad**: Confidentiality, Integrity and Availability
    - *Confidentiality* prevents the unauthorized disclosure of data
    - 
  - **Use case**: A goal an organization wants to achieve
    - *Actors*: People or items involved contributing to the use case
    - *Precondition*: Event occuring before the process begins
    - *Trigger*: Event that starts the use case
    - *Postcondition*: Events occuring after the actor triggers the process
    - *Normal Flow*: A use case lists each of the steps in order
    - *Alternate Flow*: A set of events occuring in the use case differing from the normal flow
  - **PII**: Personally Identifiable Information (i.e. Medical info, Credit card #)
#### Ensuring Confidentialty - A use case
We have to make sure authorized personnel can access data but unauthorized personnel can't. Here are some methods:
1. Encryption
   - Scrambles data to make it unreadable
   - Only authorized personnel can decrypt and access
2. Access Control 
   - Combination of Identification, Authentication and Authorization to grant and restrict access
     - *Identification*: User claims an identity that is unique
     - **