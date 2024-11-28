<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/phpstreamserver/.github/refs/heads/main/assets/phpss_metrics_light.svg">
    <img alt="PHPStreamServer logo" align="center" width="70%" src="https://raw.githubusercontent.com/phpstreamserver/.github/refs/heads/main/assets/phpss_metrics_dark.svg">
  </picture>
</p>

# HTTP Server Plugin for PHPStreamServer
![PHP >=8.2](https://img.shields.io/badge/PHP->=8.2-777bb3.svg)
![Version](https://img.shields.io/github/v/tag/phpstreamserver/phpstreamserver?label=Version&filter=v*.*.*&sort=semver&color=374151)

The Metrics Plugin for **PHPStreamServer** extends the core functionality by providing integration with Prometheus,
providing an endpoint for collecting and exposing application metrics.  
It provides some default metrics to monitor and allows users to define custom metrics as needed.

## Features:
 - Provides counter, gauge, histogram and summary metrics.
 - User defined metrics.

## Getting started
### Install composer packages
```bash
$ composer require phpstreamserver/core phpstreamserver/metrics
```

### Configure server
Here is an example of a simple server configuration with metrics.  
After startup, metrics are available at http://127.0.0.1:8081/metrics

```php
// server.php

use PHPStreamServer\Core\Plugin\Supervisor\WorkerProcess;
use PHPStreamServer\Core\Server;
use PHPStreamServer\Plugin\Metrics\MetricsPlugin;
use PHPStreamServer\Plugin\Metrics\RegistryInterface;
use Revolt\EventLoop;

$server = new Server();

$server->addPlugin(
    new MetricsPlugin(listen: '0.0.0.0:8081'),
);

$server->addWorker(
    new WorkerProcess(
        name: 'Metrics test',
        count: 2,
        onStart: function (WorkerProcess $worker): void {
            $registry = $worker->container->getService(RegistryInterface::class);
            $counter = $registry->registerCounter(namespace: 'test', name: 'ticks', help: 'Demonsration');

            EventLoop::repeat(1, function () use ($counter) {
                $counter->inc();
            });
        },
    ),
);

exit($server->run());
```

### Run
```bash
$ php server.php start
```
