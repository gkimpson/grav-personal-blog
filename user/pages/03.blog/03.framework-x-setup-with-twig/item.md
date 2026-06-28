---
title: Getting Started with Framework-X and Twig Templates
description: Learn how to build a simple Framework-X application with Twig templating. A micro-framework guide for async PHP development with event-loop architecture.
keywords: framework-x, php, reactphp, twig, async, micro-framework, tutorial
date: '2026-06-28'
tags:
  - framework-x
  - php
  - reactphp
  - twig
  - async
  - tutorial
og:
  title: Getting Started with Framework-X and Twig Templates
  description: Build an async PHP application with Framework-X and Twig templating. Learn event-loop architecture vs PHP-FPM.
  image: /images/framework-x.jpg
canonical: https://gavk.dev/blog/framework-x-setup-with-twig
---

Framework-X is a modern, async-first PHP web framework built on ReactPHP. What I love about it is that it doesn't force you into a rigid structure. You can start with a simple script and grow it as your needs evolve. In this guide, we'll build a very simple application and integrate the Twig template engine to render HTML pages. Don't worry if you're new to async PHP. We'll keep things straightforward.

## What is Framework-X?

Framework-X is a micro-framework built on ReactPHP. Unlike Laravel or Symfony, which come packed with batteries, Framework-X stays lean. It gives you routing, a container, and middleware support, but then gets out of the way. You decide what else you need.

What sets Framework-X apart is its architecture. Traditional PHP frameworks like Laravel run on PHP-FPM, which follows a "boot and die" model. Each request spins up a new PHP process, runs your code, and shuts down. This works, but it's wasteful for high-concurrency scenarios.

Framework-X runs on a persistent event-loop. The process starts once and stays alive, handling many concurrent requests without booting up for each one. This is a fundamentally different execution model. You get better performance, lower latency, and the ability to maintain state across requests if you need to.

This approach works well if you're building APIs, microservices, or applications that need to handle many concurrent connections efficiently.

Framework-X is a PHP web framework that:

- **Runs asynchronously.** Handles many concurrent connections efficiently.
- **Starts simple.** Begin with closure routes, add structure only when needed.
- **Keeps hard things possible.** Supports async/await patterns, fibers, and promises.
- **Works anywhere.** Runs with the built-in server, PHP-FPM, Docker, or behind a reverse proxy.

It's perfect for building REST APIs, web applications, real-time services, and traditional web apps.

## Project Structure

A typical Framework-X project looks like this:

```
framework-x-blog/
├── public/
│   ├── index.php           # Entry point
│   └── assets/             # CSS, JS, images
├── src/
│   ├── Controller/         # Controller classes
│   ├── Service/            # Business logic
│   └── Repository/         # Data access
├── templates/              # Twig templates
│   ├── layout.html.twig
│   ├── post/
│   │   ├── list.html.twig
│   │   └── show.html.twig
│   └── home.html.twig
├── composer.json
├── .env
└── .gitignore
```

## Key Concepts

**Routing:** Define URL patterns that map to handlers (closures or controllers)

```php
$app->get('/', $handler);           // GET /
$app->post('/posts', $handler);     // POST /posts
$app->get('/posts/{id}', $handler); // GET /posts/123
```

**Controllers:** Invokable classes that handle requests

```php
class PostController {
    public function __invoke(ServerRequestInterface $request) {
        return new Response(...);
    }
}
```

**Async/Await:** Handle I/O operations concurrently using PHP 8.1+ fibers

```php
use function React\Async\await;

$result = await($database->query('SELECT * FROM posts'));
```

## Step 1: Create a New Project

```bash
# Create project directory
mkdir framework-x-blog
cd framework-x-blog

# Initialize composer project
composer init

# Install dependencies
composer require clue/framework-x react/http twig/twig
```

## Step 2: Create the Entry Point

Create `public/index.php`:

```php
<?php

require __DIR__ . '/../vendor/autoload.php';

use FrameworkX\App;
use React\Http\Message\Response;

$app = new App();

// Simple route
$app->get('/', function () {
    return Response::plaintext("Welcome to Framework-X!\n");
});

$app->run();
```

Start the server:

```bash
php public/index.php
# Listening on http://127.0.0.1:8080
```

Visit `http://localhost:8080` and you'll see your response!

## Step 3: Install and Configure Twig

Install Twig via Composer:

```bash
composer require twig/twig
```

Twig is Framework-X's recommended template engine. Create the Twig Environment and pass it to your app via the dependency injection container:

```php title="public/index.php"
<?php

require __DIR__ . '/../vendor/autoload.php';

use FrameworkX\App;
use FrameworkX\Container;
use Twig\Environment;
use Twig\Loader\FilesystemLoader;

$loader = new FilesystemLoader(__DIR__ . '/../templates');
$twig = new Environment($loader, [
    'cache' => false, // Disable cache in development
]);

$app = new App(new Container([
    Environment::class => $twig,
]));

$app->get('/', Acme\Todo\HomeController::class);
$app->run();
```

For production, enable caching so templates compile once and persist in memory:

```php
$twig = new Environment($loader, [
    'cache' => __DIR__ . '/../cache/twig',
]);
```

## Step 4: Set Up Templates

Create the `templates/` directory and add a layout template `templates/layout.html.twig`:

```twig
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Framework-X Blog{% endblock %}</title>
</head>
<body>
    <header>
        <h1>Gavin Kimpson : Blog</h1>
    </header>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer>
        <p>&copy; 2026 Built with Framework-X and Twig.</p>
    </footer>
</body>
</html>
```

Create `templates/home.html.twig`:

```twig
{% extends "layout.html.twig" %}

{% block title %}Blog{% endblock %}

{% block content %}
    {% for post in posts %}
        <article>
            <h2><a href="/posts/{{ post.id }}">{{ post.title }}</a></h2>
            <p>{{ post.date|date('F j, Y') }}</p>
            <p>{{ post.excerpt }}</p>
        </article>
    {% else %}
        <p>No posts yet. Check back soon.</p>
    {% endfor %}
{% endblock %}
```

## Step 5: Create a Controller

Create `src/Acme/Todo/HomeController.php`:

```php
<?php

namespace Acme\Todo;

use Twig\Environment;
use Psr\Http\Message\ServerRequestInterface;
use React\Http\Message\Response;

class HomeController
{
    public function __construct(private Environment $twig)
    {
    }

    public function __invoke(ServerRequestInterface $request): Response
    {
        $html = $this->twig->render('home.html.twig', [
            'title' => 'Welcome to Framework X',
        ]);

        return Response::html($html);
    }
}
```

The Twig Environment is passed via dependency injection from the container. Render your template with data and return Response::html() to send HTML to the client.

## Step 6: Other Template Engines

Twig is the recommended option, but Framework-X works with any PHP template engine:

- **Plates** - Simple, markup-based templates without a new language
- **Latte** - Nette's template engine with clean syntax
- **Mustache** - Logic-less templates
- **Handlebars** - Logic-less templates with familiar syntax

Choose based on your preference and project needs.

## Step 7: Important Considerations

Don't reload templates from the filesystem on every request. Twig caches compiled templates in memory. Disable caching in development so changes show immediately:

```php
$twig = new Environment($loader, [
    'cache' => false, // Development
]);
```

In production, enable caching so templates compile once:

```php
$twig = new Environment($loader, [
    'cache' => __DIR__ . '/../cache/twig', // Production
]);
```

## Test Your Application

```bash
php public/index.php
```

Visit `http://localhost:8080` to see your HTML rendered by Twig.

## Understanding the event-loop model

Since Framework-X keeps the process alive between requests, you need to be aware of a few things. Variables in your application live for the lifetime of the process, not just a single request. This means you can cache data, maintain database connections, or keep state if you're careful about it.

The downside is you can't just dump all your session state in a global variable and expect it to work like traditional PHP. You need to think about request isolation and cleanup. Reading the official docs helps here.

Also, never use blocking I/O calls (like `file_get_contents()` on a remote URL or synchronous database queries) in hot request paths. They'll block the entire event-loop and freeze all concurrent requests. This is where async methods like `await()` come in. But don't worry if this sounds complicated now, it becomes natural quickly.

## A note on best practices

This is a very simple application to get you started. As you build more complex features, take time to read the official Framework-X documentation, especially the [best practices guide](https://framework-x.org/docs/best-practices/controllers/). It covers controller architecture, middleware patterns, async handling, and more. The team behind Framework-X knows what they're doing, and their docs are genuinely helpful.

## Key Takeaways

**Framework-X is minimal but powerful.** Start with simple routes, add structure when needed.

**Twig separation.** Keep your templates in separate files for cleaner code.

**Async ready.** The built-in server handles concurrent requests efficiently.

**Easy to extend.** Add services, middleware, or async database queries as your app grows.

## Next Steps

Now that you have the basics down, explore:

- **Database integration.** Use `react/mysql` for async queries.
- **Middleware.** Add authentication, logging, or CORS handling.
- **Asset pipeline.** Minify and bundle CSS/JS.
- **Deployment.** Run behind Nginx or Docker.
- **Testing.** Write PHPUnit tests for your controllers.

## Resources

- [Framework-X Official Docs](https://github.com/clue/framework-x)
- [Twig Documentation](https://twig.symfony.com/)
- [ReactPHP Documentation](https://reactphp.org/)
- [PHP Async with Fibers](https://www.php.net/manual/en/class.fiber.php)

I genuinely enjoy building with Framework-X. It respects your intelligence as a developer and gets out of the way when you need it to. Start with what we've covered here, and you'll find your own path as things get more complex. Let me know if you hit any snags with this setup.
