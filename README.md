# LiveKit Token Server Action

[![CI](https://github.com/livekit/token-server-action/actions/workflows/ci.yml/badge.svg)](https://github.com/livekit/token-server-action/actions/workflows/ci.yml)

Install and run a LiveKit token server for end-to-end testing. The server exposes a `/createToken` endpoint that mints JWTs following the [LiveKit endpoint token schema](https://docs.livekit.io/frontends/build/authentication/endpoint/).

## Usage

```yaml
- uses: livekit/token-server-action@v1
```

When paired with [dev-server-action](https://github.com/livekit/dev-server-action), all inputs can be omitted — they default to the local dev server (`ws://localhost:7880`) and its credentials (`devkey` / `secret`).

Pair with [dev-server-action](https://github.com/livekit/dev-server-action) to stand up a full local LiveKit stack in CI:

```yaml
- name: Start LiveKit server
  uses: livekit/dev-server-action@v1
  with:
    github-token: ${{ github.token }}

- name: Start token server
  id: token_server
  uses: livekit/token-server-action@v1

- name: Run integration tests
  env:
    TOKEN_URL: ${{ steps.token_server.outputs.token-url }}
  run: ./run-tests
```

## Inputs

| Name           | Required | Default | Description                                                          |
| -------------- | -------- | ------- | -------------------------------------------------------------------- |
| `livekit-url`  | No       | `ws://localhost:7880` | LiveKit server URL the minted tokens should target. Matches [dev-server-action](https://github.com/livekit/dev-server-action) defaults. |
| `api-key`      | No       | `devkey` | LiveKit API key used to sign tokens. Matches [dev-server-action](https://github.com/livekit/dev-server-action) defaults. |
| `api-secret`   | No       | `secret` | LiveKit API secret used to sign tokens. Matches [dev-server-action](https://github.com/livekit/dev-server-action) defaults. |
| `port`         | No       | `3000`  | Port the token server listens on.                                    |

## Outputs

| Name        | Description                                          |
| ----------- | ---------------------------------------------------- |
| `token-url` | Full URL of the `/createToken` endpoint.             |
| `log-path`  | Path to the token server's captured stdout/stderr log. |

## Endpoint

The token server accepts `POST /createToken` with a JSON body containing any of:

- `room_name` (string, optional)
- `participant_identity` (string, optional)
- `participant_name` (string, optional)
- `participant_metadata` (string, optional)
- `participant_attributes` (map, optional)
- `room_config` (RoomConfiguration, optional)

It responds with:

```json
{
  "server_url": "ws://localhost:7880",
  "participant_token": "<jwt>"
}
```

## Local Development

```console
pnpm install
cp .env.example .env.local
# fill in LIVEKIT_URL, LIVEKIT_API_KEY, LIVEKIT_API_SECRET
pnpm build && pnpm start
```
