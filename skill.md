union isfuzzy=true traces, exceptions
| where timestamp > ago(2h)
| project timestamp, message = coalesce(
    tostring(column_ifexists("outerMessage", "")),
    tostring(column_ifexists("message", ""))
)
| order by timestamp desc
| take 50