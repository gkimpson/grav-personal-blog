---
title: Getting Started with Framework-X and Twig Templates
date: '2026-06-28'
tags:
  - framework-x
  - php
  - reactphp
  - twig
  - async
  - tutorial
---

# Getting Started with Framework-X and Twig Templates

Framework-X is a modern, async-first PHP web framework built on ReactPHP that makes building fast, concurrent web applications incredibly straightforward. In this guide, we'll set up a new Framework-X project and integrate the Twig template engine to render beautiful HTML pages.

## What is Framework-X?

Framework-X is a PHP web framework that:

- **Runs asynchronously** — handles many concurrent connections efficiently
- **Starts simple** — begin with closure routes, add structure only when needed
- **Keeps hard things possible** — supports async/await patterns, fibers, and promises
- **Works anywhere** — runs with the built-in server, PHP-FPM, Docker, or behind a reverse proxy

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

Create a Twig helper service. Create `src/TemplateEngine.php`:

```php
<?php

namespace App;

use Twig\Environment;
use Twig\Loader\FilesystemLoader;

class TemplateEngine
{
    private Environment $twig;

    public function __construct(string $templatePath)
    {
        $loader = new FilesystemLoader($templatePath);
        
        $this->twig = new Environment($loader, [
            'cache' => false, // Disable cache in development
            'debug' => true,
        ]);
    }

    public function render(string $template, array $data = []): string
    {
        return $this->twig->render($template, $data);
    }
}
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
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            line-height: 1.6;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background: #f5f5f5;
        }
        header {
            background: #333;
            color: white;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 30px;
        }
        h1 {
            margin: 0;
        }
        .post {
            background: white;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .post h2 {
            margin-top: 0;
        }
        .meta {
            color: #666;
            font-size: 0.9em;
        }
    </style>
</head>
<body>
    <header>
        <h1>GAVK Developer Blog</h1>
        <p>Exploring web development with Framework-X and modern PHP</p>
    </header>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer style="text-align: center; color: #666; margin-top: 40px; padding-top: 20px; border-top: 1px solid #ddd;">
        <p>&copy; 2026 GAVK Blog. Built with Framework-X and Twig.</p>
    </footer>
</body>
</html>
```

Create `templates/home.html.twig`:

```twig
{% extends "layout.html.twig" %}

{% block title %}Home - GAVK Developer Blog{% endblock %}

{% block content %}
    <div class="posts">
        {% for post in posts %}
            <article class="post">
                <h2>
                    <a href="/posts/{{ post.id }}" style="text-decoration: none; color: #333;">
                        {{ post.title }}
                    </a>
                </h2>
                <div class="meta">
                    Posted on {{ post.date|date('F j, Y') }}
                </div>
                <p>{{ post.excerpt }}</p>
                <a href="/posts/{{ post.id }}">Read more →</a>
            </article>
        {% else %}
            <p>No posts yet. Check back soon!</p>
        {% endfor %}
    </div>
{% endblock %}
```

## Step 5: Update Your Application

Now update `public/index.php` to use Twig:

```php
<?php

require __DIR__ . '/../vendor/autoload.php';

use FrameworkX\App;
use App\TemplateEngine;
use React\Http\Message\Response;

// Initialize Twig
$templates = new TemplateEngine(__DIR__ . '/../templates');

// Sample data (replace with database queries later)
$posts = [
    [
        'id' => 1,
        'title' => 'Building Async APIs with Framework-X',
        'excerpt' => 'Learn how to build high-performance APIs using ReactPHP and async/await patterns.',
        'date' => new DateTime('2026-06-28'),
    ],
    [
        'id' => 2,
        'title' => 'Introduction to Twig Templating',
        'excerpt' => 'A beginner-friendly guide to using Twig for rendering beautiful HTML.',
        'date' => new DateTime('2026-06-27'),
    ],
];

$app = new App();

// Home page route
$app->get('/', function () use ($templates, $posts) {
    $html = $templates->render('home.html.twig', [
        'posts' => $posts,
    ]);

    return new Response(
        200,
        ['Content-Type' => 'text/html; charset=utf-8'],
        $html
    );
});

// Single post route (placeholder)
$app->get('/posts/{id}', function ($request) use ($templates) {
    $id = $request->getAttribute('id');
    
    $html = $templates->render('post.html.twig', [
        'id' => $id,
        'title' => 'Post #' . $id,
        'content' => 'This is where the full post content would go.',
    ]);

    return new Response(
        200,
        ['Content-Type' => 'text/html; charset=utf-8'],
        $html
    );
});

$app->run();
```

## Step 6: Create a Post Template

Create `templates/post.html.twig`:

```twig
{% extends "layout.html.twig" %}

{% block title %}{{ title }} - GAVK Developer Blog{% endblock %}

{% block content %}
    <article class="post">
        <h1>{{ title }}</h1>
        <div class="meta">
            <a href="/">← Back to home</a>
        </div>
        
        <div style="margin-top: 20px; line-height: 1.8;">
            {{ content }}
        </div>
    </article>
{% endblock %}
```

## Step 7: Test Your Application

```bash
php public/index.php
```

Visit `http://localhost:8080` to see your beautifully styled blog homepage with Twig-rendered templates!

## Key Takeaways

**Framework-X is minimal but powerful** — start with simple routes, add structure when needed

**Twig separation** — keep your templates in separate files for cleaner code

**Async ready** — the built-in server handles concurrent requests efficiently

**Easy to extend** — add services, middleware, or async database queries as your app grows

## Next Steps

Now that you have the basics down, explore:

- **Database integration** — use `react/mysql` for async queries
- **Middleware** — add authentication, logging, or CORS handling
- **Asset pipeline** — minify and bundle CSS/JS
- **Deployment** — run behind Nginx or Docker
- **Testing** — write PHPUnit tests for your controllers

## Resources

- [Framework-X Official Docs](https://github.com/clue/framework-x)
- [Twig Documentation](https://twig.symfony.com/)
- [ReactPHP Documentation](https://reactphp.org/)
- [PHP Async with Fibers](https://www.php.net/manual/en/class.fiber.php)

Framework-X makes building modern PHP applications fast, simple, and fun. Start with the basics we've covered here, and scale up as your needs grow. Happy coding.
