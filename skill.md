union traces, exceptions
| where timestamp > ago(30m)
| where severityLevel >= 3
| project timestamp, message, outerMessage,
    innermostMessage, details
| order by timestamp desc
| take 30