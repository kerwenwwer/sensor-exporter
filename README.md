# sensor-exporter
Prometheus exporter for sensor data like temperature and fan speed.  

## Installation
### Release package
Download the release package
```bash
mv sensor-exporter /usr/local/bin 
mv sensor-exporter.service /etc/systemd/system/
systemctl daemon-reload
systemctl start sensor-exporter.service
```

### Build from source
**Please install golang 1.16** 

Install dep.
```bash
sudo apt install -y libsensors4-dev
git clone https://github.com/kerwenwwer/sensor-exporter.git
cd sensor-exporter/sensor-exporter
go build && mv sensor-exporter /usr/loca/bin/
```

## Usage
Run both lm-sensors and hddtemp
```bash
sensor-exporters --all
```
Run only one of its
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


## Troubleshooting

### About libsensors4 and libsensors5
If your distribuction are using libsensor5 (e.g. Ubuntu 20.04), note that ``libsensors.so.4`` are not in the same directory, and by default the system are trying find the lib in ``/usr/lib/x86_64-linux-gnu``, so you will see:
```bash
libsensors.so.4: cannot open shared object file: No such file or directory
```
the easy way to slove this problem is 
```bash
sudo ln -rs  -- /usr/lib/x86_64-linux-gnu/libsensors.so.5 /usr/lib/x86_64-linux-gnu/libsensors.so.4
```