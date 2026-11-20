traces
| where timestamp > ago(24h)
| where message contains "Error"
    or message contains "Exception"
    or message contains "Host"
    or message contains "Worker"
| project timestamp, severityLevel, message
| order by timestamp desc
| take 50