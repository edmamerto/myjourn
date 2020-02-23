---
title: Thundering herd
date: "2020-02-22T17:51:00.000Z"
layout: post
draft: false
path: "/posts/thundering-herd/"
category: "Gatsby"
tags:
  - "Algorithms"
  - "Interviewing"
  - "Python"
description: "An example scenario of how to mitigate a stampeding herd exploit (http flood)."
---



## HTTP flood (sort of)

> This is purely imaginative and does not reflect my frustrations for current or past employers.

Why am I writing this?
- Maybe it helps for interview questions like "Tell me about a situation where you had to deal with site issues, and how did you resolve it"

 Scenario

1. You get paged that site is down 
2. `php-fpm` active processes spiked 
3. Databases are healthy
4. Spike on throughput
5. This happened at at non-peak hours

What is `php-fpm`?
- it sits behind `nginx`
	- `nginx` is a `webserver`
- php-fpm is an `appserver`
- with `nginx + fpm`, processing your `php` pages is very fast, and makes your website perform faster
- Think of `php-fpm` as a plugin for nginx to process `php` because nginx alone can't do this 

Why were `php-fpm` active processes spiked?
- *Root Cause:* 
	- Network traffic was high and there weren't enough webservers to accomodate the requests coming in
	- Sudden burst of traffic 
- *Side effect 1:* 
	- The number of `php-fpm` active processes will be higher
- *Side effect 2:* 
	- CPU and RAM usage will be pegged, because of high number of processes running on it
- *Side effect 3:* 
	- The number of `php-fpm` active processes will hit its limit
	- In other words high server load
	- resource starvation
- *Side effect 4:* 
	- Requests can't get through because of the bottleneck, 502s are returned
	- Site outage

What is a thundering herd 
- Basically think of a stampeding herd but internet traffic instead of animals
- Incapability of a site to handle a sudden burst of traffic

Things you can do to mitigate a thundering herd
- Scale up your webservers to max capacity 
	- You cannot scale more webservers than what your database can handle
- While you figure where the issue is coming from restart `php-fpm` process to relieve pressure on your servers

Investigating traffic spikes
- Ask yourself, where is the traffic coming from?
- ~~Was there a press release that caused a sudden burst of traffic?~~
- Where can I look to see who's making the requests and how?

Sumologic
- Sumologic is a cloud-based platform that collects alll your webserver logs so you can run queries on it and analyze data
- We can run a query on the log that collects http requests 
	- Let's say we have multiple domains we have to find the domain that's being hit the most
	- We should count how many requests each ip is making 
	- We should also know that that we can get the domain from the header 
		- See host: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Host](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Host)

lets say we can find `http` requests and query headers like `http_host` and `ip` from `drupal-requests.log` our query will look like this...

Check which domain is hit the most 
```
_source=drupal-requests AND _sourceHost=web-server-name-* | count by http_host
```
What this tells us is there's an abnormal traffic hitting `https://client1.mycoolwebsite.com`
```
+---+-----------------------------------+--------+
| # |             http_host             | _count |
+---+-----------------------------------+--------+
| 1 | https://client1.mycoolwebsite.com | 87,825 |
| 2 | https://client2.mycoolwebsite.com |  5,452 |
| 3 | https://client3.mycoolwebsite.com |  5,162 |
| 4 | https://client4.mycoolwebsite.com |  4,676 |
+---+-----------------------------------+--------+
```
Let's count the number of requests coming from every ip and sort them in descending order
```
_source=drupal-requests AND _sourceHost=web-server-name-* https://client1.mycoolwebsite.com | count by ip
```
What this tells us is we have abnormal traffic coming from two ips.
```
+---+----------------+--------+
| # |       ip       | _count |
+---+----------------+--------+
| 1 | 94.178.242.186 | 43,123 |
| 2 |  94.178.242.63 | 50,141 |
| 3 |    45.29.21.42 |    891 |
| 4 |   35.234.45.23 |    671 |
| 5 |    56.23.44.85 |    872 |
| 6 |    33.54.76.23 |    749 |
+---+----------------+--------+
```
- Is the traffic valid or invalid? 
	- Technically this is valid traffic, but invalid . An invalid traffic would be abnormally high requests coming from multiple sources. ( `DDoS` )

We can block them from the load balancer with the following subnet mask
```
94.178.242.0/24
```
 - [Subnet cheatsheet](http://bp2.blogger.com/_NVxlqh8F-Yo/RnFIPdCOclI/AAAAAAAAABU/J35Bs6Y9uG8/s1600-h/ips.bmp)
- [Subnet calculator](https://www.calculator.net/ip-subnet-calculator.html)

To check information about the ip address 
- You can also file an abuse to its provider

```
whois -h whois.arin.net 94.178.242.63
```
Prevention
> Lets say we are hosted in AWS
- Was there rate limiting?
	- Let's say there was rate limiting in the `userid` level, we did not account for public pages
	- This traffic was hitting a public endpoint with massive GET requests
- AWS
	- We can add `Web Application Firewall (WAF)` to ALB and/or Cloudfront

