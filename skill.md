union traces, exceptions
| where timestamp > ago(30m)
| order by timestamp desc
| take 50