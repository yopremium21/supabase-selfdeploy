# supabase-selfdeploy

A minimal self-hosted Supabase deployment template using `docker-compose` and Kong as a declarative API gateway.

## Project Overview

This repository provides a lightweight local deployment of a Supabase stack with the following components:

- `db`: PostgreSQL database for Supabase services
- `auth`: GoTrue auth server
- `rest`: PostgREST API server for database access
- `realtime`: Supabase Realtime server
- `storage`: Supabase Storage API
- `meta`: Postgres Meta service for Supabase Studio
- `studio`: Supabase Studio web console
- `kong`: Kong Gateway configured by `kong.yml`

The stack is routed through Kong so the Supabase API endpoints can be exposed behind a single proxy host.

## Files

- `docker-compose.yml`: Docker Compose definition for the Supabase services.
- `kong.yml`: Kong declarative configuration routing incoming requests to Supabase service ports.

## Required Environment Variables

The compose file relies on these environment variables:

- `POSTGRES_PASSWORD` - PostgreSQL password for the `postgres` DB user
- `SUPABASE_PUBLIC_URL` - Public URL for your Supabase instance
- `ANON_KEY` - Supabase anon public key for client requests
- `SERVICE_ROLE_KEY` - Supabase service role key for elevated server access
- `JWT_SECRET` - JWT signing secret shared across Supabase services

Create a `.env` file in the repository root or export these variables in your shell before starting the stack.

Example `.env`:

```env
POSTGRES_PASSWORD=your-postgres-password
SUPABASE_PUBLIC_URL=https://supabase.yourdomain.com
ANON_KEY=your-anon-key
SERVICE_ROLE_KEY=your-service-role-key
JWT_SECRET=your-jwt-secret
```

## Quick Start

1. Clone the repository:

```bash
git clone https://github.com/your-org/supabase-selfdeploy.git
cd supabase-selfdeploy
```

2. Create the `.env` file and set the required variables.

3. Start the stack:

```bash
docker compose up -d
```

4. Verify the containers are running:

```bash
docker compose ps
```

## Service Endpoints

Kong is configured to forward these paths to the internal Supabase services:

- `http://api.yourdomain.com/auth/v1` → `auth`
- `http://api.yourdomain.com/rest/v1` → `rest`
- `http://api.yourdomain.com/realtime/v1` → `realtime`
- `http://api.yourdomain.com/storage/v1` → `storage`

The current `docker-compose.yml` also includes Traefik labels for external TLS routing. Adjust these labels if you use a different proxy or hostname.

## Persistence and Volumes

The deployment uses Docker volumes for persistent storage:

- `supabase_db` for PostgreSQL data
- `supabase_storage` for Supabase Storage files

## Stopping and Removing the Stack

To stop the services:

```bash
docker compose down
```

To stop the stack and remove volumes:

```bash
docker compose down -v
```

## Troubleshooting

- Confirm all required environment variables are set.
- Inspect logs if services fail to start:

```bash
docker compose logs -f
```

- Validate Kong routing with `kong.yml` and your external proxy settings.

## Customization

Adjust the following values to fit your environment:

- `SUPABASE_PUBLIC_URL` for your public host name
- Traefik labels in `docker-compose.yml` if you use a different reverse proxy setup
- Supabase image versions for newer releases
- `kong.yml` if you need custom route prefixes or additional services

## Notes

- `studio` is configured to communicate with `meta` and expects `SUPABASE_URL`, `SUPABASE_PUBLIC_URL`, `ANON_KEY`, and `SERVICE_ROLE_KEY`.
- The storage backend is configured for local file storage via `FILE_STORAGE_BACKEND_PATH`.
- Kong is running in DB-less mode using `kong.yml` as the declarative configuration.

## License

This project is licensed under the GNU General Public License version 3.0.
See the `LICENSE` file for full terms.
