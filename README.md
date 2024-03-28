# Subsquid Worker CheckMK Monitoring Plugin

This plugin allows you to monitor Subsquid workers via the enterprise IT monitoring platform [CheckMK](https://checkmk.com/). It works using the [piggyback mechanism](https://docs.checkmk.com/latest/en/piggyback.html). This means that the plugin is installed on a central monitoring host and centrally gathers data for all deployed workers.

## Installation

First, ensure that you have installed the necessary distribution packages to create Python virtualenvs. For example, on Ubuntu, that would be:

```sh
sudo apt install python3-pip python3-venv
```

Clone the git repository, create a virtualenv and copy the script into a place where it will be found by the check_mk agent:

```sh
git clone https://github.com/cardinate/subsquid-checkmk.git
cd subsquid-checkmk
sudo python3 -m venv /usr/local/lib/check-subsquid-worker/
sudo /usr/local/lib/check-subsquid-worker/bin/pip install -r requirements.txt
sudo cp check-subsquid-worker /usr/lib/check_mk_agent/local/check-subsquid-worker
sudo chmod a+x /usr/lib/check_mk_agent/local/check-subsquid-worker
```

## Configuration

For each worker that you monitor, you create a file in `/var/lib/check-subsquid-worker` (create this directory if it does not exist). The file name must be equal to the hostname in CheckMK, and the content must be the public key/P2P address of the (`12D3…`) worker's RPC node.

Additionally, you can create a file `/etc/default/check-subsquid-worker` to configure the following environment variables that affect the behaviour of the check.

| Variable | Meaning | Default |
|----------|---------|---------|
| `SUBSQUID_WORKERS_DIR` | The directory where the worker configuration files are found | `/var/lib/check-subsquid-worker` |
| `SUBSQUID_PING_TIMEOUT` | Timeout for retrieving basic data via the ping endpoint | `30` |
| `SUBSQUID_CHUNKS_TIMEOUT` | Timeout for retrieving chunk allocations | `30`
| `SUBQSUID_METRICS_TIMEOUT` | Timeout for retrieving additional per-worker metrics | `30`

## Known issues and limitations

* The plugin works by scraping Subsquid's monitoring endpoints, which an be rather slow to respond. Consider using a rather large interval between checks to prevent issues.
* Traffic metrics (`responseBytes`) are currently not returned from the metrics endpoint.
