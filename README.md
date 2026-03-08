# [luizcarloscf.github.io](https://luizcarloscf.github.io)

Personal homepage and portfolio of **Luiz Carlos Cosmi Filho** — Electrical Engineer and researcher focused on deep learning, computer vision, and DevOps.

Built with [Hugo](https://gohugo.io/) and the [Blowfish](https://blowfish.page/) theme. Automatically deployed to GitHub Pages via GitHub Actions.

## Prerequisites

- [Hugo](https://gohugo.io/installation/) (extended edition)
- [Go](https://go.dev/dl/) 1.25.3+
- [Git](https://git-scm.com/)

## Getting started

### Clone the repository

```bash
git clone --recurse-submodules https://github.com/luizcarloscf/luizcarloscf.github.io.git
cd luizcarloscf.github.io
```

> The `--recurse-submodules` flag is required to pull the Blowfish theme, which is included as a Git submodule.

### Run the development server

```bash
hugo server -D
```

The site will be available at [http://localhost:1313](http://localhost:1313). The `-D` flag includes draft content.

### Build for production

```bash
hugo --minify
```

The generated static files are written to the `public/` directory.

## Project structure

```
.
├── archetypes/          # Default front matter templates for new content
├── assets/img/          # Source images processed by Hugo Pipes
├── config/_default/     # Site configuration (Hugo, params, menus, markup, etc.)
├── content/             # Site content (Markdown)
│   ├── about/           # About me page
│   ├── posts/           # Blog posts
│   ├── projects/        # Project showcases
│   └── research/        # Research and publications
├── data/                # Data files (publications.json)
├── layouts/shortcodes/  # Custom Hugo shortcodes (publications)
├── public/              # Generated static site (build output)
├── static/              # Static assets served as-is
├── themes/blowfish/     # Blowfish theme (Git submodule)
├── go.mod               # Hugo module definition
└── go.sum               # Hugo module checksums
```

## Configuration

All configuration files live under `config/_default/`:

| File | Purpose |
|------|---------|
| `hugo.toml` | Core Hugo settings (base URL, taxonomies, outputs, pagination) |
| `params.toml` | Theme parameters (layout, article options, appearance) |
| `languages.en.toml` | Language settings & author profile |
| `menus.en.toml` | Navigation menu items |
| `markup.toml` | Goldmark / syntax highlighting settings and KaTeX passthrough |
| `module.toml` | Hugo module imports (Blowfish theme) |

## Deployment

The site is automatically built and deployed to GitHub Pages on every push to `main` via the GitHub Actions workflow in `.github/workflows/gh-pages.yml`.

The workflow:
1. Checks out the repo with submodules
2. Sets up Hugo (extended, latest version)
3. Builds the site with `hugo --minify`
4. Deploys the `public/` directory to the `gh-pages` branch

## License

Content &copy; Luiz Carlos Cosmi Filho. Theme by [Blowfish](https://blowfish.page/).