# hidane benchmark results

The results of hidane's **canonical benchmark**: [hidane](https://github.com/hidane-dev/hidane)
and the official Cloud Firestore emulator, measured on the same dedicated machine, with the same
client, data, workload and method, every night (hidane's `main`) and for every release (its
published binary).

The numbers hidane's README and release notes quote come from the **release** results here, and
each quote names the result it comes from. Nothing here is a single best run: every metric keeps
its raw samples and their median, p95, minimum, maximum, mean and standard deviation, and
comparisons use medians. Measurements are never edited after publication; the rare correction
(a commit a run failed to record, for example) is listed in [PROVENANCE.md](PROVENANCE.md) with
the evidence behind it.

## Layout

| Path | What |
|---|---|
| `environments/<name>-r<revision>.json` | The machine and its software for one environment revision: instance type, OS image, disk, Java, Node.js and firebase-tools versions, methodology version |
| `results/nightly/YYYY/MM/` | Nightly results: a `.json` and the `.md` rendered from it |
| `results/release/YYYY/MM/` | Release results |
| `index.json` | Every result, with its kind, time, environment revision, hidane version and commit, official emulator version, and whether its environment was stable |
| `PROVENANCE.md` | What each result traces to (commits, versions, environment), and every correction made to a published result, with its evidence |
| `metrics/YYYY/MM/YYYY-MM-DD.json` | Adoption metrics: one snapshot a day (see below) |
| `metrics/latest.json`, `metrics/schema.json` | The newest snapshot, and the JSON Schema of every snapshot |

Results are compared only within one environment revision: a new revision (another machine image,
kernel series, Java or Node.js major, firebase-tools baseline, disk, instance type or method)
starts a new series. A run whose environment was not stable (CPU steal, load, memory pressure) is
kept, marked `stable: false`, and left out of baselines and quoted numbers.

## Reproducing

The benchmark is `bench/` in hidane's repository; `bench/run.sh` runs the same measurements on any
Linux or macOS machine, and `hidane benchmark --compare-official` runs them from the binary. Its
methodology and the schema of these files are documented there.

## The machine

An AWS EC2 `c7i.xlarge` (4 vCPU, 8 GiB, x86_64) running Ubuntu 24.04 LTS on an encrypted gp3
volume, started only for a run and stopped afterwards, managed through Systems Manager with no
inbound access. Its results carry no hostname, IP address, account or instance ID, user name or
path.

## Adoption metrics

`metrics/` holds hidane's public adoption numbers, one snapshot a day (UTC), collected from public
APIs only:

| Source | Numbers |
|---|---|
| GitHub: hidane-dev/hidane, hidane-dev/setup-hidane | Stars, forks, watchers |
| GitHub releases of hidane | Each release asset's download count, and the binary archives' total |
| crates.io: `hidane`, `hidane-core`, `hidane-proto`, `hidane-rules` | Total and 90-day downloads |
| npm: `hidane` and its `@hidane-dev/<platform>` packages | Downloads on the last day, 7 days and 30 days npm has counted, with the days each covers |
| pub.dev: `hidane` | 30-day downloads, likes, pub points |

Every number records the URL it was read from and when. A number no public API gives is `null`
with the reason, in every snapshot, rather than an estimate: the repositories and packages that
depend on hidane (GitHub has no API for them), the workflows that use setup-hidane (only an
authenticated, approximate code search could count them), pulls of `ghcr.io/hidane-dev/hidane`
(no API), installs from the Homebrew tap (Homebrew has no analytics for third-party taps), and
adopters (reserved for hidane's ADOPTERS list). A number that could not be loaded on a given day
(an error, a timeout) is `null` with the reason, the URL and the time of the attempt.

Reading them: release download counts include every download, hidane's own CI and the installers
and launchers that fetch the archives (Homebrew, the pub.dev launcher, setup-hidane); npm's
platform packages are optional dependencies of `hidane` and follow its count; npm's newest counted
day lags by a day or two. hidane collects no telemetry: these are the registries' public numbers,
nothing more.

## License

The results and adoption metrics in this repository are licensed under [CC BY 4.0](LICENSE):
use them freely, and credit "hidane canonical benchmark (hidane-dev/benchmark-results)" with a
link when you quote them. The metrics restate counts that GitHub, crates.io, npm and pub.dev
publish; each value names the API it came from. hidane itself is licensed under MIT OR Apache-2.0.

hidane is an independent open-source project, not affiliated with Google. Firebase and Cloud
Firestore are trademarks of Google LLC.
