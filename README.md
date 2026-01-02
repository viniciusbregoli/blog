# My Programming Blog

A modern, dark-themed personal blog built with Hugo.

## Getting Started

### Run Development Server

```bash
hugo server
```

Visit http://localhost:1313/ to see your blog.

### Create a New Post

```bash
hugo new content posts/your-post-title.md
```

This creates a new markdown file in `content/posts/`.

### Writing a Post

1. Open the newly created file (e.g., `content/posts/your-post-title.md`)
2. Edit the front matter:
   - Change `draft = false` when ready to publish
   - Update the `title`
   - Add a `description` (optional but recommended)
3. Write your content below the `+++` section using Markdown

Example:

```markdown
+++
date = '2026-01-02T10:00:00-03:00'
draft = false
title = 'My Awesome Post'
description = 'A short description of what this post is about'
+++

Your content here with **markdown** formatting!

## Code Examples

Code blocks support syntax highlighting:

```python
def hello():
    print("Hello, world!")
```
```

## Build for Production

Generate static files for deployment:

```bash
hugo
```

Files will be created in the `public/` directory.

## Deploy

### Coolify (Recommended)

1. Push your code to a Git repository (GitHub, GitLab, etc.)
2. In Coolify, create a new resource → Docker-based deployment
3. Connect your Git repository
4. Coolify will automatically detect the Dockerfile and build your site
5. Your blog will be deployed and accessible via your Coolify domain

### Other Options

You can also deploy to:
- GitHub Pages
- Netlify
- Vercel
- Cloudflare Pages

For these services, run `hugo --minify` and deploy the `public/` directory.

## Customization

- **Site title & description**: Edit `hugo.toml`
- **Styling**: Edit `static/css/style.css`
- **Layouts**: Modify files in `layouts/`

## Tips

- Posts must have `draft = false` to appear on the site
- Use code fences with language for syntax highlighting (e.g., ` ```python `)
- Hugo auto-reloads when you save changes during development
