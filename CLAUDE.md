# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Jekyll blog using the Chirpy theme for ObjectivePixel Blog. It's a Jekyll site that uses Ruby/Bundler for dependency management and is designed to be deployed to GitHub Pages.

## Development Commands

### Installation
```bash
bundle install
```

### Local Development Server
```bash
bash tools/run.sh
```
- Runs Jekyll in development mode with live reload
- Available options:
  - `-H, --host [HOST]` - Host to bind to (default: 127.0.0.1)
  - `-p, --production` - Run Jekyll in production mode
  - `-h, --help` - Print help information

### Build and Test
```bash
bash tools/test.sh
```
- Builds the site in production mode
- Runs HTML validation with htmlproofer
- Available options:
  - `-c, --config "<config_a[,config_b[...]]>"` - Specify config file(s)
  - `-h, --help` - Print help information

### Manual Jekyll Commands
```bash
# Development server
bundle exec jekyll serve -l

# Production build
JEKYLL_ENV=production bundle exec jekyll build

# Run tests
bundle exec htmlproofer _site --disable-external --ignore-urls "/^http:\/\/127.0.0.1/,/^http:\/\/0.0.0.0/,/^http:\/\/localhost/"
```

## Site Structure

- `_config.yml` - Jekyll configuration file
- `_posts/` - Blog posts directory
- `_tabs/` - Static pages (About, Archives, Categories, Tags)
- `_data/` - Data files (contact.yml, share.yml)
- `_plugins/` - Jekyll plugins
- `_site/` - Generated site output (excluded from git)
- `assets/` - Static assets (images, CSS, JS)
- `tools/` - Development scripts (run.sh, test.sh)
- `Gemfile` - Ruby dependencies

## Theme Details

Uses the Chirpy Jekyll theme (v7.3+) which provides:
- Responsive design
- Dark/light theme toggle
- Search functionality
- TOC (Table of Contents) support
- PWA (Progressive Web App) features
- Social media integration
- Analytics support

## Configuration

Key configuration is in `_config.yml`:
- Site metadata (title, description, author)
- Social media links
- Analytics settings
- Theme customization options
- Avatar/logo at `/assets/objpxllogo.png`

## Content Management

- Blog posts go in `_posts/` directory
- Use Jekyll front matter for post metadata
- Supports markdown with syntax highlighting
- Comments can be enabled via Disqus/Utterances/Giscus
- TOC is enabled by default for posts