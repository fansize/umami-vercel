# Vercel deployment

This fork deploys Umami 3.5.0 using the [official Vercel guide](https://docs.umami.is/docs/guides/running-on-vercel).

The repository pins Node.js 22.x and pnpm 12.3.4. Vercel must use the repository root and the Next.js preset. `vercel.json` installs the frozen workspace lockfile and runs the complete Umami build, including Prisma generation, database checks and migrations, tracker/recorder generation, workspace packages, and Next.js. Do not replace it with `next build` alone.

Set these in Vercel environment settings; never commit their values:

- `DATABASE_URL`: PostgreSQL connection string, pooled if available.
- `DIRECT_DATABASE_URL`: direct, non-pooled PostgreSQL connection string for migrations when using a pooler (supported since Umami 3.2).
- `APP_SECRET`: a non-empty random secret generated with `openssl rand -hex 32`. Umami 3.5 refuses to start without it, even if the build succeeds. Store it as a Vercel secret and keep it stable across deployments; changing it invalidates existing sessions.
- Preserve existing application secrets and optional settings when upgrading.

Review database backups before upgrading. Production builds apply pending migrations automatically; do not disable database checks or migrations to hide a failed upgrade. Preview builds should use a separate database if enabled, since they run the same build command.

Deploy the existing project with `vercel deploy --prod --project umami-selfhost --scope tang-lang`. Wait for the command to finish and verify `/login`, `/script.js`, and `/api/heartbeat` on https://umami-selfhost-dusky.vercel.app/. A Ready deployment alone does not establish that the production domain points to it. Vercel promotion or alias assignment must also succeed.

The GeoIP download is skipped on Vercel by default, as in upstream Umami. See the [environment variable reference](https://docs.umami.is/docs/environment-variables) for optional configuration.
