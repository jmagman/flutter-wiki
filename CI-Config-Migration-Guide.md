As of May 19, 2021, Flutter has migrated away from using `try_builders.json` and `prod_builders.json` to a single `.ci.yaml` file for CI configuration. Currently, they are more or less equivalent, with only a few differences.

The official source of truth for the new `.ci.yaml` is [scheduler.proto](https://github.com/flutter/cocoon/blob/master/scheduler/lib/models/scheduler.proto). It specifies default values for fields that have them.

A sample, minimal migration occurred in [#82870](https://github.com/flutter/flutter/pull/82870/files). In that change, these JSON files:

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

## Field Mappings

What follows are mappings from the field name in the *_builders.json file to the corresponding field in `.ci.yaml`.

### try_builders.json

 - `name` -> `builder`
 - `repo` is unused in `.ci.yaml`.
 - `task_name` -> `name`
 - `enabled` -> `presubmit` (defaults to true)
 - `run_if` -> `run_if`

### prod_builders.json
 - `name` -> `builder`
 - `repo` is unused in `.ci.yaml`.
 - `task_name` -> `name`
 - `flaky` -> `bringup` (defaults to false)
