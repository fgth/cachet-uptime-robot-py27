# Cachet Uptime Robot

Cachet is an open source status page system, this repository is a Python script that does two things, **first**, it reads the status of a page in UptimeRobot and updates a cachet component based on that status and **second**, it updates a metric with the historic uptime ratio from Uptime Robot.

This script was initially cloned from https://github.com/Axiacore/cachet-uptime-robot. Axiacore does the job for Python3, be thanks for that.
Fgth has downgraded it here for Python27.

**Prerequisites**
* pip install configparser
* pip install json
* pip install requests


**Component status: Uptime Robot (left), Cachet (right)**

* Not checked yet: Operational
* Up: Operational
* Seems down: Partial outage
* Down: Major outage

### Getting started 

To get started, you have to specify your Cachet and UptimeRobot settings in **config.ini**.

Rename config.ini.sample to config.ini, then:
```ini
[uptimeRobot]
UptimeRobotMainApiKey = your-api-key
UptimeRobotUrl = UptimeRobot-api-url (at this time : https://api.uptimerobot.com/v2/getMonitors)

[https://url.in.uptime.robot.com]
CachetApiKey = cachet-api-key
CachetUrl = https://status.mycompany.com
MetricId = 1
ComponentId = 1
```

* `CachetApiKey`:  Cachet API key.
* `https://url.in.uptime.robot.com`: This exact "monitor" url (=name) set in UptimeRobot (eg. http://mywebsite.com) 
* `CachetUrl`: URL of the API of the status page you want to show the site availability in.
* `MetricId`: Id of the Cachet metric with site availability.
* `ComponentId`: (Optional) Id of the component you want to update on each check.

### Usage

Register a cron that runs `update_status.py` every 5 minutes.

```bash
# Open cron file to edit.
crontab -e
```

Edit the crontab file and add this line:
```bash
*/5 * * * * python27 ~/path/update_status.py ~/path/config.ini
```

_Note that the path of the update_status.py & config.ini files may vary depending on the location you cloned the repository_

### Running manually

You can also update your Cachet data manually by running this:

```python
python27 update_status.py config.ini

>>> Updating monitor MySite. URL: http://mysite.co. ID: 12345678
>>> Metric created: {'data': {'calculated_value': 99.99, 'counter': 1, 'metric_id': 4, 'value': 99.99, 'created_at': '2016-08-12 08:23:10', 'updated_at': '2016-08-12 08:23:10', 'id': 99}}
```
### Troubleshooting
You can check your access to Cachet and Uptime Robot manually with curl, to validate your configuration.
Connect to Cachet and list metric information :
```bash
# List information for metric id=2
curl -X GET -H "Content-Type: application/json" -H "X-Cachet-Token: <your-api-key>" https://status.mycompany.com/api/v1/metrics/2
# List points from metric id=2
curl -X GET -H "Content-Type: application/json" -H "X-Cachet-Token: <your-api-key>" https://status.mycompany.com/api/v1/metrics/2/points
```
Connect to Uptime Robot and list a monitor with its response_times :
```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -H "cache-control: no-cache" --url "https://api.uptimerobot.com/v2/getMonitors" --data-urlencode "api_key=<your-monitor-api-key>" --data-urlencode "format=json" --data-urlencode "response_times=1"  --data-urlencode "uptime_ratio=30"
```
