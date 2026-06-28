---
title: Getting Started with Grav CMS
date: '2026-06-28'
tags:
  - grav
  - cms
  - tutorial
  - php
---

# Getting Started with Grav CMS

Grav is a modern, file-based content management system that doesn't require a database. It's perfect for developers who want full control over their content and codebase.

## Why Grav?

- **No Database Required**: All content is stored in Markdown files and YAML configuration
- **Fast**: Built with performance in mind from the ground up
- **Flexible**: Use Twig templating for complete control over your frontend
- **Developer Friendly**: Works seamlessly with Git and version control

## Installation

Getting Grav up and running is incredibly simple. Here's how:

```bash
# Clone from GitHub
git clone https://github.com/getgrav/grav.git ~/webroot/grav
cd ~/webroot/grav

# Install dependencies
composer install

# Start the dev server
bin/grav server
```

Your site will be available at `http://localhost:8000`.

## File Structure

Grav uses a simple, intuitive folder structure:

```
grav/
├── user/
│   ├── pages/          # Your content (Markdown files)
│   ├── themes/         # Theme files
│   ├── plugins/        # Plugins
│   ├── config/         # Site configuration
│   └── data/           # Custom data storage
├── system/             # Grav core files
└── bin/                # CLI tools
```

## Creating Your First Page

Creating content in Grav is simple. Each page is just a Markdown file with YAML frontmatter:

```markdown
---
title: My First Page
date: '2026-06-28'
---

# Welcome

This is my first page in Grav!
```

## Next Steps

- Explore the [Grav Documentation](https://learn.getgrav.org)
- Install a theme using `bin/gpm install theme-name`
- Add plugins to extend functionality
- Deploy to your hosting provider

Happy coding!
