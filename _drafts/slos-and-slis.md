---
title: Searching PRs with gh cli and parsing its response
date: 2024-02-16 21:35:00
excerpt: 'A little advice to not destroy your Friday afternoon trying to make gh work'
categories:
  - blog
tags:
  - bash
  - git
header:
  image: '/images/header.jpg'
---
# Intro

It is important to know what user expects and what is considered a poor experience, so you can gather data based on data. Data will allow you to define SLOs (`Service Level Objetives`), which is the level of service degradation that you are allow to have without penalizing user experience too much.

# Reliability

- 100% reliability is not practical. It reduce time to implement new features and it also involves lot of resources.

# SLI

Technical indicators of user experience.

# SLOs

SLOs are reliability promises. They are based on SLIs.

SLOs are also a indicator of system stability. There is a tradeoff between implementing features and reliability. More precisely, there is kind of inverse proportional relationship. I mean, introducing new features increases the possibility of service being unstable.

# Error budget

Useful to explain the reliability status to other people.
