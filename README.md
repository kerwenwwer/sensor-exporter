# sensor-exporter
Prometheus exporter for sensor data like temperature and fan speed.  

## Installation

## Usage
Run both lm-sensors and hddtemp
```bash
sensor-exporters --all
```
Run only one of it
```bash
sensor-exporter --runtime=lmsensor
```

## Inputs

lm-sensors (http://www.lm-sensors.org) to get metrics like CPU/MB temp and
CPU/Chassis fan speed.  You'll likely need to install lm-sensor dev package
(libsensors4-dev on my Ubuntu 14 system) in order to build the dependant
package github.com/md14454/gosensors.

hddtemp (http://www.guzu.net/linux/hddtemp.php) to get HDD temperature from
SMART data.  Since hddtemp must run as root to collect this data, rather than
call it directly we expect the user to run it in daemon mode with its -d flag.
Then we connect to a port it listens on to fetch the data.

```bash
$ systemctl edit hddtemp.service
--------------------------------------------------------------------------------
[Unit]
Description=hddtemp
After=syslog.target

[Service]
Type=simple
User=root
ExecStart=
ExecStart=/usr/sbin/hddtemp --daemon /dev/sda /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg --listen=127.0.0.1

[Install]
WantedBy=multi-user.target
```

## Dashboard

See https://grafana.net/dashboards/237 for an example dashboard.  This is probably
way more than what you want, just mine the bits that are of interest and incorporate
them into your general system health dashboard.
