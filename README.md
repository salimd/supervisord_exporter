# Supervisor Exporter

The Supervisor Exporter is a simple Go application that collects process status information from the Supervisor process control system and exposes it as Prometheus metrics. This allows you to monitor the state of processes managed by Supervisor.

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Prometheus Metrics](#prometheus-metrics)
- [License](#license)

## Features

- Collects process status information from Supervisor.
- Exposes process state, exit status, and more as Prometheus metrics.
- Configurable via command line parameters.
- Provides a simple HTTP server for Prometheus to scrape metrics.
- Handles unreachable Supervisord XML-RPC endpoints gracefully.

## Getting Started

### Prerequisites

Before running the Supervisor Exporter, make sure you have the following prerequisites:

- Ensure that the Supervisord instance is configured to expose the XML-RPC interface. 

Follow these steps to enable the XML-RPC endpoint on Supervisord:

1. **Edit Supervisord Configuration:**

   Open the configuration file for Supervisord, typically located at `/etc/supervisord.conf` or a custom path specified during installation. You can use your preferred text editor to edit the file. For example:

   ```shell
   vi /etc/supervisord.conf
   ```
2. **Configure the XML-RPC Server:**

   Add the following lines to your Supervisord configuration file, if they are not already present, to configure the XML-RPC server:
   
   ```shell
   [inet_http_server]
   port = 127.0.0.1:9001
   ```

   * `port`: Specify the IP address and port where the XML-RPC server will listen. In the example above, it listens on 127.0.0.1:9001.

3. **Save and Restart Supervisord:**
   Save the configuration file and then restart Supervisord to apply the changes:

   ```shell
   supervisorctl reread
   supervisorctl update
   ```

Once you have configured and verified the XML-RPC endpoint on Supervisord, you can use the Supervisor Exporter to monitor your processes using Prometheus.

4. **Verify XML-RPC Endpoint:**

   To ensure the XML-RPC endpoint is working, you can test it by using a tool like curl or accessing it in your web browser:
   ```shell
   curl http://127.0.0.1:9001/RPC2
   ```

### Installation

1. Clone the repository:

   ```shell
   git clone https://github.com/salimd/supervisord_exporter.git
   ```

2. Build the application:
   ```shell
   go build
   ```

### Usage

To start the Supervisord Exporter, run the following command:

   ```shell
   ./supervisord_exporter
   ```

By default, the exporter will listen on port 9876 and use the Supervisor XML-RPC interface at `http://localhost:9001/RPC2`. You can change the defaults using command line parameters (see [Configuration](#configuration) section).

### Configuration

The Supervisord Exporter can be configured using command line parameters. Here are the available parameters:

* `-supervisord-url`: The URL of the Supervisord XML-RPC interface. Default is `http://localhost:9001/RPC2`
* `-web.listen-address`: The address and port where the exporter will listen for HTTP requests. Default is `:9876`
* `-web.telemetry-path`: Path under which to expose metrics. Default is `/metrics`
* `-version`: Print the version information and exit.

Example of custom configuration:

```shell
./supervisord_exporter -supervisord-url="http://example.com:9001/RPC2" -web.listen-address=":8080" -web.telemetry-path="/metrics"
```

### Prometheus Metrics

The Supervisord Exporter exposes the following Prometheus metric:

* `supervisor_process_info`: Gauge vector with labels for `name`, `group`, `state`, `start`, and `exit_status`. The `state` label will be `RUNNING` if the process is running, and `exit_status` will be `0` for a running process.
* `supervisord_up`: Gauge metric indicating the status of the connection to Supervisord (1 if up, 0 if down). If the Supervisord XML-RPC endpoint is unreachable, this metric will be set to 0, and there will be no supervisor_process_info metrics in the output.


Sample metric:
```
supervisor_process_info{exit_status="0",group="apache2",name="apache2",state="RUNNING"} 1
supervisord_up{} 1
```

### License

This project is licensed under the MIT License

