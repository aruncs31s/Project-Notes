---
id: Projects
tags:
  - projects
cssclasses:
  - wide-page
dg-publish: true
---
# 🧭 Projects Dashboard

Welcome to your central Project Hub — an overview of all projects, tasks, and notes.

***
##  Active Projects

```dataview
table
    Status,
    length(file.tasks) as "Tasks",
    sum(file.tasks.completed) as "Done",
    file.mtime as "Last Updated"
from #projects
where Status != "Completed"
Limit 5
sort file.mtime desc
```


### Tasks
```tasks
not done
limit 10
```

---

## Completed Projects
```dataview
table date(file.mtime) as "Completed On"
from #projects
where Status = "Completed"
sort file.mtime desc
```

### Tasks

```tasks
done
limit 10
```

---

## Upcomming Tasks

```dataview
task
from "Projects"
where !completed and due and due <= (date(today) + dur(7 days))
sort due asc
```
