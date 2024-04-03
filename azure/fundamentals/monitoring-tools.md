# 🕋 Monitoring tools
---

[[monitoring-tools#Azure Advisor|Azure Advisor]]
[[monitoring-tools#Azure Service Health|Azure Service Health]]
[[monitoring-tools#Azure Monitor|Azure Monitor]]
	[[monitoring-tools#Azure Log Analytics|Azure Log Analytics]]
	[[monitoring-tools#Azure Monitor Alerts|Azure Monitor Alerts]]
	[[monitoring-tools#Alert groups|Alert groups]]
	[[monitoring-tools#Application Insights|Application Insights]]

## Azure Advisor

- Gives out recommendations on how to improve setup and configuration of Azure resources based on its evaluation of the current state of your resources.
    - Helps in the optimization of your resource’s performance.
    - Gives recommended actions that you can take away, postpone, or dismiss.
- Recommendations are separated into five categories:
    - **Reliability.** Ensure continuity of your business-critical applications
    - **Security.** Detect threats and vulnerabilities.
    - **Performance.** Improve application speed.
    - **Operation excellence.** Help achieve efficiency in workflow performance.
    - **Cost.** Optimize and reduce your overall Azure spending.

---

## Azure Service Health

- Collection of Azure services that keeps track of the resources you deployed and provisioned and the overall health of Azure.
- Services involved:
    - **Azure Status**
        - Tracks status of Azure globally across all Azure regions.
    - **Service Health**
        - Provides a personalized view of status of the Azure regions you’re in and Azure services you’re using.
        - Best place to get information about planned maintenance, outages and other health advisories to the services that you’re specifically using.
        - Alerts and notifications can be configured to let you know once these health advisories start to circulate to users.
    - **Resource Health**
        - Provides a view on the health of your specific resources (specific VM instances, database servers, app functions, etc.)
        - Alerts and configurations based on availability changes.
- Historical alerts are available for viewing at any time.
    - Allows you to inspect simple anomalies that becomes a trend.

---

## Azure Monitor

- Platform for collecting data on your resources, visualizing the data that was uncovered, like a personal dashboard of data about your Azure resources.

### Azure Log Analytics

- Tool that enables you to write and run queries on the data gathered by Azure monitor.
    - Can run simple and complex queries, even advanced queries where you can perform statistical analysis and place data in a chart for visualization purposes.
- Has features that enables you to sort, filter and and analyze the records gathered by the query.

### Azure Monitor Alerts

- Automated way to stay informed when Azure monitor detects thresholds being crossed.
- Thresholds, alert conditions and notification acti are configurable.
- Used to monitor logs and will alert you when log events occured or monitor metrics and will alert you when metric thresholds are reached.
    - Example: Setup an alert that will notify you if one of your VMs exceeded 80% CPU capacity.
- Conditions are based on real time numeric values from your resources.

### Alert groups

- The above services all use alert groups
- Collection of notification and action preferences that you associate with one or more alerts
- Here’s where we determine who will be sent out with a notification.

### Application Insights

- Monitors web applications that are hosted either in Azure, on-prem or multi-cloud environments.
- Two ways to use Application Insights:
    - Through SDK available in most programming languages
    - Application Insights agent - application running inside your environment
- Monitors metrics like:
    - Hardware stats like CPU, memory and network usage
    - Numbers of requests, response times, failure rates
    - User and session counts
    - and many more
- Can also send dummy requests to check the status and monitor your application during periods of low activity.