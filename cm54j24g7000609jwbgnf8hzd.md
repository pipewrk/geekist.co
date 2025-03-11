---
title: "WordPress MVC Plugin Development"
seoDescription: "Build a simple CRM WordPress plugin with a custom router for better control over routes, actions, and parameters. Perfect for developers streamlining admin"
datePublished: Wed Sep 29 2010 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm54j24g7000609jwbgnf8hzd
slug: wordpress-mvc-plugin-development
canonical: https://jasonnathan.wordpress.com/2010/09/29/wordpress-mvc-plugin-development/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735170486467/8c86ea90-ad86-4600-a9b6-d9b38a33b8ed.png
tags: wordpress-plugins, mvc, wordpress, php, digging-into-the-archives

---

*This article was originally published in 2010 and was deliberately preserved as it was written for WordPress 3.0, released in June 2010, when custom post types and menus were introduced. However, the accompanying screenshots have been updated for WordPress 6.7.1 in 2024, and you can find the working code on* [*GitHub*](https://github.com/theGeekist/2010-router-plugin-example)*.*

---

I was working on creating a simple CRM plugin for WordPress. My first plugin yet. I needed something lightweight and very specific to my work in real estate. After some searching, I didn’t find anything suitable- unsurprisingly.

So, I set out to make this plugin. I divided the functionality into two parts. The first: basic CRM functions to view contacts, reminders, opportunities, etc. The second: advanced features like syncing, auto-reminder templates, and automated emails.

After creating my first single-handed website with Kohana 2.4, I realised the importance of an administrative backend - not just any admin panel, but something apt for managing the site AND adding new functionality.

ere's where I learnt the point of CMSes, really only because I hate tiresome learning curves, and eventually - because I was already using it: WordPress.

Don’t get me wrong; I absolutely LOVE Kohana and am already trying to master Kohana 3, but that’s a topic for another day. Before I digress further from this (intended short!) introduction, let's better get started.

I’ve grown too comfortable with the MVC approach, where URL segments map to methods in a controller. After dissecting a few plugins, I realised WordPress doesn’t really have a straightforward router to decide which method or function runs. So, I decided to create a utility class to handle routing. It wouldn’t change how the URLs look but would at least know what to run automatically.

### The Router Class

```php
class Router
{
  /**
   * Sets up the routing logic based on provided controller, method, and arguments.
   * Falls back to defaults if specific routes are unavailable.
   *
   * @param array $route [controller, method, args]
   * @param string $default_controller Default controller to use
   * @param string $default_method Default method to use
   */
  public function __construct($route, $default_controller, $default_method)
  {
    // Attempt specified route, fallback to defaults if it fails
    $try = $this->route_it($route, $default_method);

    if ($try === false) {
      $this->default_route_it(
        $default_controller,
        $default_method,
        $route[2]
      );
    }
  }

  /**
   * Attempts to execute the specified route (controller, method, args).
   *
   * @param array $route [controller, method, args]
   * @param string $default_method Default method to fallback to
   * @return mixed
   */
  public function route_it($route, $default_method)
  {
    $controller = $route[0];
    $method = $route[1];
    $parameters = $route[2] ?? null;

    $obj = is_object($controller) && ($controller instanceof $controller)
      ? $controller
      : (class_exists($controller) ? new $controller() : false);

    if (!$obj) {
      return false;
    }

    return method_exists($obj, $method)
      ? $obj->$method($parameters)
      : (method_exists($obj, $default_method)
          ? $obj->$default_method($parameters)
          : false);
  }

  /**
   * Fallback to default controller and method if routing fails.
   *
   * @param string $controller Default controller
   * @param string $method Default method
   * @param mixed $parameters Optional arguments
   * @return mixed
   */
  public function default_route_it($controller, $method, $parameters = null)
  {
    $obj = is_object($controller) && ($controller instanceof $controller)
      ? $controller
      : (class_exists($controller) ? new $controller() : false);

    if (!$obj) {
      return false;
    }

    return method_exists($obj, $method)
      ? $obj->$method($parameters)
      : false;
  }
}
```

Of course, this is a very basic class. We could probably enhance with error and/or exception or autoload classes in the include path but I this is the basic idea behind the router.

### Using the Router in the Parent Controller

I decided the respective controllers would be defined in the `$_GET['page']` variable since that’s how WordPress admin navigates plugin pages. The method would come from `$_GET['action']`. Finally, the remaining `$_GET` variables would be the arguments.

Here’s how it might look:

```php
class CRM
{
  public function __construct()
  {
    // Initialize the request handler for routing plugin functionality
    $request_handler = [$this, 'request_handler'];

    // Add main menu and submenu entries for the plugin
    add_menu_page(
      'MY CRM',
      'MY CRM',
      4,
      '?page=CRM',
      $request_handler
    );

    add_submenu_page(
      '?page=CRM',
      'Contacts',
      'Contacts',
      4,
      '?page=CRM',
      $request_handler
    );

    add_submenu_page(
      '?page=CRM',
      'Opportunities',
      'Opportunities',
      4,
      '?page=CRM_Opportunities',
      $request_handler
    );

    add_submenu_page(
      '?page=CRM',
      'Reminders',
      'Reminders',
      4,
      '?page=CRM_Reminders',
      $request_handler
    );
  }

  /**
   * Processes incoming requests and invokes the router.
   */
  public function request_handler()
  {
    // Determine the controller from the "page" parameter
    $controller = $_GET['page'];

    // Default to "index" if no action is specified
    $action = $_GET['action'] ?? 'index';

    // Check if the request is for the current class
    if ($controller == get_class($this)) {
      $controller = $this;
    }

    // Extract additional parameters from GET variables
    $params = $_GET;
    unset($params['page'], $params['action']);

    // Define the route for the router
    $route = [$controller, $action, $params];

    // Route the request using the Router class
    $router = new Router($route, $this, 'index');
  }

  /**
   * Default method when no specific action is specified.
   */
  public function index($args = null)
  {
    echo "Welcome to My CRM.......";
  }
}

class CRM_Opportunities
{
  /**
   * Displays the opportunities page.
   */
  public function index($args = null)
  {
    echo "The Opportunities Page";
  }

  /**
   * Handles the "edit" action for opportunities.
   */
  public function edit($args)
  {
    if ($_POST && !empty($args['id'])) {
      // Process data for editing an opportunity
    }
  }

  /**
   * Handles the "delete" action for opportunities.
   */
  public function delete($args)
  {
    // Logic to delete an opportunity
  }
}
```

### Example

If WordPress admin points to:

```plaintext
wp-admin/admin.php?page=CRM
```

The `CRM` controller runs, and since no action is specified, it defaults to the `index()` method.

If the URL is:

```plaintext
wp-admin/admin.php?page=CRM_Opportunities&action=edit&id=1
```

The `CRM_Opportunities` controller loads, and the `edit()` method runs with the `id` argument.  
  
**Here are some screenshots:**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735189828246/5aca9558-89f8-456f-8dfc-e18b3ecd2e91.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735189938573/7e2ba504-2f7d-44f2-8360-46d9acbc57ee.png align="center")

### Closing Thoughts

You’ll still need to sanitize input and clean POST data, but this should give you an idea of implementing a simple router for controllers in plugin development.

Let’s discuss in the comments!