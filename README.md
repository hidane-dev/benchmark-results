# hidane benchmark results

The results of hidane's **canonical benchmark**: [hidane](https://github.com/hidane-dev/hidane)
and the official Cloud Firestore emulator, measured on the same dedicated machine, with the same
client, data, workload and method, every night (hidane's `main`) and for every release (its
published binary).

The numbers hidane's README and release notes quote come from the **release** results here, and
each quote names the result it comes from. Nothing here is a single best run: every metric keeps
its raw samples and their median, p95, minimum, maximum, mean and standard deviation, and
comparisons use medians.

## Layout

| Path | What |
|---|---|
| `environments/<name>-r<revision>.json` | The machine and its software for one environment revision: instance type, OS image, disk, Java, Node.js and firebase-tools versions, methodology version |
| `results/nightly/YYYY/MM/` | Nightly results: a `.json` and the `.md` rendered from it |
| `results/release/YYYY/MM/` | Release results |
| `index.json` | Every result, with its kind, time, environment revision, hidane version and commit, official emulator version, and whether its environment was stable |

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

## License

The results in this repository are licensed under [CC BY 4.0](LICENSE): use them freely, and
credit "hidane canonical benchmark (hidane-dev/benchmark-results)" with a link when you quote
them. hidane itself is licensed under MIT OR Apache-2.0.

hidane is an independent open-source project, not affiliated with Google. Firebase and Cloud
Firestore are trademarks of Google LLC.
