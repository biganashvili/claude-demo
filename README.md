# claude-demo

A small HTTP server in Go, built on the [Echo](https://echo.labstack.com/) framework. It has one endpoint, `/health`.

## Requirements

- Go 1.26 or newer. If your Go is older, the toolchain downloads 1.26 automatically.

## Quick start

```sh
go run .
```

```sh
curl localhost:8080/health
# {"status":"ok"}
```

## Configuration

| Variable | Default | Description          |
|----------|---------|----------------------|
| `PORT`   | `8080`  | Port the server uses |

```sh
PORT=9090 go run .
```

## API

### `GET /health`

Shows whether the server is up.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"status":"ok"}
```

## Server behavior

- **Timeouts:** the server cuts off slow clients. It allows 5s to receive request headers, 10s to read the full request, and 10s to write the response. Idle keep-alive connections close after 60s.
- **Graceful shutdown:** on `SIGINT` or `SIGTERM`, the server stops accepting new connections and gives in-flight requests up to 10s to finish.
- **Startup errors:** if the server can't start, for example because the port is in use, it logs the error and exits with code 1.
- **Middleware:** every request is logged, and a panic in a handler returns a 500 instead of crashing the process.

## Development

```sh
go test ./...   # run tests
go vet ./...    # static checks
go build -o server .
```

Code layout:

- `main.go` – server setup (`newServer`), startup and shutdown (`run`), and the health handler
- `main_test.go` – tests that send requests through the full router

## Automated PR review

Claude reviews each pull request automatically using [`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action) (`.github/workflows/claude-review.yml`). It posts comments on specific lines and a summary comment.

- **When it runs:** when a PR is opened, reopened, marked ready for review, or gets a new push. Draft PRs are skipped.
- **Fork PRs:** skipped, because repository secrets aren't available to them.

To set it up:

1. Run `claude setup-token` and copy the token. This needs a Claude Pro, Max or Team plan.
2. Add the token as a repository secret named `CLAUDE_CODE_OAUTH_TOKEN`, under **Settings → Secrets and variables → Actions**.
3. Optional: install the [Claude GitHub App](https://github.com/apps/claude) so comments come from the Claude bot.
