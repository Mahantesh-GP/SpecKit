## Azure DevOps Discussion Posting

Build the complete clarification content as ONE SINGLE-LINE string.

Do not use multiline strings, here-strings, newline characters,
or separate command arguments.

Format:

[SDD CLARIFICATION] | SDD-Q1 | RESOLVED | Topic: <topic> | Question: <question> | Decision: <decision> | SDD-Q2 | RESOLVED | Topic: <topic> | Question: <question> | Decision: <decision> | SDD-Q3 | RESOLVED | Topic: <topic> | Question: <question> | Decision: <decision>

Pass the complete single-line string as one value to --discussion:

az boards work-item update --id <WORK_ITEM_ID> --discussion "<COMPLETE_SINGLE_LINE_CONTENT>" --organization "https://dev.azure.com/fnf"

Before executing, verify that the string contains every expected
SDD-Q identifier.

Do not report SUCCESS unless all expected clarification identifiers
were included in the value passed to --discussion.