# Demo script used for Elastic Daily Bytes S04E03 - Cloud - Elastic Agent Ingestion

![Cloud - ELastic Agent Ingestion](images/00-talk.png "Cloud - ELastic Agent Ingestion")

## Setup

Start a GCP VM.

On the machine, download the elastic-agent and auditbeat:

```sh
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.1.3-amd64.deb
curl -L -O https://artifacts.elastic.co/downloads/beats/auditbeat/auditbeat-8.1.3-amd64.deb
```

If already installed, remove them:

```sh
sudo systemctl stop elastic-agent
sudo systemctl stop auditbeat
sudo dpkg --purge elastic-agent
sudo dpkg --purge auditbeat
```

## Demo part

### Create a cluster

Open your cloud deployment in the [cloud console](https://cloud.elastic.co/deployments).
Create a new cluster.

![Cloud - ELastic Agent Ingestion](images/10-create.png "Cloud - ELastic Agent Ingestion")

Make sure to create a cluster with the integrations server and the enterprise search. It's proposed by default.

![Cloud - ELastic Agent Ingestion](images/11-integrations-server.png "Cloud - ELastic Agent Ingestion")

Highlight the fact that everything is deployed and managed for us within few clicks.
So we can easily start to ingest new data as we need to.

### Add new integrations

![Cloud - ELastic Agent Ingestion](images/20-add-integ.png "Cloud - ELastic Agent Ingestion")

Show the system integration:

![Cloud - ELastic Agent Ingestion](images/21-list-integ.png "Cloud - ELastic Agent Ingestion")

### Monitor the system

Add it:

![Cloud - ELastic Agent Ingestion](images/30-add-system.png "Cloud - ELastic Agent Ingestion")

Configure it:

![Cloud - ELastic Agent Ingestion](images/31-configure-system.png "Cloud - ELastic Agent Ingestion")

### Install the agent

![Cloud - ELastic Agent Ingestion](images/40-add-agent.png "Cloud - ELastic Agent Ingestion")

![Cloud - ELastic Agent Ingestion](images/41-config-agent.png "Cloud - ELastic Agent Ingestion")

And follow the guide:

```sh
# curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.1.3-amd64.deb
sudo dpkg -i elastic-agent-8.1.3-amd64.deb
```

It gives:

```txt
Selecting previously unselected package elastic-agent.
(Reading database ... 43909 files and directories currently installed.)
Preparing to unpack elastic-agent-8.1.3-amd64.deb ...
Unpacking elastic-agent (8.1.3) ...
Setting up elastic-agent (8.1.3) ...
Processing triggers for systemd (232-25+deb9u13) ...
```

### Enroll the agent

Copy from the Linux / macOS tab the url and the token:

* URL: `https://ed32a7ba14f1459fa2359402f293c600.fleet.europe-west1.gcp.cloud.es.io:443`
* TOKEN: `VnBISlg0QUJESjZONVQxcXUzUkg6Z2VHcTlHTmxSd1dWaHgxZW5BUkcyZw==`

```sh
sudo elastic-agent enroll --url=https://ed32a7ba14f1459fa2359402f293c600.fleet.europe-west1.gcp.cloud.es.io:443 --enrollment-token=VnBISlg0QUJESjZONVQxcXUzUkg6Z2VHcTlHTmxSd1dWaHgxZW5BUkcyZw==
```

It gives:

```txt
{"log.level":"info","@timestamp":"2022-04-25T08:30:44.320Z","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":455},"message":"Starting enrollment to URL: https://ed32a7ba14f1459fa2359402f293c600.fleet.europe-west1.gcp.cloud.es.io:443/","ecs.version":"1.6.0"}
{"log.level":"info","@timestamp":"2022-04-25T08:30:45.593Z","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":253},"message":"Elastic Agent might not be running; unable to trigger restart","ecs.version":"1.6.0"}
Successfully enrolled the Elastic Agent.
```

Enable and start the agent:

```sh
sudo systemctl enable elastic-agent 
sudo systemctl start elastic-agent
```

Open the "[Metrics System] Overview" asset.

![Cloud - ELastic Agent Ingestion](images/50-dashboard.png "Cloud - ELastic Agent Ingestion")

And click on the Host one:

![Cloud - ELastic Agent Ingestion](images/51-dashboard.png "Cloud - ELastic Agent Ingestion")

And open the Observability Application:

![Cloud - ELastic Agent Ingestion](images/52-obsvervability.png "Cloud - ELastic Agent Ingestion")

Start the injector:

```sh
./injector.sh
```

And look at the data.

### Add OsQuery

![Cloud - ELastic Agent Ingestion](images/60-osquery.png "Cloud - ELastic Agent Ingestion")

When deployed to the agents, you can start running commands on the host machines:

![Cloud - ELastic Agent Ingestion](images/61-osquery-run.png "Cloud - ELastic Agent Ingestion")

Run:

```sql
select * from etc_hosts
select * from os_version
```

### Add Auditbeat

That's the old way for doing this. Some of the beats components are not yet deployable through the elastic agent.

```sh
#curl -L -O https://artifacts.elastic.co/downloads/beats/auditbeat/auditbeat-8.1.3-amd64.deb
sudo dpkg -i auditbeat-8.1.3-amd64.deb
```

```sh
sudo vi /etc/auditbeat/auditbeat.yml
```

```yml
cloud.id: "Elastic_Daily_Bytes_S04:ZXVyb3BlLXdlc3QxLmdjcC5jbG91ZC5lcy5pbyQ0ODdjZTRlMDk2ZDI0NTEzYWMwNTkwNGU2ZWU2MjdhYyRkMTk0YTI1MTczYTc0YjQ5OWUzZDE2NTkzNzQ2ZTAzMg=="
cloud.auth: "elastic:<password>"
```

```sh
sudo auditbeat setup
sudo service auditbeat start
```

Check the data coming.

Open the Security App to show that data from observability is also used for security, out of the box.

![Cloud - ELastic Agent Ingestion](images/70-security-app.png "Cloud - ELastic Agent Ingestion")

### Webcrawler

If time allows.

Because we also deployed by default the enterprise search service, we can crawl a website like my personal blog post <https://david.pilato.fr>.

![Cloud - ELastic Agent Ingestion](images/80-webcrawler.png "Cloud - ELastic Agent Ingestion")

![Cloud - ELastic Agent Ingestion](images/81-blog.png "Cloud - ELastic Agent Ingestion")

![Cloud - ELastic Agent Ingestion](images/82-domain.png "Cloud - ELastic Agent Ingestion")

![Cloud - ELastic Agent Ingestion](images/83-start.png "Cloud - ELastic Agent Ingestion")

## Conclusion

Just start a cluster and you can immediately start to get data to it.
It could be as you seen previously by adding sample data or by deploying the agent on the hosts you want to monitor or secure.
It could be by crawling also the website.

The next session will be about making all those deployments even more automatic by using Terraform. Alex will cover this tomorrow.
