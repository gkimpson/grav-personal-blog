# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Grav is a flat-file, database-free content management system written in PHP 8.3+. It's designed to be fast, simple, and flexible with zero installation required.

## Core Architecture

**System Components** (`system/src/Grav/`):
- `Common/` — Core utilities and foundational classes (Grav, Assets, Config, Processor, etc)
- `Framework/` — Modern framework layer with form handling, blueprints, and collection management
- `Events/` — Event dispatcher and event classes used throughout Grav
- `Console/` — CLI command infrastructure for bin/grav and bin/gpm
- `Installer/` — Plugin/theme installation and package management logic

**Dependency Management**:
- Pimple container (`system/src/Pimple/`) — service locator and dependency injection
- Services registered in `Grav` singleton — accessed as `$grav['service_name']`
- Framework config loads from `system/config/` and user config from `user/config/`

**Key Technologies**:
- Twig 3 templating with custom extensions
- Symfony components (Cache, Console, Event Dispatcher, YAML, DotEnv)
- Parsedown for Markdown parsing
- Monolog for logging
- Gregwar Image library for dynamic image manipulation

**Content Structure** (`user/`):
- `pages/` — markdown-based content files with YAML frontmatter
- `config/` — YAML configuration files (site.yaml, system.yaml, security.yaml, etc)
- `themes/` — theme packages
- `plugins/` — plugin packages
- `data/` — arbitrary user data storage

## Development Commands

### Testing
```bash
# Run all unit tests
composer test

# Run on Windows
composer test-windows

# Run a single test file
composer test tests/unit/Grav/Common/AssetsTest.php

# Run a specific test method
composer test tests/unit/Grav/Common/AssetsTest.php --filter testAddingAssets
```

### Static Analysis
```bash
# PHPStan level 2 (global analysis)
composer phpstan

# PHPStan level 6 (framework-specific, stricter)
composer phpstan-framework

# PHPStan level 1 (installed plugins)
composer phpstan-plugins
```

### Code Compatibility
```bash
# Check PHP compatibility and auto-fix
composer rector:php-compat
```

### Local Development
```bash
# Install dependencies
composer install

# Start development server on http://localhost:8000
bin/grav server

# Install Grav (downloads plugins/themes)
bin/grav install
```

## Testing Patterns

Tests use Codeception with PHPUnit backend. Test structure mirrors source structure:
- Unit tests in `tests/unit/Grav/` matching `system/src/Grav/` namespaces
- Tests extend `\PHPUnit\Framework\TestCase`
- Use `Codeception\Util\Fixtures` to access test fixtures
- The `$grav` service container is available in fixtures via `Fixtures::get('grav')()`

Example test pattern:
```php
class MyTest extends \PHPUnit\Framework\TestCase
{
    protected function setUp(): void {
        parent::setUp();
        $grav = Fixtures::get('grav');
        $this->service = $grav()['service_name'];
    }
}
```

## Pull Request Guidelines

- Target the `develop` branch, not `main`
- Grav 2.x requires PHP 8.3+ baseline (major version with modernized core)
- Ask in Discord/Forum before embarking on significant PRs to avoid wasted effort
- Translations for core/admin go to Crowdin, not GitHub PRs
- All other plugin/theme translations are handled in their own GitHub repos

## Key Files and Entry Points

- `index.php` — main application entry point
- `system/defines.php` — global constants and initialization (GRAV_ROOT, GRAV_VERSION, etc)
- `bin/grav` — CLI application bootstrap
- `system/router.php` — URL routing
- `system/blueprints/` — form schema definitions (YAML)
- `system/templates/` — system twig templates
