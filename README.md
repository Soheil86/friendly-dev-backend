# The Friendly Dev — Backend (Strapi CMS)

[![CI](https://github.com/Soheil86/friendly-dev-backend/actions/workflows/ci.yml/badge.svg)](https://github.com/Soheil86/friendly-dev-backend/actions/workflows/ci.yml)

Headless CMS and REST API for **The Friendly Dev** portfolio, built with **Strapi 5** and **TypeScript**. It stores projects and blog posts in **PostgreSQL** and serves media through **Cloudinary**.

**🖥️ Frontend repo:** [Soheil86/friendly-dev](https://github.com/Soheil86/friendly-dev)
**🔗 Live site:** [friendly-dev-dun.vercel.app](https://friendly-dev-dun.vercel.app)

## Tech stack

- **Strapi 5** (TypeScript): content types, admin panel, REST API
- **PostgreSQL** in production (SQLite works out of the box for local development)
- **Cloudinary** upload provider for images
- **Users & Permissions** plugin with refresh-token JWT management and httpOnly sessions
- Hardened upload config: an allow-list of MIME types, with SVG and executables denied
- Content Security Policy configured for Cloudinary media
- **GitHub Actions** CI (install + build)

## Content model

### Project (`/api/projects`)

| Field         | Type        | Notes                                           |
| ------------- | ----------- | ----------------------------------------------- |
| `title`       | string      |                                                 |
| `description` | string      |                                                 |
| `image`       | media       | Stored on Cloudinary                            |
| `url`         | string      | Link to the live project                        |
| `date`        | date        |                                                 |
| `category`    | enumeration | `Frontend` · `Fullstack` · `Design` · `Mobile`  |
| `featured`    | boolean     | Shown on the homepage                           |

### Post (`/api/posts`)

| Field     | Type     | Notes                     |
| --------- | -------- | ------------------------- |
| `title`   | string   |                           |
| `slug`    | uid      | Generated from the title  |
| `excerpt` | text     |                           |
| `body`    | richtext | Markdown                  |
| `date`    | datetime |                           |
| `image`   | media    | Stored on Cloudinary      |

## Example requests

```bash
# Featured projects with their images
GET /api/projects?filters[featured][$eq]=true&populate=*

# Blog posts, newest first
GET /api/posts?populate=*&sort=date:desc

# A single post by slug
GET /api/posts?filters[slug][$eq]=writing-clean-code&populate=*
```

The frontend reads content without a token, so `find` and `findOne` must be enabled for both content types under **Settings → Users & Permissions → Roles → Public**.

## Getting started

**Prerequisites:** Node.js 20+ and, optionally, PostgreSQL and a Cloudinary account.

```bash
git clone https://github.com/Soheil86/friendly-dev-backend.git
cd friendly-dev-backend
npm install
cp .env.example .env   # fill in secrets, database and Cloudinary values
npm run develop        # admin panel at http://localhost:1337/admin
```

If `DATABASE_CLIENT` isn't set, Strapi falls back to a local SQLite database, which is the quickest way to try it out.

### Environment variables

| Variable | Description |
| -------- | ----------- |
| `HOST`, `PORT` | Server address (default `0.0.0.0:1337`) |
| `APP_KEYS`, `API_TOKEN_SALT`, `ADMIN_JWT_SECRET`, `TRANSFER_TOKEN_SALT`, `JWT_SECRET`, `ENCRYPTION_KEY` | Strapi secrets |
| `DATABASE_CLIENT` | `postgres`, `mysql` or `sqlite` |
| `DATABASE_URL`, or `DATABASE_HOST` / `PORT` / `NAME` / `USERNAME` / `PASSWORD` | Database connection |
| `DATABASE_SSL` | `true` for hosted Postgres |
| `CLOUDINARY_NAME`, `CLOUDINARY_KEY`, `CLOUDINARY_SECRET` | Cloudinary credentials |

### Scripts

| Command           | Description                              |
| ----------------- | ---------------------------------------- |
| `npm run develop` | Start with auto-reload and admin panel   |
| `npm run build`   | Build the admin panel                    |
| `npm run start`   | Start in production mode                 |

## Project structure

```
config/        # Server, database, plugins (Cloudinary, auth), middlewares (CSP)
src/api/
├── project/   # Content type schema, controller, routes, service
└── post/
database/      # Migrations
types/         # Generated TypeScript types
```

## Author

**Soheil Ghanbari** — [GitHub](https://github.com/Soheil86)
