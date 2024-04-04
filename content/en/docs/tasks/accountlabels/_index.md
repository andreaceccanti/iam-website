---
title: "IAM account lifecycle labels"
weight: 2
description: >
   Meaning of IAM account labels
---


{{% alert title="Important" color="warning" %}}

This only applies to users imported from the CERN HR DB

{{% /alert %}}

Indigo IAM communicates with the CERN HR database to automatically synchronise and update user credentials and authorisations within the system.  
IAM instances at CERN define a cron job that runs, for example, every twelve hours and synchronises user membership information from the CERN HR DB with that of IAM.

To enable the cron this configuration is required:
```
cern:
  experimentName: %%EXPERIMENT%%
  person-id-claim: cern_person_id
  task:
    enabled: true
    ## Cron schedule here include seconds as the first field, pay attention!
    cron-schedule: "0 0 */12 * * *"
```

Specific labels can be added to the user account at this time:

|    label name    |        label value     |                                             meaning                                                          |
|------------------|------------------------|--------------------------------------------------------------------------------------------------------------|
| cern_person_id   |    _id_                | a unique identifier assigned to each CERN employee and stored in the CERN HR DB                              |
| <br> <br> action           |    DISABLE_ACCOUNT <br> <br> RESTORE_ACCOUNT <br> <br> NO_ACTION     | added when CERN experiment membership is expired or not found; the IAM account is then disabled <br> <br> added when CERN experiment membership is restored but IAM account is inactive; the account is then restored <br> <br> added when the previous state does not change   |
| ignore           |                        | if present, the account is ignored during the identity and access management processes                       |
| skip-email-synch |                        | if present, email synchronization for that account is skipped                                                |
| <br> status           |    OK <br> <br> ERROR  | added when account is disabled, restored or when no action is performed <br> <br> added when cern_person_id misses or when there is an error contacting CERN HR DB api                                     |
| timestamp        |    _timestamp_         | the instant at which the cron job runs and the account is handled                                            |
| message          |    _message_           | an error or ignore message                                                                                   |

All these labels have the same prefix which is `hr.cern`.

The following graph shows how the labels evolve during the lifecycle of a CERN account:

![Account lifecycle labels](images/account-lifecycle-labels.png)