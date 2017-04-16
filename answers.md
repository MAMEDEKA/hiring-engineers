# Eiji Kawakita(DataDog)

## Level 0 (optional) - Setup an Ubuntu VM
- Ubuntu node
`$ vagrant init hashicorp/precise64`
`$ vagrant init bento/ubuntu-16.04`
 `$ vi Vagrantfile`

> config.vm.hostname = "wu01.default"
` $ vagrant up`

## Level 1 - Collecting your Data
- Sign up for Datadog (use "Datadog Recruiting Candidate" in the "Company" field), get the Agent reporting metrics from your local machine.

**Done**
![](https://i.imgur.com/P0TBSwA.jpg)


  - Cleate Account
    - https://www.datadoghq.com/
    - age@daionet.gr.jp


- Bonus question: In your own words, what is the Agent?

It gathers information system and aprication as metric. Agent reports metric to datadog system where they regist in Agent setting. 

- Add tags in the Agent config file and show us a screenshot of your host and its tags on the Host Map page in Datadog.

`$ sudo vi /etc/dd-agent/datadog.conf`
> tags: age,agedb

`$ sudo /etc/init.d/datadog-agent restart`
I add age and agedb tag.

- Install a database on your machine (MongoDB, MySQL, or PostgreSQL) and then install the respective Datadog integration for that database.
MySQL
> -> Integration-> Integration-> MySQL.
Account set

`$ sudo cp /etc/dd-agent/conf.d/mysql.yaml.example /etc/dd-agent/conf.d/mysql.yaml`
`$ vi /etc/dd-agent/conf.d/mysql.yaml`

``` mysql.yaml
instances:
  - server: localhost
    user: datadog
    pass: ********
    # port: 3306             # Optional
    # sock: /path/to/sock    # Connect via Unix Socket
    # defaults_file: my.cnf  # Alternate configuration mechanism
    # connect_timeout: None  # Optional integer seconds
    tags:                  # Optional
       - agedbmysql
       - optional_tag2
    options:               # Optional
       replication: false
```
`$ sudo /etc/init.d/datadog-agent restart`


- Write a custom Agent check that samples a random value. Call this new metric: test.support.random

Create "random_check.py" in /opt/datadog-agent/agent/checks.d.


`$ sudo chmod dd-agent:dd-agent random_check.py`

```
# Import
from checks import AgentCheck
import random

class RandomCheck(AgentCheck):
  def check(self, instance):
    self.gauge('test.support.random', random.random())
```

Create "random_check.yaml" in /etc/dd-agent/conf.d.

sudo vi random_check.yaml
sudo chown dd-agent:dd-agent random_check.yaml 

```
# This check takes no initial configuration
init_config:

instances:
    [{}]
```

Restart
`$ sudo /etc/init.d/datadog-agent restart`


## Level 2 - Visualizing your Data
- Since your database integration is reporting now, clone your database integration dashboard and add additional database metrics to it as well as your test.support.random metric from the custom Agent check.

Clone "Custom Metrics - mysql
->Menu, "Clone Dashboard"

![](https://i.imgur.com/7jtVNme.jpg)

![](https://i.imgur.com/2XM9NJy.jpg)

- Bonus question: What is the difference between a timeboard and a screenboard?

TimeBoard is showing graph at same time. ScreenBoards is more customizable. 

- Take a snapshot of your test.support.random graph and draw a box around a section that shows it going above 0.90. Make sure this snapshot is sent to your email by using the @notification

![](https://i.imgur.com/RaptTda.jpg)

![](https://i.imgur.com/zwXUEQs.jpg)

![](https://i.imgur.com/kQS6d0A.jpg)


"Add Alert"

"Alert threshold" is "0.9"


## Level 3 - Alerting on your Data
- Since you've already caught your test metric going above 0.90 once, you don't want to have to continually watch this dashboard to be alerted when it goes above 0.90 again. So let's make life easier by creating a monitor.

Chenge "dualing the last" time to longer time(For example 24Hour)

- Set up a monitor on this metric that alerts you when it goes above 0.90 at least once during the last 5 minutes

![](https://i.imgur.com/AyLVFEH.jpg)

- Bonus points: Make it a multi-alert by host so that you won't have to recreate it if your infrastructure scales up.

"Advance..." Option set

![](https://i.imgur.com/pw4U0ey.jpg)

- Give it a descriptive monitor name and message (it might be worth it to include the link to your previously created dashboard in the message). Make sure that the monitor will notify you via email.

I add "Host multied mail" 

![](https://i.imgur.com/qDiUhQU.jpg)

![](https://i.imgur.com/uWFckK5.jpg)

- This monitor should alert you within 15 minutes. So when it does, take a screenshot of the email that it sends you.

![](https://i.imgur.com/uXPPfkN.jpg)

- Bonus: Since this monitor is going to alert pretty often, you don't want to be alerted when you are out of the office. Set up a scheduled downtime for this monitor that silences it from 7pm to 9am daily. Make sure that your email is notified when you schedule the downtime and take a screenshot of that notification.

Monitor's "Manage Down time" ->"Schedule Downtime".

Set 7pm to 9am, Select "Schedule"'s "Repeat this downtime."
![](https://i.imgur.com/oohia5I.jpg)

