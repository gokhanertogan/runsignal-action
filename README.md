# RunSignal alerts for background jobs

Provision alerts for cron jobs, queue consumers and background workers from CI: New Relic alert policies,
NRQL conditions, workflows and dashboards, or Prometheus alerting rules and a Grafana dashboard.

The action installs the [`runsignal` CLI](https://github.com/gokhanertogan/RunSignal#cli) and runs one of its
commands. It reads the workload manifests that the [RunSignal SDKs](https://github.com/gokhanertogan/RunSignal)
for .NET, Go, Java, Node.js, Python and Rust write, and turns each job's expectations into alerts: **missed**,
**failed**, **overrun**, **stalled**, **low output** and **failure rate**.

## Usage

### New Relic

```yaml
- uses: gokhanertogan/runsignal-action@v1
  with:
    command: provision runsignal.json --account-id 1234567 --region EU --channel critical=<channel-id>
    new-relic-api-key: ${{ secrets.NEW_RELIC_API_KEY }}
```

Run `diff` instead of `provision` on pull requests to see what would change.

### Prometheus and Grafana

```yaml
- uses: gokhanertogan/runsignal-action@v1
  with:
    command: export prometheus runsignal.json -o deploy/runsignal-rules.yml
```

### Writing the manifest

The manifest comes from your running application. Start it once in CI with `RUNSIGNAL_MANIFEST_PATH` set:

```yaml
- run: RUNSIGNAL_MANIFEST_PATH=runsignal.json timeout 60 dotnet run --project src/Billing || true
- uses: gokhanertogan/runsignal-action@v1
  with:
    command: provision runsignal.json --account-id 1234567
    new-relic-api-key: ${{ secrets.NEW_RELIC_API_KEY }}
```

## Inputs

| Input | Required | Description |
|---|---|---|
| `command` | yes | The `runsignal` command and its arguments: `plan`, `diff`, `provision`, `destroy`, `export prometheus` or `export grafana`. |
| `version` | no | The `RunSignal.Cli` version to install. Defaults to the latest release. |
| `new-relic-api-key` | no | A New Relic User API key. Falls back to the `NEW_RELIC_API_KEY` environment variable. |

## License

MIT
