# Blog Post Skill

## Description
Create and manage blog posts for the Hugo Blox Academic CV site.

## When to Use
Use this skill when the user asks to create, edit, or manage blog posts in `content/blog/`.

## Blog Post Structure

Each blog post lives in its own directory under `content/blog/`:
```
content/blog/<slug>/
  index.md          # Main content (required)
  featured.jpg      # Card thumbnail image (REQUIRED for card/grid views)
  *.json            # Data files for charts (optional)
  *.mp3             # Audio files for podcasts (optional)
  *.ipynb            # Jupyter notebooks (optional)
```

**IMPORTANT**: `featured.jpg` is required for the blog card to render correctly in list/grid views. Without it, a placeholder icon is shown. The `cover.image` URL in front matter is only used for the hero banner on the post detail page, NOT for the card thumbnail.

## Front Matter Template

```yaml
---
title: "Post Title"
summary: "A brief description shown in cards and SEO."
date: 2024-01-01

authors:
  - admin

tags:
  - TagName

# Featured image (in page folder)
image:
  caption: 'Image credit: [**Source**](https://example.com)'

# Cover image (hero banner at top of post)
cover:
  image: "https://images.unsplash.com/photo-xxx?q=80&w=2560"
  position:
    x: 50
    y: 40
  overlay:
    enabled: true
    type: "gradient"
    opacity: 0.4
    gradient: "bottom"
  fade:
    enabled: true
    height: "80px"
  icon:
    name: "icon-emoji"

# Optional flags
math: false          # Enable LaTeX math rendering
content_meta:
  trending: true     # Mark as trending
---
```

## Available Shortcodes

### Embed Resources
```go-html-template
{{</* embed platform="github" resource="user/repo" type="repo" */>}}
{{</* embed platform="huggingface" resource="org/model" type="model" */>}}
{{</* embed platform="huggingface" resource="org/dataset" type="dataset" */>}}
{{</* embed url="https://example.com" title="Title" description="Desc" image="url" */>}}
```

### Charts (Plotly)
Save JSON data file in post folder, then:
```go-html-template
{{</* chart data="chart-filename" */>}}
```

### Diagrams (Mermaid)
Use fenced code blocks with `mermaid` language:
- flowchart, sequenceDiagram, classDiagram, stateDiagram, gantt

### Mindmaps (Markmap)
Use fenced code blocks with `markmap` language:
```
```markmap {height="200px"}
- Root
  - Branch 1
  - Branch 2
```

### Video
```go-html-template
{{</* youtube VIDEO_ID */>}}
{{</* bilibili BV_ID */>}}
{{</* video src="my_video.mp4" controls="yes" */>}}
```

### Audio
```go-html-template
{{</* audio src="audio.mp3" */>}}
```

### Jupyter Notebook
```go-html-template
{{</* notebook src="notebook.ipynb" title="Title" show_metadata=true line_numbers=true */>}}
```

### Interactive Elements
```go-html-template
{{</* spoiler text="Click to reveal" */>}}Hidden content{{</* /spoiler */>}}
{{</* button url="/" style="primary" icon="icon-name" */>}}Label{{</* /button */>}}
{{</* cite page="/publications/slug" view="citation" */>}}
{{</* icon name="python" */>}}
{{</* toc mobile_only=true is_open=true */>}}
{{</* table path="data.csv" header="true" caption="Table caption" */>}}
```

### Callouts (Markdown alerts)
```markdown
> [!NOTE]
> Informational note

> [!TIP]
> Helpful tip

> [!IMPORTANT]
> Important info

> [!WARNING]
> Warning message
```

## Homepage Integration

Blog is displayed on the homepage via `content/_index.md` with a `collection` block:
```yaml
- block: collection
  id: blog
  content:
    title: Recent Blog Posts
    page_type: blog
    count: 10
    order: desc
  design:
    view: card
```

Navigation entry in `config/_default/menus.yaml`:
```yaml
- name: Blog
  url: blog/
  weight: 35
```

## Section Index

The blog section index at `content/blog/_index.md`:
```yaml
---
title: Blog
view: article-grid
---
```

Available views: `article-grid`, `card`, `citation`, `compact`, `date-title-summary`.

## Workflow

1. Create directory: `content/blog/<slug>/`
2. Create `index.md` with front matter + content
3. Add optional assets (images, data, notebooks) to the same directory
4. Preview with `pnpm dev`
