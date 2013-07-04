---
layout: page
category: Professional science
tagline: Managing multiple long running jobs on multiple clusters
tags: [development environment, science]
---
{% include JB/setup %}

The grad students in the CPRG have a number of scripts and best practices for monitoring their long running jobs on clusters, however, there's not a whole lot of automatic in place. The scripts are for convenience and at times automating trivial things like wrapping job commands in a script before submitting.

What I really want is this:
----
In the morning, I want a list of all my jobs to email me:
- their q status,
- their usage stats (snapshot of their line in `top`),
- the tail of their `.err` file

And upon dump file creation, I want an email notification.

Tools to create this setup:
------
`mail -s 'some subject' email@address < file` or `echo 'text' | mail -s 'some subject' email@address` will send emails from the command line.

