# iamian.dev

A multilingual (EN/ES/PT) **portfolio site** built with **Django's static export** to host on GitHub Pages. It showcases projects, an About page, optional Notes, and a **Curriculum (PDF) download** per language.

## ✨ Features
- **5–6 pages**: Home, Projects, About, Contact, (optional) Notes/Lab.
- **Language switcher**: English, Español, Português (URL prefixes `/en/`, `/es/`, `/pt/`).
- **Static export**: `django-distill` builds a no-server site.
- **Tailwind CSS**: small, fast, responsive UI.
- **SEO**: sitemap, robots, OpenGraph, `hreflang`.
- **Privacy-friendly analytics** (Plausible, optional).

## 🧱 Tech & Architecture
- **Django 5**, `django-distill`, `markdown2`, `PyYAML`
- **Tailwind CLI** (no Node runtime in prod), **HTMX** (light UX)
- Content = YAML (meta) + Markdown (long text) under `/content`
- PDFs at `static/cv/ian-cv-EN.pdf` (also `ES`/`PT`)

repo/
├─ core/ # settings/urls/wsgi
├─ pages/ # home/about/contact (+ distill config)
├─ projects/ # list/detail, reads YAML+MD
├─ notes/ # optional blog/lab (can be skipped)
├─ assets/ # tailwind input
├─ static/ # built CSS/JS/images + cv/
├─ templates/ # base.html + pages
├─ content/
│ └─ projects/{slug}/
│ ├─ meta.en.yaml | meta.es.yaml | meta.pt.yaml
│ └─ body.en.md | body.es.md | body.pt.md
└─ distill/ # static output folder (generated)

## 🚀 Getting Started

python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -U django django-distill markdown2 pyyaml

django-admin startproject core .
python manage.py startapp pages
python manage.py startapp projects
# (optional)
python manage.py startapp notes
Tailwind (dev)

npm init -y
npm i -D tailwindcss
npx tailwindcss init
# Build once (or --watch during dev)
npx tailwindcss -i ./assets/tailwind.css -o ./static/build.css
i18n
In settings.py: LANGUAGES = [('en','English'),('es','Español'),('pt','Português')] and enable LocaleMiddleware.
python manage.py makemessages -l es -l pt
python manage.py compilemessages

🔗 URLs & i18n Patterns
Wrap app URLs with i18n_patterns in core/urls.py.

Add language switch form posting to django.views.i18n.set_language.

🗂 Content Model (Projects)
content/projects/my-project/meta.en.yaml:

yaml
Copy code
title: My Project
subtitle: Lightweight ETL + Dashboard
stack: [Python, PostgreSQL, Power BI]
year: 2025
tags: [ETL, BI]
links:
  repo: https://github.com/...
  demo: https://...
thumbnail: /static/img/projects/my-project.png
content/projects/my-project/body.en.md: long description (use Markdown).

Provide meta.es.yaml, meta.pt.yaml, body.es.md, body.pt.md for locales.

📄 Curriculum PDFs
Place files in static/cv/:

ian-cv-EN.pdf, ian-cv-ES.pdf, ian-cv-PT.pdf
Navbar “Curriculum” button picks the file according to current locale.

🧪 Run, Build, Export
python manage.py runserver
python manage.py collectstatic --noinput
python manage.py distill-local --collectstatic --force   # outputs to /distill
☁️ Deploy
GitHub Pages

Create branch gh-pages.

CI builds /distill and publishes to gh-pages.

In repo settings → Pages → Source: gh-pages.

Set custom domain: iamian.dev and add CNAME.

Netlify

Site settings → Build command: (none); Publish directory: distill/.

Add custom domain iamian.dev (CNAME/ANAME at your DNS).

🛠 CI (GitHub Actions)
.github/workflows/deploy.yml (excerpt):

yaml
Copy code
name: build-and-deploy
on:
  push:
    branches: [ main ]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: '3.12' }
      - run: pip install -U django django-distill markdown2 pyyaml
      - run: python manage.py collectstatic --noinput
      - run: python manage.py distill-local --collectstatic --force
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: distill
          publish_branch: gh-pages
          force_orphan: true
🔍 SEO & Analytics
sitemap.xml, robots.txt, og:image, canonical + hreflang.

(Optional) Plausible script (respect DNT; no cookies).

✅ Launch Checklist
 All pages in EN/ES/PT render and export

 CV buttons download correct locale PDFs

 Lighthouse ≥ 95 (Performance/Best-Practices/SEO)

 OG preview image looks good when sharing

 Sitemap submitted to Google/Bing

 Domain iamian.dev points to Pages/Netlify

🗺 Roadmap
Dark mode toggle

Project filters (tags/stack)

RSS for Notes

PWA basics (offline shell)