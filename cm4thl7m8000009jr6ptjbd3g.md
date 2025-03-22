---
title: "Smarter Defaults for Subdomains and SSL"
seoDescription: "Tame Nginx with smarter defaults! Learn how to simplify subdomains, SSL, and streamline your configs for secure, scalable setups"
datePublished: Wed Dec 18 2024 06:03:21 GMT+0000 (Coordinated Universal Time)
cuid: cm4thl7m8000009jr6ptjbd3g
slug: smarter-defaults-for-subdomains-and-ssl
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734501610435/46b5a441-5b8a-4169-8e0b-7d7e54e692c4.webp
tags: ssl, nginx, vps, configuration, subdomains

---

Over the decades, I’ve developed a convention for working with Nginx that simplifies subdomains and SSL. While there’s no shortage of broad tutorials on Nginx, I’ve often wondered: *why are so many processes still so manual?*

More importantly, why aren’t Nginx’s defaults being exploited to their full potential?

This is the first in the [Taming Nginx](https://geekist.co/series/taming-nginx) series, where I’ll explore **smarter, more intentional configurations** that streamline workflows and enhance performance.

In this article, we’ll focus on **globally managed subdomains and SSL**.

Let’s skip the repetitive setups and turn Nginx into the efficient powerhouse it’s meant to be, shall we?

---

In a typical nginx installation folder `/etc/nginx`, you'd find an `nginx.conf` file which specifies the `http` block.

This is where all the magic of nginx happens.

*You'd also see an* `events` *block, but we'll talk about that in future posts*

The `http` block is where global defaults are declared. Things like compression and which Nginx modules to load are defined here. As you’ll see, this file can get very verbose, very quickly.

Fortunately, Nginx includes an `include` directive, which allows you to modularize configurations by including other files or folders.

In almost every default Nginx configuration, you’ll see something like this near the end of the `http` block:

```nginx
http {
    # ...other directives

    ##
    # Virtual Host Configs
    ##

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

}
```

Most tutorials gloss over this, but if you’re a discerning sort, you might notice some very specific details:

1. `include /etc/nginx/conf.d/*.conf;`: Notice how it’s loading any and all files in the `conf.d` directory that end with a `.conf` extension?
    
2. `include /etc/nginx/sites-enabled/*;`: Unlike `conf.d`, this one loads all files in `sites-enabled`, regardless of their extension.
    

**But why? And why in that order?**

Besides offering obvious flexibility in naming your domains, subdomains, or other server blocks, this order ensures that files in `conf.d` are loaded **first**.

This is intentional, and it’s a good thing.

Typically, `conf.d` is used to specify global or module-specific defaults, but it also opens up some creative possibilities - which I’ll illustrate below.

**The default HTTP config**

Now that we understand how `conf.d` is loaded first, let’s explore how it ties in to the **default HTTP configuration**.

In a typical Nginx setup, you’ll find this file in `/etc/nginx/sites-enabled/default`. Look closely, and you’ll notice it’s actually a symbolic link to the original file in `/etc/nginx/sites-available/default`.

This is by design - it allows you to easily load or unload configurations without permanently deleting the file.

If you open this file in your editor, you’ll see it contains a mix of commented-out examples and configuration blocks.

While these examples can be useful later, let’s start fresh: unlink it, back it up, and create a clean slate for our **default** config.

```plaintext
# remove the symlink
sudo unlink /etc/nginx/sites-enabled/default

# backup the original file
sudo mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak

# create an empty configuration file
sudo touch /etc/nginx/sites-available/default

# link this new one back to sites-enabled
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default
```

Once you’ve done this, your **default** file is ready for a fresh configuration. And here’s where it might surprise you:

*My default configuration contains just one directive*

```plaintext
server {
    listen 80;
    listen [::]:80;
    server_name _;

    # Redirect all HTTP requests to HTTPS with a 301 Moved Permanently response.
    return 301 https://$host$request_uri;
}
```

I use this minimalist configuration across every cloud machine, VM, and VPS server I set up. Here's why:

### Default to HTTPS, not HTTP

1. By redirecting all HTTP traffic to HTTPS, you no longer need to wonder if a subdomain is insecure -everything defaults to HTTPS, which is great practice.
    
2. This eliminates the need to configure HTTP blocks for individual subdomains. Everything follows the same rule by default, saving time and reducing complexity.
    
3. If you ever need an exception (e.g., for testing purposes), you can still use an `if` block within a specific server or location block to short-circuit routing for just that one subdomain.
    

By making HTTPS the default and treating HTTP as an **explicit exception**, this approach ensures secure, intentional configurations with minimal effort.

*Wait - did I say* `if` *block?*

I did! And if that feels a bit hackish (don’t worry, purists), this is exactly why we talked about `conf.d`. You see, when HTTPS is your default, your brain is wired to treat HTTP traffic as an **exception**, just as it should be.

*Okay, that’s an easy fix, we’ll just create a new config in sites-available. Right?*

The obvious solution for an HTTP exception might look like this:

```nginx
# File: /etc/nginx/sites-available/blog.example.com
server {
    listen 80;
    listen [::]:80;
    server_name blog.example.com;

    location / {
    # specify directives here.
    }
}
```

And this would work - as long as the server name ([`blog.example.com`](http://blog.example.com)) loads **before** `default` alphabetically. But what happens if your subdomain is [`freebies.example.com`](http://freebies.example.com)?

In that case, `default` is loaded **first** (alphabetically) and redirects all traffic to HTTPS before Nginx even evaluates [`freebies.example.com`](http://freebies.example.com). This makes your HTTP exception ineffective.

### The `conf.d` Solution

This is why HTTP exceptions belong in the `conf.d` folder. By placing them here, they are loaded **before anything in** `sites-enabled`, ensuring they take precedence over the default HTTPS redirect.

For clarity, you can organize exceptions into a sub-directory within `conf.d`, like `http_exceptions`, and name the file to match the subdomain:

`/etc/nginx/conf.d/http_exceptions/`[`freebies.example.com`](http://freebies.example.com)`.conf`

**Example Configuration:**

```nginx
# File: /etc/nginx/conf.d/http_exceptions/freebies.example.com.conf
server {
    listen 80;
    listen [::]:80;
    server_name freebies.example.com;

    location / {
        # Specify directives here
    }
}
```

By placing HTTP exceptions in `conf.d/http_exceptions`, you:

1. **Ensure Proper Load Order**: HTTP exceptions are processed before the default HTTPS redirect.
    
2. **Align with the Convention**: Deviations from the HTTPS default are treated as deliberate exceptions, and their placement in `conf.d` reflects their special status.
    

#### Why This Approach Rocks

This structure makes your configurations:

* **Predictable**: HTTPS is the default. Anything in `conf.d/http_exceptions` stands out as intentional.
    
* **Modular**: Exceptions are isolated, simplifying management without affecting `sites-enabled`.
    
* **Secure by Default**: HTTP is an edge case, with everything else defaulting to secure HTTPS.
    
* **Easy to Identify**: Remember nginx loads only `.conf` files here? This file is named [`freebies.example.com`](http://freebies.example.com)`.conf` allowing you to make a distinction from the file named [`freebies.example.com`](http://freebies.example.com) in `sites-available\`
    

*Okay, then what's in* `/etc/nginx/sites-available/`[`freebies.example.com`](http://freebies.example.com)?

Everything you need for your HTTPS site - but let’s start with a **minimal setup** for clarity

```nginx
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name freebies.example.com;

    root /srv/freebies.example.com/;
    index index.html;

    # SSL Configuration
    include /etc/nginx/ssl_params;

    # Serve files and directories, or return 404
    location / {
        try_files $uri $uri/ =404;
    }
}
```

Yes, if you swapped `443` for `80` and removed `ssl`, it would look no different from a typical HTTP server block. That simplicity is deliberate and made possible with `include /etc/nginx/ssl_params;`

*But isn’t specifying a separate file like* `ssl_params` overkill for a single domain?

It certainly is - if you’re managing just one domain, but this setup is perfect for **wildcard domain certificates**, allowing you to scale efficiently across all your subdomains. Here's what it looks like:

```nginx
# ssl_params for wildcard certs
ssl_certificate /etc/ssl/certs/example_com.crt;
ssl_certificate_key /etc/ssl/private/example_com.key;

ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;

ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 1.1.1.1 valid=300s;
resolver_timeout 5s;
```

### All the steps in a nutshell

1. Create a new `default` file in `sites-available` after backing up the old one and then symlink it to `sites-enabled`
    
2. Route all HTTP traffic to HTTPS using a single directive as described above in your new `default` file
    
3. Create a global configuration file in `/etc/nginx/ssl_params` just like the one above and point to the location of your certificates
    
4. Create individual [`subdomain.mydomain.com`](http://subdomain.mydomain.com) config files in `sites-available` for every sub-domain you need, ensuring that you symlink each one back to `sites-enabled`
    
5. Run `sudo nginx -t` to test everything
    
6. Run `sudo service nginx reload (or restart)` followed by `sudo service nginx status` to ensure everything is running
    
7. Finally, test your newly configured subdomain with `curl -L` [`subdomain.mydomain.com`](http://subdomain.mydomain.com) to make sure everything works! You can also verify headers with `curl -I`.
    

In addition to `curl`, you can test your SSL configuration using online tools like [SSL Labs](https://www.ssllabs.com/ssltest/) to ensure your settings are secure and correctly configured.

And that’s it! With this setup, you now have a flexible, scalable Nginx configuration that makes adding or removing subdomains a breeze. You no longer need to worry about HTTP traffic or SSL parameters—they’re managed centrally and follow best practices by default.

---

This is just the beginning of the [Taming Nginx](https://geekist.co/series/taming-nginx) series. In upcoming articles, I’ll dive deeper into:

* [Why Your Web Server Structure is Holding You Back (And How to Fix It)](https://geekist.co/building-the-foundation)
    
* [Wildcard Certificates, Certbot & Cloudflare and Mutual SSL with Nginx](https://geekist.co/wildcard-ssl-and-beyond).
    
* [Advanced Nginx topics like **reverse proxying**, **caching**, and **rate limiting**](https://geekist.co/the-art-of-proxying).
    

Stay tuned as we continue taming Nginx in upcoming articles!