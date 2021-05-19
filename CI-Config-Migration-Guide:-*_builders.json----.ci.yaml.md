As of May 19, 2021, Flutter has migrated away from using `try_builders.json` and `prod_builders.json` to a single `.ci.yaml` file for CI configuration. Currently, they are more or less equivalent, with only a few differences.

The official source of truth for the new `.ci.yaml` is [scheduler.proto](https://github.com/flutter/cocoon/blob/master/scheduler/lib/models/scheduler.proto).

These two JSON files:

```
# dev/prod_builders.json
    {
      "name": "Linux analyze",
      "repo": "flutter",
      "task_name": "linux_analyze",
      "flaky": false
    },

# dev/try_builders.json
    {
      "name": "Linux analyze",
      "repo": "flutter",
      "task_name": "linux_analyze",
      "enabled": true
    },
```

Became:
```
# .ci.yaml 
 - name: linux_analyze
    builder: Linux analyze
    scheduler: luci
```