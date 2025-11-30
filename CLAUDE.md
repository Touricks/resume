# Hugo Blox Academic CV Resume Template

## Project Summary

This is a Hugo-based static site generator project using the **Hugo Blox Academic CV** theme. It creates professional academic/portfolio websites for researchers, students, and professionals.

## Tech Stack

- **Hugo**: Static site generator (Go-based) - v0.152.2 extended
- **Hugo Blox**: Academic CV theme (formerly Wowchemy)
- **Tailwind CSS v4**: Utility-first CSS framework
- **pnpm**: Node.js package manager
- **Preact**: Lightweight React alternative for UI components

## Project Structure

```
resume/
├── assets/              # Static assets (icons, images, SVGs)
├── config/_default/     # Hugo configuration files
│   ├── hugo.yaml        # Main Hugo configuration
│   ├── params.yaml      # Site parameters (branding, theme, SEO)
│   ├── languages.yaml   # Language settings
│   ├── menus.yaml       # Navigation menu configuration
│   └── module.yaml      # Hugo module dependencies
├── content/             # All site content (Markdown)
│   ├── _index.md        # Homepage content
│   ├── authors/         # Author profiles
│   ├── blog/            # Blog posts
│   ├── courses/         # Course/tutorial content
│   ├── events/          # Events/talks
│   ├── experience.md    # Work experience
│   ├── projects/        # Portfolio projects
│   └── publications/    # Academic publications
├── layouts/             # Custom Hugo templates (overrides)
├── static/              # Static files (copied as-is to output)
├── .github/             # GitHub Actions workflows
├── go.mod               # Hugo module definition
├── package.json         # Node.js dependencies
└── netlify.toml         # Netlify deployment config
```

## Commands

```bash
# Development server (hot reload)
pnpm dev
# or
hugo server --disableFastRender

# Build for production
pnpm build
# or
hugo --minify
```

## Configuration Files

- `config/_default/params.yaml`: Site branding, theme, typography, SEO settings
- `config/_default/hugo.yaml`: Core Hugo settings (language, pagination, taxonomy)
- `config/_default/menus.yaml`: Navigation menu items

## Content Types

1. **Author Profile** (`content/authors/admin/_index.md`)
   - Personal info, education, work experience, skills, awards

2. **Blog Posts** (`content/blog/*/index.md`)
   - Markdown content with optional featured images

3. **Projects** (`content/projects/*/index.md`)
   - Portfolio items showcasing work

4. **Publications** (`content/publications/*/index.md`)
   - Academic papers with BibTeX citations

5. **Events** (`content/events/*/index.md`)
   - Talks, conferences, workshops

6. **Courses** (`content/courses/*/`)
   - Tutorial/course content with nested sections

## Customization Guide

### Branding
Edit `config/_default/params.yaml`:
- `hugoblox.branding.name`: Site name
- `hugoblox.theme.mode`: light/dark/system
- `hugoblox.theme.pack`: Color theme
- `hugoblox.typography.font`: sans/serif/native

### Profile
Edit `content/authors/admin/_index.md`:
- Personal details, bio, social links
- Education, work history
- Skills, languages, awards

### Navigation
Edit `config/_default/menus.yaml` to add/modify menu items.

## Deployment

- **GitHub Pages**: Automated via `.github/workflows/deploy.yml`
- **Netlify**: Configure in `netlify.toml`

## Dependencies

Node.js packages:
- `tailwindcss` v4.x - CSS framework
- `@tailwindcss/cli` - Tailwind build tool
- `@tailwindcss/typography` - Prose styling
- `preact` - UI components

Hugo modules (via go.mod):
- `blox-plugin-netlify` - Netlify optimizations
- `blox-tailwind` - Tailwind integration

## Development Notes

- Hugo Extended version required (includes SCSS support)
- Use `hugo server` for live preview
- Content uses front matter (YAML) + Markdown body
- Images go in content directories as `featured.jpg` or `index.md` siblings
- Custom icons: add SVGs to `assets/media/icons/custom/`