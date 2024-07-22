---
title: "Client registration"
linkTitle: "Client registration"
weight: 7
---

IAM has the ability to enable or disable the client registration, and
to limit the registration to certain users.
The default behavior is that anyone can register a client, even
anonymous users.

To have control over the below properties using the environment
variables, please check the [Configuration][conf] section.

### Disable client registration

In order to totally disable the client registration, set

```yaml
iam:
  client-registration:
    enable: false 
```

The client registration is enabled by default in IAM.


### Specify who can register a client

In order to specify who can register a client, please set one
among _ANYONE_, _REGISTERED_USERS_ or _ADMINISTRATORS_ such
to limit to all, IAM users or administrators only the
client registration

```yaml
iam:
  client-registration:
    allow-for: <ANYONE|REGISTERED_USERS|ADMINISTRATORS> 
```

The client registration is enabled for anyone by default.


### Default settings of a new client

For any newly registered client, the default value of the related
access token, device code, ID token and refresh token lifetimes
can be set trough the following properties

```yaml
iam:
  client-registration:
    client-defaults:
      # IAM default is 1 hour
      default-access-token-validity-seconds: 3600
      # IAM default is 10 minutes
      default-device-code-validity-seconds: 600
      # IAM default is 10 minutes
      default-id-token-validity-seconds: 600
      # IAM default is 30 days
      default-refresh-token-validity-seconds: 2592000
```

The above values can be changed per client trough web interface
or API by an IAM administrator.


[conf]: ../#client-registration