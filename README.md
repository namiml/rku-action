# rku-action

Publish a Roku channel to [rkuhub](https://www.rku.dev) from GitHub Actions — upload a signed `.pkg`, publish it to a channel, and fail the build if the publish fails.

```yaml
jobs:
  publish-roku:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # ... build and sign your Roku .pkg ...

      - uses: namiml/rku-action@v1
        with:
          base-url: https://hub.rku.dev
          token: ${{ secrets.RKU_TOKEN }}
          channel-slug: my-channel
          package-path: ./out/my-app.pkg
          version: ${{ github.sha }}
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `base-url` | yes | — | rkuhub base URL, no trailing slash (e.g. `https://hub.rku.dev`). |
| `token` | yes | — | rkuhub API token, sent as a bearer. Store it as a repo/org **secret**. |
| `channel-slug` | yes | — | Target channel slug (e.g. `nami-dev`). |
| `package-path` | yes | — | Path to the built `.pkg` to upload. |
| `artifact-name` | no | the `.pkg` filename | Name to store the artifact under. |
| `version` | no | — | Version label recorded with the publish (e.g. the git sha or tag). |
| `poll-timeout-seconds` | no | `300` | Max seconds to wait for a terminal job state. |
| `poll-interval-seconds` | no | `5` | Seconds between job-status polls. |

## Outputs

| Output | Description |
|---|---|
| `job-id` | The rkuhub publish job id. |
| `channel-id` | The Roku channel id from the publish result, when available. |

## Exit behavior

- **Success** → the step passes.
- **Failure / timeout** → the step fails (exit 1).
- **Reconnect required** (the linked Roku session expired) → the step fails with a distinct exit code (2). Reconnect rkuhub and re-run.

## Token

Generate an API token in the rkuhub dashboard and store it as a GitHub secret (`RKU_TOKEN` above). The token is scoped to your org and is sent only to your `base-url`.

---

Maintained by [Nami ML](https://www.nami.ml). Your use is subject to the [terms of service](https://www.nami.ml/legal/tos).
