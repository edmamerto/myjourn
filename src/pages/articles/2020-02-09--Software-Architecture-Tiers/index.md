---
title: Software Architecture Tiers
date: "2020-02-09T16:51:00.000Z"
layout: post
draft: false
path: "/posts/software-architecture-tiers/"
category: "Gatsby"
tags:
  - "Systems Design"
  - "Interviewing"
description: "Different tiers and their benefits with concepts and examples"
---

## Software Architecture Tiers

Why am I writing this?
- this can be handy in interviews
- helps with work

## Single tier
- UI, Backend and DB are all in one machine
	- i.e. Microsoft Office, PC games

Advantages
- No network latency adds up to software performance
- User data stays in machine and doesn't need to be transmitted over a network ensures data safety at the highest level.

Disadvantages
- Even if it is just making a change to one component, you'd still have to ship the whole thing
- If app is shipped with a bug, customer has to manually download the patch

## Two tier
- Involves a client and a server
- `tier1` client is UI + Business logic
- `tier2`  DB running on a different machine

Advantages
- Network latency is still relatively low
- Network calls to DB are only made to persist state

Disadvantages
- Needs internet for network calls

## Three tier
- `tier1` UI is client
- `tier2` Business logic lives in a seprate server
- `tier3` DB is in a separate server

Advantages

- Lesser bugs, i.e. A change in the UI won't accidentally affect the business logic
- Tier specific engineering roles make for better code quality
- Splitting implementation makes it easier to find things
- As a side effect to advatanges, code is shipped faster

Disadvantages
- More costly since we are maintaining more machines
- Network latency is relatively higher with more network calls 

## N tier
- **More than three** components involved
- A.k.a “distributed applications”

Examples of other components

-   _Cache_
-   _Message queues for asynchronous behaviour_
-   _Load balancers_
-   _Search servers for searching through massive amounts of data_
-   _Components involved in processing massive amounts of data_
-   _Components running heterogeneous tech commonly known as web services_  etc.

### Why The Need For So Many Tiers?  

Two software design principles that are key to explaining this are the  _Single Responsibility Principle_  & the  _Separation of Concerns_.

### Separation of Concerns
- SoC is a Design principle or concept that suggests to break your application into parts that can be independently solved and optimized.

### Single Responsibility Principle
- simply means giving one, just one responsibility to a component & letting it execute it with perfection

**Examples**

Component down
- When DB goes down only features requiring DB access will not work, rest can still work

Stored procedures
- Enable us to add business logic to the database
- A database should not hold business logic, it should only take care of persisting the data.

### Layers vs. Tiers
- Tier is physical separation of components
- Layers represent the organization of the code and breaking it into components (Code level)