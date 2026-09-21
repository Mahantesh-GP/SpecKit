## Comment Format

Build the ADO Discussion comment as HTML (not Markdown).
The ADO Comments API renders HTML only.

Use this HTML structure:

<h1>SDD Clarification</h1>
<p><strong>Source:</strong> Spec Kit<br>
<strong>ADO Work Item:</strong> <WORK_ITEM_ID></p>
<hr>

<h2>SDD-Q1 — RESOLVED</h2>
<p><strong>Topic:</strong> <topic></p>
<p><strong>Question:</strong><br><question></p>
<p><strong>Decision:</strong><br><decision></p>
<hr>

<h2>SDD-Q2 — OUTSTANDING</h2>
<p><strong>Topic:</strong> <topic></p>
<p><strong>Question:</strong><br><question></p>
<p><strong>Decision:</strong><br>Awaiting business clarification.</p>
<hr>

<p><strong>Summary:</strong> <resolved-count> Resolved | <outstanding-count> Outstanding</p>