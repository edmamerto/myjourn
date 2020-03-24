---
title: NGINX Deep Dive
date: "2020-03-24T17:51:00.000Z"
layout: post
draft: false
path: "/posts/nginx-deep-dive/"
category: "Gatsby"
tags:
  - "DevOps"
  - "SRE"
description: "nginx notes"
---


  
## NGINX Deep Dive

### Why am I writing this?
- I recently had to modify directives and I realized how important it is to understand nginx configuration


`context`
- Sections within a configuration
 
```
http { }

server { }

location { }
```

> anything  with brackets are contexts 

nested context (`http` being the main context)
```
http {
	server { }
}
```
`directives`
specific configuration options, consists of a name an dvalue 
```
server_name example.com
```
*`virtual host`
The feature of being able to host **multiple websites on a single server**. 
> This is extremely useful given that you own multiple sites and don’t want to go through the lengthy (and expensive) process of setting up a new web server for each site.

*`server`
the server context is responsible for listening on a port

reload when configuration change, `restart` dirupts service
```
systemctl reload nginx
```
*MIME type
> A media _type_ (also known as a Multipurpose Internet Mail Extensions or _MIME type_) is a standard that indicates the nature and format of a document or file

This directive pretty much includes everything 
```
include       /etc/nginx/mime.types;
```
[list of mime types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)
> including this will allow your client to consume content in its proper format

*`location`
This directive intercepts ah request if it matches the uri 

*Prefix match
> this will match /greet* in other words 
> other examples: /greeting or /greet/foobar
```
location /greet {
	return 
}
```
*Exact match
> this will only match `/greet` and not `/greeting`
```
location = /greet {
	return 
}
```
*PHP processing
- For dynamic websites, nginx needs help with processing 
- for php-based applications, `php-fpm` is paired with nginx to process php 

*Nginx + php-fpm workflow
- client makes a request, 
- nginx sends it to `php-fpm` for processing 
- `php-fpm` sends back response to nginx
- nginx returns content to client
> In this sense, nginx acts as a reverse proxy 

*FastCGI
- nginx passes php requests to php-fpm by way of FastCGI
- FastCGI is a protocol like http
- Just know that this is how nginx communicates with `php-fpm`

```
location ~\.php$ {
	include fastcgi.conf
	fastcgi_pass unix:/run/php/php7.1-fpm.sock
}
```
*Reverse Proxy
> the way I like to think of this is it's a server that sits between the client and the app servers. It acts as a firewall or load balancer

*Unix Socket
> _Sockets_ allow communication between two different processes on the same or different machines

*Logging
```
error_log
access_log
```
> http requests go to access_log, nginx app errors go to error_log. Common misconceptions is errors like 404 or 502 go to error_log.

### Optimizing Performance

*Master and Worker process
- main purpose of the **master process** is to read and evaluate configuration files, as well as maintain the worker **processes**
- The worker processes do the actual processing of requests
```
$ ps aux | grep nginx
root      1541  0.0  0.0  12944   936 pts/0    S+   23:30   0:00 grep --color=auto nginx
nginx     3383  0.0  0.1 124784  4856 ?        S    02:45   0:06 nginx: worker process
nginx     3384  0.0  0.1 124784  4856 ?        S    02:45   0:06 nginx: worker process
nginx     3385  0.0  0.1 124784  4940 ?        S    02:45   0:06 nginx: worker process
nginx     3386  0.0  0.1 124784  6148 ?        S    02:45   0:06 nginx: worker process
root     28384  0.0  0.1 124784  6948 ?        Ss   Feb29   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
```
> A process can only run on one cpu at a time, increasing the workers does not necessarily improve performance. A 1 cpu core running 2 workers, 1 worker is only capable of running 50% as opposed to 1 dedicated work running optimaly at 100%

to check how many cores a webserver has 
```
nproc
```
*`worker_process` - you can set this directive to auto
```
worker_process auto
```

*`worker_connections`
> sets the number of connections a process can accept, based on open files limit
 
 *ulimit
> Your server has a limit to how many files can be open at once by a process

Check open files limit
```
ulimit -n 
```
Set this to worker_connections
```
events {
	worker_connections 1024
}
```
> this implies that `Nginx` can serve 1024 clients/second.

Other ways you can improve nginx performance
- [https://www.monitis.com/blog/6-best-practices-for-optimizing-your-nginx-performance/](https://www.monitis.com/blog/6-best-practices-for-optimizing-your-nginx-performance/)