# opsworks_delayed_job

This wrapper provides support custom /etc/hosts entries

- [Delayed Job](#Delayed-Job)
- [Host Entries](#Host-Entries)

**IMPORTANT: Changes to these files will NOT take effect within opsworks until the cache has been cleared. To refresh the cache, go to Opsworks > Stacks > Deployments and hit "Run Command". From the command dropdown, select "Update Custom Cookbooks". Then run it with "Update Dependencies".**

## Delayed Job

These recipes set up an [AWS OpsWorks](http://aws.amazon.com/opsworks/) instance to run [delayed_job](https://github.com/collectiveidea/delayed_job) workers for a Rails application.

### Delayed Job Configuration Examples

By default, **4** delayed_job workers will be configured. To customize this, you may override the `delayed_job.pool_size` attribute in your stack's custom JSON:

```JSON
{
  "delayed_job": { "pool_size": 6 }
}
```

Specify an alternate `delayed_job` script location by overriding the `delayed_job.path_to_script` attribute in your stack's custom JSON. The default, `bin` was added in Rails 4. For pre-Rails 4 projects, set it to `script`:

```JSON
{
    "delayed_job": { "pool_size": 3, "path_to_script": "script" }
}
```

By default, workers will read from all queues. You can also specify custom `queues` parameters by adding attributes corresponding to each instance name and worker process. You can set a specific value for `--sleep-delay` seconds with a `sleep_delay` parameter. A special `default` pool can be specified to supply configuration for instances that don't otherwise appear in the JSON:

```JSON
{
  "delayed_job": {
    "YOUR_APP_NAME": {
      "pools": {
        "worker1": {
          "0": { "queues": "highpriority,images" },
          "1": { "queues": "emails" },
          "2": { "queues": "bidding", "sleep_delay": 1 }
        },
        "default": {
          "0": { "queues": "emails" },
          "1": { "queues": "emails" },
          "2": { "queues": "emails" }
        }
      }
    }
  }
}
```


### OpsWorks Set-Up

Create a custom layer for the delayed_job instances. Add the `AWS-OpsWorks-Rails-App-Server` security group to the layer, so that the instances have the usual cache and database access. Then, assign this cookbook's custom chef recipes to OpsWorks events as follows:

* **Setup**: `opsworks_delayed_job::setup`
* **Configure**: `opsworks_delayed_job::configure`
* **Deploy**: `opsworks_delayed_job::deploy`
* **Undeploy**: `opsworks_delayed_job::undeploy`
* **Shutdown**: `opsworks_delayed_job::stop`

## Host Entries

Additional /etc/hosts entries can be configured by defining the following JSON attributes:

```
{
    "opsworks": {
        "instance": {
            "custom_dns_entries": [
                {
                    "ip": "xx.x.xxx.xx",
                    "dns_names": "xxxxxxx.com  xxxx.xxxxxxxx.com xxxxxx.com"
                }
            ]
        }
    }
}
```
