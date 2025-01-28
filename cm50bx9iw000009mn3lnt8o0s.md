---
title: "The Art of Proxying"
seoDescription: "Unlock Nginx's full potential! Explore advanced reverse proxy use cases like WebSockets, gRPC, Layer 7 load balancing, and more."
datePublished: Mon Dec 23 2024 00:59:09 GMT+0000 (Coordinated Universal Time)
cuid: cm50bx9iw000009mn3lnt8o0s
slug: the-art-of-proxying
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734915366586/83e27721-98ea-4e67-a819-9a8971b03682.png
tags: websockets, sysadmin, web-development, nginx, devops, networking, reverse-proxy, api-gateway, grpc, rate-limiting, health-checks, layer-7

---

Welcome back to the [**Taming Nginx**](https://geekist.co/series/taming-nginx) series! In this article, we’re diving into the art of reverse proxying. While many guides focus on the basics, this article is for those who want to push Nginx to its limits, we'll explore advanced and peculiar use cases that can take your configuration to the next level.

If you missed the earlier articles, here’s a quick recap:

* [**Smarter Defaults for Subdomains and SSL**](https://geekist.co/smarter-defaults-for-subdomains-and-ssl) — The first article in the series where we talk about managing subdomains globally
    
* [**Building the Foundation**](https://geekist.co/building-the-foundation) — A guide to structuring folders for managing multiple domains and services.
    
* [**Wildcard SSL and Beyond**](https://geekist.co/wildcard-ssl-and-beyond) — We explored SSL, wildcard certificates and mutual SSL setups.
    

This time, it’s all about unlocking the potential of Nginx as a reverse proxy. We’ll examine how each use case addresses real-world challenges and elevates your configurations.

---

### Reverse Proxy Refresher

Here's a quick primer on what a reverse proxy does:

> At its core, a reverse proxy sits between clients and backend servers, forwarding requests and responses while optionally performing additional tasks like SSL termination, caching or load balancing.

If you’ve followed the earlier articles in this series, you’ll know that we redirect all HTTP traffic to HTTPS by default and wildcard SSL certificates are used to secure subdomains. The examples in this article build upon that foundation, assuming the same `ssl_params` file for shared SSL settings and no incoming requests to port 80.

Also note that I've deliberately excluded mutual SSL here (since it was covered before), along with caching & security - as I will talk about that in detail in the next article in the series.

Let’s dive in!

---

### 1\. Layer 7 Load Balancing with Sticky Sessions

Layer 7 of the OSI model, also known as the [Application Layer](https://en.wikipedia.org/wiki/Application_layer), was formalized in the late 1970s to standardize communication across networked systems. Unlike lower layers that handle data transport and routing, Layer 7 focuses on the content and context of communication - think HTTP requests, email protocols and, yes, even Nginx configurations.

Load balancing brings content-aware traffic handling into the mix. Beyond distributing connections evenly, it enables decisions based on application-specific information, like URLs, cookies or headers. Sticky sessions, for instance, solve the specific challenge of keeping user sessions tied to a single server. Think shopping carts or user logins, where consistency is key.

#### Configuration:

```nginx
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}

server {
    listen 443 ssl;
    server_name example.com;

    include /etc/nginx/ssl_params;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

#### What's going on here?

Hopefully, you weren’t expecting anything special here. The `ip_hash` directive uses the client’s IP address to generate a consistent hash value, which determines the target server within the upstream block. This method works as long as your client’s IP remains stable and makes short work of most session-dependent tasks like e-commerce or user dashboards.

---

### 2\. Protocol Translation: WebSockets, gRPC and HTTP/2

Before the advent of WebSockets, we had to rely on hacks like long polling or frequent HTTP requests which - as you know probably know - are both inefficient and resource-intensive.

Introduced in 2011 as part of the HTML5 specification, WebSockets offered a persistent, full-duplex communication channel over a single TCP connection, enabling true real-time interactivity by keep connections alive - allowing *servers* to push updates to *clients* as events occur.

It transformed applications like chat, live notifications and collaborative tools, laying the groundwork for modern, highly interactive web experiences.

With Nginx, integrating WebSockets into your stack becomes straightforward, bridging the gap between users and backend services.

#### WebSockets:

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    include /etc/nginx/ssl_params;

    location /ws/ {
        proxy_pass http://websocket_server;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

#### What's going on here?

So the `proxy_pass` directive specifies the backend server or application that will handle WebSocket traffic. It allows Nginx to forward WebSocket requests from clients to the specified server.

The `proxy_http_version 1.1` directive ensures that HTTP/1.1 is used for the connection, which is required for WebSocket compatibility.

The `proxy_set_header Upgrade $http_upgrade` and `proxy_set_header Connection "upgrade"` directives manage the protocol upgrade from HTTP to WebSocket. These headers signal the backend server to switch protocols and maintain the WebSocket connection.

Now let's talk about gRPC (gRPC Remote Procedure Call) - a modern, high-performance framework developed by Google in 2015. Built on top of HTTP/2, it enables efficient, bi-directional streaming and multiplexing over a single connection, making it ideal for real-time and low-latency applications.

#### gRPC with HTTP/2:

```nginx
server {
    listen 443 ssl;
    server_name grpc.example.com;

    include /etc/nginx/ssl_params;
    http2 on; # Enable HTTP/2 for this server block

    location / {
        grpc_pass grpc://127.0.0.1:50051;
    }
}
```

#### What's going on here?

Not much! The `grpc_pass` directive is the heart of this configuration, forwarding gRPC traffic to the backend application listening on port 50051. Unlike HTTP/1.x, gRPC relies on HTTP/2, which enables multiplexed streams over a single connection.

---

### 3\. API Gateway with Rate Limiting

APIs are the backbone of modern apps, powering everything from social networks to payment systems. But with great power comes great responsibility - or in this case, *great vulnerability*!. Left unchecked, APIs can fall victim to abuse, such as excessive requests or bot attacks, which can degrade performance for legitimate users.

Rate limiting emerged as a solution to manage API traffic by setting limits on how often a client can make requests, it prevents overuse while ensuring fair access to backend services. In the early days, we had to rely on ad-hoc custom scripts or middleware to throttle requests. They worked of course but are usually resource-intensive and difficult to scale.

Nginx, with its built-in rate limiting capabilities, simplifies this process. By integrating rate limiting directly into your reverse proxy configuration, you gain a powerful and efficient way to protect your APIs without reinventing the wheel.

#### Configuration:

```nginx
http {
    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=5r/s;

    server {
        listen 443 ssl;
        server_name api.example.com;

        include /etc/nginx/ssl_params;

        location / {
            limit_req zone=api_limit burst=10 nodelay;
            proxy_pass http://backend_api;
        }
    }
}
```

#### What's going on here?

The `limit_req_zone` directive defines a shared memory zone for tracking requests per IP. This shared memory allows Nginx to monitor how many requests each client sends over a specified time period.

The `limit_req` directive works as a gatekeeper, allowing only a specific number of requests to pass through per second for each IP address. If the rate is exceeded, excess requests are either delayed (if configured) or dropped, protecting the backend from being overwhelmed. This setup provides a way to ensure fair resource usage while safeguarding backend performance.

---

### 4\. Dynamic Upstream Selection

Dynamic upstream selection is a game-changer for anyone managing staging environments, multi-tenant applications or complex deployment workflows. Traditionally, handling such variability required maintaining multiple (and often redundant) server blocks - *you really don't want to go there!*)

But with Nginx's `set` directive, you can conditionally route traffic based on factors like query parameters, headers or cookies.

#### Configuration:

```nginx
server {
    listen 443 ssl;

    include /etc/nginx/ssl_params;

    location / {
        set $upstream http://backend1;
        if ($arg_env = "staging") {
            set $upstream http://staging_backend;
        }

        proxy_pass $upstream;
    }
}
```

#### What's going on here?

The `set` directive is a powerful tool for dynamically assigning variable values based on specific conditions. In this configuration, `set` is used to determine the upstream server by inspecting the `arg_env` query parameter. If `arg_env` is set to "staging," the request is routed to the staging backend; otherwise, it defaults to the production backend.

This ability to assign variables conditionally streamlines complex routing scenarios, allowing configurations to remain concise, modular and readable. `set` can also be used for other creative use cases, like adjusting cache keys, generating custom headers or controlling feature flags.

---

### 5\. Reverse Proxy with Backend Health Checks

How many times have you had a backend service go down and your frontend go berserk or present you with the infamous `502` gateway errors?

Health checks provide an elegant solution to this problem - and Nginx comes with this built-in. If a server goes down, Nginx temporarily removes it from the upstream pool until it recovers.

#### Configuration:

```nginx
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com backup;

    health_check interval=5s rise=2 fall=3 timeout=2s;
}

server {
    listen 443 ssl;

    include /etc/nginx/ssl_params;

    location / {
        proxy_pass http://backend;
    }
}
```

#### What's going on here?

The `health_check` directive steps in as your reverse proxy's safety net. It continuously pings the upstream servers at intervals you define (here, every 5 seconds) to verify their responsiveness. If a server fails to respond correctly, it’s temporarily removed from the pool.

But it doesn't stop there. You can configure parameters like `rise` and `fall` to determine how many consecutive successes or failures are required for a server to be reinstated or deactivated respectively. This ensures that temporary blips don’t immediately mark a server as unhealthy but where persistent issues are handled gracefully.

Your frontend remains resilient and cryptic error messages for users become a thing of the past.

---

### What’s Next?

In this article, we’ve seen how Nginx’s reverse proxy capabilities extend far beyond the basics, enabling sophisticated traffic management and backend integrations. Whether it’s load balancing, dynamic routing or protocol translation, there’s always room to level up your configurations.

Next, we’ll explore **streaming and low-level protocol handling with Nginx’s** `streams {}` block. Stay tuned for the next chapter in the **Taming Nginx** series!

If you’ve got questions, examples or clever configurations of your own, let’s chat in the comments below.

---

**Did you enjoy this post?** Don’t miss future updates! Subscribe to the newsletter and follow the series [here](https://geekist.co/series/taming-nginx).