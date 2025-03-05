---
title: "Building the Foundation"
seoDescription: "A practical look at organizing web server folders, from `/srv` structures to centralizing logs and configs—designed for scalability and security."
datePublished: Thu Dec 19 2024 02:08:05 GMT+0000 (Coordinated Universal Time)
cuid: cm4uomi9x000808jv23t0ed6g
slug: building-the-foundation
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734573048379/0b5369da-7e26-4067-9b20-3994734d1418.webp
tags: docker, nginx, nodejs, vps, best-practices, rust, github-actions-1

---

In the previous [article](https://geekist.co/smarter-defaults-for-subdomains-and-ssl), I talked about how to manage your sub-domain setup with Nginx in a streamlined way. In this one, we'll dive into common approaches to structuring folders on your server and share the system I’ve settled on.

Traditionally, most web roots are located within `/var/www/html`, a specific folder designated to house all files related to your web app. This directory is owned by `root:root`, requiring `sudo` privileges to make changes.

This design choice seems intentional. By requiring `sudo` for every modification, you’re compelled to pause and think carefully about changes to this critical directory.

Security plays a significant role here too:

*You don’t want to accidentally delete or corrupt your WordPress* `wp-config.php` *if you have a live site serving thousands of users!*

For my setup, I prefer using `/srv` as the root for my web projects. The `/srv` directory is explicitly designed for service-related data, making it ideal for hosting multiple applications or services. It promotes clarity and adheres to the Filesystem Hierarchy Standard (FHS).

By keeping project files here, you ensure better organization and scalability. Be sure to secure sensitive files like `.bashrc` and `.ssh` to prevent accidental exposure to the web.

The approach I’ll outline in this guide works equally well whether you choose `/srv`, `/home`, `/var/www`, or any other web root you’re comfortable with.

Before we dive in, it’s worth mentioning that although this article is part of the [Taming Nginx](https://geekist.co/series/taming-nginx) series, the folder structure principles discussed here apply universally. Whether you’re using Nginx, Apache, Lighttpd, or even Node.js, the organizational practices outlined will serve you well.

---

### The problem with `/var/www/html`

It's the name! `html/` implies that all files here are only ever related to what you're serving over https, but where do the project files go?

Because of this, I've encountered numerous variations. Some folks clone an entire project repo directly into this location. Others might have the `.git` repo within their home directories and only the compiled files get dumped into `html/`

And very often you find yourself thinking, should I commit, pull, compile, then `sudo mv` the entire folder back into `/var/www/html` - or should I just make that *tiny little edit* to that one file.

*I'm fairly certain you've never done the latter! 😉*

In fairness though, this is why we have things like Github Actions and similar, just so you never have to think about `sudo`\-ing anything and wreck your brain doing it.

Also, as you'll find in 99.9% of the tutorials out there, one still has to `sudo chgrp -R www-data /var/www/html` or sometimes even `chown` the whole thing just so your web server has access to it.

If project files are also in this directory, that's already a bad practice because it means that if an attacker managed to compromise security and get into the root of your site, they'd have access to all files located in it.

But truthfully, attacks are generally far more advanced these days - although there are bots written specifically to exploit this security flaw that will visit your site every day!

### What I do.

The way I see it? If you're going to clone a repo anyway, just use `/srv`. Here's what mine typically looks like:

```plaintext
/srv/: tree -L 1
.
├── auth.example.com
├── blog.example.com
├── downloads.example.com
```

In each directory, your web root can be named according to any convention you like. `webroot`, `public` or simply just `app`. This directory is what you `chgrp` to `www-data`.

It's worth mentioning that the web root is not always needed. Especially if you're using `docker`  to manage the backend service.

Even if you are using docker, containing them into subdomain directories has its benefits:

1. `/logs`: A central location for all the logs related to this app
    
    * Some apps have configurable log paths, and here's where you'd want to see them.
        
    * I’d also add Nginx’s `access` and `error` logs here too, but more on that in a bit.
        
2. `/config`: A central location for all configuration files for this app
    
    * This is where your `toml`, `yaml`, `json` etc config files go
        
    * For me, I'd add the `nginx.conf` here too
        

As mentioned, if using docker, you'd typically have a `docker-compose.yml` here. If not, you'd likely want to have some sort of `start` script - unless you are managing the service as a startup script - which is also a great practice.

For added security, feel free to change the permissions and ownership of the files in the config directory so that only you have access to it but make sure that `nginx.conf` is still owned by the `www-data` group. Here's a minimal example of what that looks like:

```nginx
# File: /srv/blog.example.com/config/nginx.conf

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name blog.example.com;

    # if using docker-compose, this will be mounted to the public 
    # root of container
    root /srv/blog.example.com/app;
    index index.html;

    # Logging
    access_log /srv/blog.example.com/logs/access.log;
    error_log /srv/blog.example.com/logs/error.log;

    # SSL Configuration
    include /etc/nginx/ssl_common;

    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires max;
        add_header Cache-Control "public";
    }

    # Serve files and directories, or return 404
    location / {

        # If using docker to serve your app, use your proxy
        # configuration instead
        try_files $uri $uri/ =404;
    }
    
    

}
```

And then all you have to do is:

```bash
# Create a symlink to sites-enabled
sudo ln -s /srv/blog.example.com/config/nginx.conf /etc/nginx/sites-enabled

# test that your configuration works
sudo nginx -t

# reload the service
sudo service nginx reload

# check the status of the service
sudo service nginx status

# finally, curl your website to see that everything works
curl -L blog.example.com
curl -i https://blog.example.com
```

### Let's break down the benefits of this approach

1. Your logs are centralized per app. If you're running multiple services on a single machine, this becomes very helpful in locating the root of the problem.
    
2. Docker Compose scenarios will probably still be related to a single public service offering. This structure can also transition well to more advanced setups, like multi-server environments, where directories can be synced or mirrored for scalability:
    
    * Rename the parent directory to something specific that makes sense for both services
        
    * Split the docker compose file per service
        
3. Nginx configuration located here and symlinked to `sites-available` makes it easy to understand what's going on and you still have the added benefit of enabling or disabling the service.
    

There's more you can do of course. You can symlink `sites-enabled` to the root directory too, just so you can quickly see what's in there:

`sudo ln -s /etc/nginx/sites-enabled /srv`

If you feel creating directories like this becomes a chore for every domain, I’ve written a utility called `Skeletor` for this purpose:

* [Skeletor JS](https://github.com/seriouslyjs/skeletor) : A cli tool written in Node.js.
    
* [Skeletor](https://github.com/jasonnathan/skeletor): The same tool written in Rust.
    

As I mentioned before, I’m in the second year of using [RackNerd as my super low-priced VPS](https://my.racknerd.com/aff.php?aff=13317), so they are worth checking out!

---

#### Where we go from here:

If you read [the previous article](https://geekist.co/smarter-defaults-for-subdomains-and-ssl) in the series [Taming Nginx](https://geekist.co/series/taming-nginx), you'd see how this one is a natural progression to the conventions described there. In the next article, we'll explore SSL cert creation and renewal automation with `certbot` and `cloudflare`!

I'd also be talking about `Skeletor` in detail in an upcoming post and hopefully, the Readme gives you enough context in the meantime.

Hope this article gave you insight and creative ideas for folder organisation. Until next time, cheers!