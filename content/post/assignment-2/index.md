---
title: Assignment 2 - Static Personal Blog Website
date: 2025-04-23
description: This assignment focuses on the creation and deployment of a static personal blog website using Hugo, a fast and flexible static site generator. The project involves setting up a customizable site structure, selecting and integrating a theme, and documenting the development process using Markdown. Git is used for version control to ensure meaningful and traceable progress throughout the development cycle. 
slug: assignment-2
tags: 
    - Static Sites
    - Hugo
    - GitHub Pages
categories:
    - Assignment
image: cover2.jpg
---

## Introduction to Static Sites

Static websites consist of fixed content delivered to users exactly as stored, unlike dynamic websites that generate content on-the-fly. Benefits include:

- **Performance**: Faster load times with pre-built pages
- **Security**: Reduced attack surface with no server-side processing
- **Scalability**: Easier to handle traffic spikes
- **Cost-effectiveness**: Lower hosting requirements

## Why Hugo?

Hugo is a modern static site generator written in Go, offering:

- **Blazing fast build times** (often <1ms per page)
- **Rich theming system** with hundreds of community themes
- **Flexible content organization** with sections and taxonomies
- **Built-in development server** with live reload
- **Markdown-based content** with front matter support

## Installation Guide

### Windows Installation

1. **Download the Extended version**:
   - Visit [Hugo Releases](https://github.com/gohugoio/hugo/releases)
   - Under "Assets", download `hugo_extended_*.msi`

2. **Run the installer**:
   - Double-click the downloaded `.msi` file
   - Follow the installation wizard (recommend keeping default options)

3. **Verify installation**:
   ```powershell
   hugo version
  ```
You should see output like hugo v0.xxx windows/amd64 Extended confirming the Extended build is installed.

## Project Setup
1. **Initialize Your Site**
```bash
hugo new site my-blog
cd my-blog
git init
```
2. **Add a Theme (Using Stack Theme)**
```bash
git submodule add https://github.com/CaiJimmy/hugo-theme-stack themes/stack
```
Add to config.toml:

```toml
theme = "stack"
```
3. **Configure Basic Settings**
Edit config.toml with essential parameters:

```toml
baseURL = "https://yourusername.github.io/"
languageCode = "en-us"
title = "My Personal Blog"
```
## Content Creation
### Creating Posts
Generate a new post:

```bash
hugo new posts/assignment-1.md
```
Example post structure:

```markdown
---
title: "Assignment 1: Static Site Setup"
date: 2025-04-23T15:30:00Z
draft: false
tags: ['Hugo', 'Static Sites']
categories: ['Assignments']
description: "Initial setup of my static blog using Hugo"
---
## Introduction
Content goes here...
```
### Additional Pages
Create an about page:

```bash
hugo new about.md
```
## GitHub Pages Deployment
1. Create a GitHub repository named yourusername.github.io

2. Add remote and push:

```bash
git remote add origin git@github.com:yourusername/yourusername.github.io.git
git push -u origin main
```
3. Enable GitHub Pages in repository settings (use gh-pages branch)

## Version Control Best Practices
Make atomic commits with clear messages:

```bash
git add .
git commit -m "Add Stack theme and configure base settings"
git push origin main
```
Use feature branches for significant changes:

```bash
git checkout -b feature/new-layout
# Make changes
git push origin feature/new-layout
```