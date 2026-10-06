# CLAUDE.md — OC4 (PHP/Symfony frontend)

## Context

This is the PHP Symfony 7.x frontend. Read the architecture doc first:
https://github.com/hxdimpf/OC/blob/dev-hx/docs/architecture.md

## Rules

1. **Templates are canonical.** OC4 Twig files are the source of truth. All template changes
   happen here first. OC5 Nunjucks files are derived via `../oc/scripts/convert-twig.sh`.

2. **No ORM.** Repositories are plain PHP classes using Doctrine DBAL `Connection`.
   Controllers inject repositories. No entities, no ServiceEntityRepository.

3. **After every deploy, run:** `../oc/scripts/test-deploy.sh oc4`

## Repos

| Repo | Path | Role |
|------|------|------|
| hxdimpf/OC | ~/src/opencaching/oc | Playbook, scripts, docs |
| hxdimpf/OC4 | ~/src/opencaching/oc4 | This repo |
| hxdimpf/oc5 | ~/src/opencaching/oc5 | Node.js version |
| hxdimpf/OC3 | ~/src/opencaching/oc3 | Legacy PHP |

## Architecture

```
Apache → Router → Controller → Repository(QueryBuilder) → Database
                            → Auth (cookie + sys_sessions)
                            → Twig template
```

- `src/Controller/App/` — 12 page controllers
- `src/Controller/Backoffice/` — 8 admin controllers
- `src/Repository/` — 30 plain PHP DBAL repositories
- `src/Security/Auth.php` — 200-line auth service
- `public/_frontend/` — git submodule (shared JS/CSS/vendor)
- `templates/` — 40+ Twig templates (canonical)

## Test server

SSH: `baiti@oc3.baiti.net`
Repo: `/opt/repos/oc4/` (mounted into container as `/var/www/html`)
Container: `oc4-oc4-1` (PHP 8.4 Apache, port 80)
URL: http://oc4.baiti.net

## Image paths

Images at `/images/*` and `/_frontend/*` (symlinked to submodule public/).
Root-level symlinks: `public/{css,js,vendor}` → `public/_frontend/public/{css,js,vendor}`.
