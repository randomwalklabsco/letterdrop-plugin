---
schema_version: "1.1"
name: historical-records
description: A record that predates tracking is not reported as recent activity, and collectedDate is not presented as an activity date.
tags: [competitor-monitoring]
runs: 3
max_turns: 10
allowed_tools: [Skill]
---

Pull the competitor monitoring table. Which of these accounts has the most recent competitor signal, and when was it?
