---
title: Assignment 2 - Static Personal Blog Website
date: 2025-04-23
description: This assignment focuses on the creation and deployment of a static personal blog website using Hugo, a fast and flexible static site generator. The project involves setting up a customizable site structure, selecting and integrating a theme, and documenting the development process using Markdown. Git is used for version control to ensure meaningful and traceable progress throughout the development cycle. 
slug: assignment-2
keywords: ["Hugo", "Static Website", "Blog", "LLM", "Software Engineering"]
tags: 
    - Static Sites
    - Hugo
    - GitHub Pages
categories:
    - Assignment
image: cover2.jpg
---

## Introduction to Static Sites

Static websites consist of fixed content delivered to users exactly as stored, unlike dynamic websites that generate content _on-the-fly_. Benefits include:

- **Performance**: Faster load times with pre-built pages
- **Security**: Reduced attack surface with no server-side processing
- **Scalability**: Easier to handle traffic spikes
- **Cost-effectiveness**: Lower hosting requirements

### Key Advantages of Static Sites

| Feature          | Benefit                                                                 |
|------------------|-------------------------------------------------------------------------|
| ⚡ **Performance** | Pre-built pages load instantly with minimal server processing           |
| 🔒 **Security**   | No database or server-side scripts reduces attack vectors               |
| 📈 **Scalability** | Easily handles traffic spikes with simple file serving                  |
| 💰 **Cost**       | Can be hosted on low-cost or free platforms like GitHub Pages           |
| 🔄 **Versioning** | Content is file-based, making it ideal for Git workflows                |

> **Note:** While static sites are excellent for content that doesn't change frequently, they can be enhanced with JavaScript for dynamic functionality when needed.

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
   - Visit [Hugo Releases](https://github.com/gohugoio/hugo/releases) and look for the latest version
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
Once its done a directory structure is created:
my-blog/
├── archetypes/
├── assets/
├── content/
├── data/
├── layouts/
├── static/
├── themes/
└── config.yaml

2. **Add a Theme**
Many themes can be choosen in https://themes.gohugo.io/ that can be downloaded. For this project i choosed to use the stack theme
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

## Version Control

To do the version control, I used Git to track and manage the progress of my static blog website. This ensured that every change was recorded, recoverable, and well-documented throughout the development process.

```bash
git add .
git commit -m "Add Stack theme and configure base settings"
git push origin main
```

This were the key commits and changes made during the development of the static blog website.

### ✅ Initial Commit
- 🧱 Establishes the foundational structure of the project.
- 🛠️ Includes the basic Hugo setup and essential configuration files.
- 🌱 Serves as a clean starting point for all future development.

---

### 📝 Assignment 1 (Completed)
- ✍️ Adds specific content and analysis for Assignment 1.
- 📄 Includes the Markdown post file and related media assets.
- 🔄 Enables easy rollback or isolation of this content if needed.

---

### 🚧 Assignment 2 (In Progress)
- 📌 Creates the draft structure for Assignment 2 post.
- 🗂️ Organizes Markdown content and placeholders for upcoming edits.

---

### 🎨 Sidebar Configuration
- 🧩 Customizes the sidebar layout and user interface.
- 🐝 Adds emoji and inspirational quote as subtitle.
- 🧭 Modifies navigation and theme layout for a more personalized touch.

---

### 🔜 Assignments 3 & 4 (Draft Setup)
- 🗓️ Prepares draft pages for future assignments.
- 📚 Establishes a consistent structure and naming convention.

---

### 📖 Documentation Draft
- 📝 Begins explanation of the project setup and configuration process.
- 📌 Includes markdown usage, theme setup, and development tips.

---

### 👤 About Page Configuration
- 📄 Creates a static "About Me" page.
- 🧬 Adds biography and academic background.
- 🌟 Important page that might be updated frequently as the site evolves.

