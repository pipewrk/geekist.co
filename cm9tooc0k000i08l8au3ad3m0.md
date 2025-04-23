---
title: "The Invisible CDN We Built for Display Pics"
seoDescription: "How we used PHP and Nginx to build a dynamic image delivery system with zero exposed URLs, full cache control, and no strain on the app server."
datePublished: Wed Apr 23 2025 08:40:16 GMT+0000 (Coordinated Universal Time)
cuid: cm9tooc0k000i08l8au3ad3m0
slug: the-invisible-cdn-we-built-for-display-pics
canonical: https://geekist.co/the-invisible-cdn-we-built-in-2012-for-dynamic-avatars/
tags: cdn, performance, nginx, php, architecture, webdev, cache, retro-tech

---

I found it by accident.

Buried in an old project folder, long-forgotten PHP files still humming with logic that *somehow* still works today. And not just technically functional, but conceptually relevant. Maybe even ahead of its time.

But to appreciate what it did exactly, you’d have to understand what it was trying to solve.

### 💫 The Problem

The application functioned as a social network, where profile pictures weren’t just aesthetic, they were identity. Display pictures (DPs) had to update fast, stay visible everywhere and somehow keep up with the user. That meant you couldn’t just serve a static image. You needed something smarter. Something dynamic. A system that could ensure the images remained current, even on posts written years earlier.

### ❖ The Existing Solutions

By 2012, the problem of dynamic DPs *seemed* solved. Facebook had cracked it. DPs changed and appeared instantly, even on years-old posts. But look under the hood and there was little public documentation on how it worked… Anywhere. The rest of us just had to piece it together ourselves.

Most developer forums back then were still discussing crude options:

1. **Serve via app** – `image.php?user_id` (slow, uncacheable)
    
2. **Save on disk** – `uuid.jpg` in `/uploads/`
    
3. **Store in the DB** – blobs in MySQL (don’t even)
    
4. **Set up a CDN** – but good luck managing keys, origins, headers (and bills)
    

Each option had a fatal flaw. (1) and (2) made images fragile; if your app server went down, so did your photos and (3) was a storage disaster waiting to happen.

(4) was the only viable answer if you were aiming for scale and speed. But it had problems of its own. Not just cost or abuse, but something more fundamental, *posterity*.

You could save the DP with a new UUID every time it changed, but then you were back to serving outdated images in every previous post. If you overwrote the original instead, that meant dealing with CDN purges or versioning edge rules.

In other words, (4) solved delivery but broke consistency. It was never a *set-and-forget* solution. You had to choose between real-time updates and the convenience of caching.

That old code didn’t just sidestep the trade-offs. It answered the question that none of the others really solved:

> *How do you serve dynamic content with static delivery confidence?*

That’s why it deserved to be found and *why it’s worth writing about.*

### ✨ The Code That Solved It

```php
<?php defined('SYSPATH') or die('No direct script access.');
/**
 * A simple controller that returns the location of an avatar for a given user.
 * It sets nginx headers so it can redirect the request to serve the static file
 * instead of having PHP handle the file.
 *
 * @author Jason Nathan
 * @category RealtAsia
 * @package RT_Controller
 * @subpackage Avatar
 * @version 1.0
 * @copyright 2012 Realtasia Pte Ltd
 */
class Controller_Avatar extends Controller {
    /**
     * A simple method that retrieves a user by id, checks for an existing avatar
     * or redirects to the default after checking user gender.
     *
     * @return Response The response headers
     */
    public function action_index()
    {
        $url = '/s3/';
        $b = $url . RT::BUCKET . '/avatars/';
        $this->response->send_headers();
        $id   = trim($this->request->param('id'));
        $size = (int) $this->request->param('size');
        $user = Document::factory('user')->collection();
        $u = $user->findOne(
            ['_id' => new MongoID($id)],
            ['info.gender' => 1, 'avatar' => 1]
        );
        if (\Arr::get($u, 'avatar')) {
            return $this->response->headers([
                'X-Accel-Redirect' => $url .
                    str_replace($id, "$id/$size", $u['avatar'])
            ]);
        }
        return $this->response->headers([
            'X-Accel-Redirect' => $b .
                ucfirst(substr($u['info']['gender'], 0, 1)) . $size . '.png'
        ]);
    }
}
```

**The real magic is in lines 38–48.**

What these lines do is subtle but powerful: they remove the burden of serving avatar images from your app entirely, while still keeping the image path dynamic. Instead of outputting the image directly, they instruct Nginx to take over, using `X-Accel-Redirect` to silently serve the right file from a protected internal path.

It means your app can make smart decisions (like checking for a custom avatar or falling back to defaults), but still deliver the image at static-speed. No extra DB hits for repeat views. No CDN edge purges. No exposed URLs. Just one redirect header and the illusion of magic.

And once that controller knew where to point, all it needed was a web server smart enough to step aside. Here’s the config we had in place for Nginx:

```nginx
server {
  root /home/manager/www/api;
  index index.html index.htm index.php;
  server_name api.realtasia.com;
  location = /favicon.ico {
    return 204;
    access_log     off;
    log_not_found  off;
  }
  location / {
    try_files $uri $uri/ /index.php?$args;
  }
  #pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
    # With php5-cgi alone:
    #fastcgi_pass 127.0.0.1:9000;
    # With php5-fpm:
    fastcgi_pass unix:/var/run/php5-fpm.sock;
    fastcgi_index index.php;
    fastcgi_send_timeout 50m;
    include fastcgi_params;
    fastcgi_read_timeout 50m;
    include fastcgi_params;
  }
  location /uploads {
    autoindex off;
  }
  location ~* ^/s3/(.*?)/(.*) {
    internal;
    resolver 8.8.8.8;
    access_log /var/log/nginx/s3.access.log;
    error_log /var/log/nginx/s3.error.log;
    set $download_uri $2;
    set $download_host $1;
    set $download_file http://$download_host/$download_uri;
    proxy_set_header Host $download_host;
    proxy_set_header Authorization '';
    proxy_max_temp_file_size 0;
    proxy_pass $download_file;
  }
}
```

Again, lines 37-49 did it all.

This single Nginx block turned our PHP controller into a silent traffic conductor. Once the avatar logic ran, the controller simply passed along the correct file path using `X-Accel-Redirect`. Nginx intercepted the response and – without ever hitting disk, database or further PHP execution – proxied the request to its final destination.

Because PHP never handled the image directly, the request path was highly cacheable. The controller itself benefitted from opcode caching (yes, even back then), so the logic overhead was negligible. Downstream, the avatar image retained its original headers, *including ETags*, ensuring that browser and CDN-level caching kicked in without disruption. This got us fewer hits, faster loads and almost zero strain on the app server.

What that meant was:

This wasn’t just a workaround. It was infrastructure thinking ahead of its time. We didn’t have edge functions or programmable CDNs. We had control over our stack, a clear understanding of the problem, and maybe a bit of ingenuity. Over a decade later, that invisible CDN still holds its ground.

Turns out, when you design for posterity instead of trend, even your old PHP can age like wine.

Questions? Shoot me up in the comments.