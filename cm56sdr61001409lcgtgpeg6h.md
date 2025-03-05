---
title: "Wordpress MVC Plugin Reboot 2024"
seoDescription: "Revisiting a custom WordPress MVC router from 2010, with insights on how it still holds up in 2024 and where modern middleware fits in."
datePublished: Wed Dec 25 2024 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm56sdr61001409lcgtgpeg6h
slug: wordpress-mvc-plugin-reboot-2024
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735305601760/7528dac8-691d-4ceb-b9a4-d09bd0a8d436.png
tags: wordpress-plugins, wordpress, php, wordpress-plugin-development, timber, mvc-framework, wordpress-plugin-boilerplate, pods-framework, retro-tech

---

### Revisiting the WordPress MVC Router: A 2024 Update

So I stumbled upon an old database dump of Wordpress backups over the years and chanced a 2010 article I wrote that I thought was interesting. So I extracted, and decided to test everything out before I posted it on Geekist.

I’ve got to say, it really did surprise me. Logic from some 15 years ago still worked! So I decided I’d push the [code to a repo](https://github.com/theGeekist/2010-router-plugin-example), add some screenshots and give [the article](https://geekist.co/wordpress-mvc-plugin-development) some well deserved love.

Fast forward to today, so much has changed with plugin development in WordPress, It has matured with frameworks and best practices, yet I’d still say that the core idea of a lightweight, custom router remains quietly powerful.

But let's revisit this in the context of what’s available now.

### The WordPress Plugin Framework Landscape

Here are a few notable frameworks and tools for WP plugin development

1. [WP Plugin Boilerplate](https://wppb.me):  
    A standardized, object-oriented foundation for WordPress plugin development. It’s built on best practices and helps developers maintain clean, modular code.
    
2. [WP Scaffold](https://developer.wordpress.org/cli/commands/scaffold):  
    A command-line tool that generates boilerplate code for plugins and themes. It doesn’t enforce MVC but provides a solid starting point for structured development.
    
3. [Timber](https://timber.github.io/docs/v2/):  
    It seems primarily focused on themes (templates), but Timber uses the MVC pattern and integrates with plugins to help structure admin interfaces.
    
4. [Pods Framework](https://pods.io/):  
    From what I gather, it does magic with custom content types and has powerful tools for building admin interfaces, with hooks and filters that align well with MVC-inspired routing.
    

### Why Revisit the Simple Router?

Because with more features, comes complexity and a steeper learning curve. Sometimes, all you need is a lightweight, no-frills solution.

*Also… there’s absolutely nothing close to creating the building blocks of your own reusable framework - especially for smaller or personal plugins.*

The beauty of the router I introduced in 2010 (and the updated one below) lies in its simplicity. It maps URLs to controllers & methods and that gives you a great starting point to:

* Quickly structure plugins.
    
* Avoid repetitive code.
    
* Scale functionality without introducing heavy dependencies.
    

And no, it’s not gonna replace WP Plugin Boilerplate for sure, but hey, there is really nothing stopping you from using both.

### Updated Simple Router for 2024

Let’s dive into the updated router for 2024, which now includes a middleware implementation, auth checks, input validation and exception handling.

#### The New Router Class

```php
class Router
{
  protected $middleware = [];
  protected $middleware_instances = [];
 // ...other methods below
}
```

So if we have a psuedo-MVC framework, it really does make sense to include some sort of middleware implementation. Here `$middleware` is a property holding defined middleware for the class and `$middleware_instances` hold instantiated instances. More on this later.

#### Constructor and Initial Setup

```php
public function __construct(
  $route,
  $default_controller,
  $default_method,
  $middleware = []
) {
  $this->middleware = $middleware;
  $this->perform_auth_checks();

  $try = $this->route_it($route, $default_method);

  if ($try === false) {
    $this->default_route_it(
      $default_controller,
      $default_method,
      $route[2]
    );
  }
}
```

`$route`, in this case is an array consisting of the extracted `$_GET` variables, namely `‘page‘` and `‘action‘`. This is provided by `plugin.php` when this class is instantiated. We try the provided route, if not we fall back to the default route. We also have an optional array `$middleware` saved.

#### Route Handling

The `route_it` method determines which controller and method to execute based on the request.

```php
public function route_it(
  $controller,
  $method,
  $parameters = [],
  $default_method
) {
  try {
    $obj = class_exists($controller)
      ? new $controller()
      : false;

    if (!$obj) {
      return false;
    }

    $method_exists = method_exists($obj, $method);
    $default_method_exists = !$method_exists
      && method_exists($obj, $default_method);

    if (!$method_exists && !$default_method_exists) {
      return false;
    }

    $this->execute_middleware(
      $controller,
      $method_exists ? $method : $default_method,
      $parameters
    );

    return $method_exists
      ? $obj->$method($parameters)
      : $obj->$default_method($parameters);
  } catch (Exception $e) {
    $this->handle_exception($e);
  }

  return false;
}
```

We first check if a controller exists:

```php
class_exists($controller);
```

then we check to see if the called action exists

```php
method_exists($obj, $method);
```

or that at least that the default action (usually named `index`) exists

```php
method_exists($obj, $default_method);
```

If none of the above checks out, we return false, otherwise we execute the defined middleware before passing control to the controller.

#### Middleware Execution

Middleware execution is optimized to reuse existing instances.

```php
protected function execute_middleware(
  $controller,
  $method,
  $parameters
) {
  foreach ($this->middleware as $key => $mw) {
    if (!isset($this->middleware_instances[$key])) {
      if (class_exists($mw)) {
        $this->middleware_instances[$key] = new $mw();
      } else {
        throw new Exception("Middleware class $mw not found.");
      }
    }

    $middleware_instance = $this->middleware_instances[$key];

    if (method_exists($middleware_instance, 'handle')) {
      $middleware_instance->handle(
        $controller,
        $method,
        $parameters
      );
    } else {
      throw new Exception(
        "Middleware class $mw does not have a handle method."
      );
    }
  }
}
```

This is a simple way to ensure that we don't instantiate all the middleware for each request and is included here to illustrate how this can be done. However, another great way to include this is within your plugin's startup code itself. That way, your plugin's first page load wouldn't take too long if you have tons of middleware!

#### Fallback Route Handling

The `default_route_it` method handles fallback routes when the specified route fails.

```php
public function default_route_it(
  $controller,
  $method = "index",
  $parameters = []
) {
  try {
    $obj = class_exists($controller)
      ? new $controller()
      : false;

    if (!$obj) {
      throw new Exception("Controller not found: $controller");
    }

    $method_exists = method_exists($obj, $method);

   if(!$method_exists) {
    throw new Exception("There is no default '$method' method found in $controller");
  }

    $this->execute_middleware(
      $controller,
      $method,
      $parameters
    );

  $obj->$method($parameters);

  } catch (Exception $e) {
    $this->handle_exception($e);
  }

  return false;
}
```

This method is pretty much the same as the 2010 version. The only difference is the added middleware execution. This method helps when your controllers have a default action (`index` in this case) and will be used as to display first page of the menu item that a controller handles

#### Authentication Checks

Basic authentication logic is enforced during construction:

```php
protected function perform_auth_checks() {
  if (!isset($_SESSION['user'])) {
    throw new Exception("Unauthenticated access detected.");
  }
}
```

This saves you the hassle of checking for an authenticated user within each controller and it serves as an example of how you can include mandatory checks for your plugin.

#### Exception Handling

The `handle_exception` method provides a standard mechanism for logging and displaying errors.

```php
public function handle_exception(
  $e
) {
  error_log($e->getMessage());
  wp_die(
    "An error occurred: " . $e->getMessage()
  );
}
```

The example above is rudimentary but you can expand on it to write to a log file, store a database entry (or even `Sentry`) when your plugin encounters exception.

#### How Do We Use This Exactly?

I'll have to admit, the original post - back in 2010 - was a little abstract so I hope the screenshot above helps to clear things up.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735189828246/5aca9558-89f8-456f-8dfc-e18b3ecd2e91.png?auto=compress,format&format=webp align="center")

Each menu item on the left is a controller class you define. If you look at the URL, you'd see that `$_GET` variables list `page=CRM` with no action defined. So the page you see in the screenshot points to the `Contacts` controller class and the default `index` action. I am hoping to include a link to a GH repo with example code of this functionality some time soon, so do subscribe to the newsletter for updates. In the meantime, working code for the original router is still [available here](https://github.com/theGeekist/2010-router-plugin-example)

### Final Thoughts

I hope this article inspires you with at least one way of organizing your plugin code. If you have questions or thoughts, do share them in the comments!