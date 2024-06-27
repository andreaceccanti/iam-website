---
title: "Registration & Enrollment"
linkTitle: "Registration & Enrollment"
weight: 6
---

IAM implements a basic registration service that requires the intervention
of an IAM admin. In fact, when users apply for membership in an
organization, administrators are asked to validate membership requests.


## Registration with external IdP

When an external OIDC or SAML IdP is used to authenticate users, IAM allows to configure:

- Whether users are required to authenticate through an external IdP and optionally
  which ones
- Which information must be retrieved from the IdP to fill the account creation form

This is done by creating a YAML file in `/indigo-iam/config`, for example
`/indigo-iam/config/application-registration.yaml`. When deploying IAM with a container,
a volume providing this file must be mapped into the container.

The contents in this file must be under the following hierarchy:

```yaml

iam:
  registration:
```


### Requiring external authentication

To require that users must authenticate through an external IdP, you need to set the
parameter `require-external-authentication=true`. You can also specify the type of external
IdP (`oidc` or `saml`) and require one specific issuer.

The following fragment is an example of authentication with the
(OIDC-based) CERN SSO required before being redirected to the registration page.
It also defines how information from identity tokens issued by CERN SSO is
mapped to IAM membership information

```yaml

iam:
  registration:
    require-external-authentication: true
    oidc-issuer: https://auth.cern.ch/auth/realms/cern
    authentication-type: oidc
```

### Filling information from IdP

The first time a user authenticates in an IAM instance, the account creation form will be displayed. It is possible to request
that some of the fields are filled with the value of an IdP attribute and to define that some of these fields are read-only,
i.e. that the value provided by the IdP cannot be changed.

To enable filling the creation form with values provided by the IdP, you need to create a YAML file in `/indigo-iam/config`, for example
`/indigo-iam/config/application-registration.yaml`. The contents should be something similar to:


```yaml
iam:
  registration:
    fields:
      email:
        read-only: false
        external-auth-attribute: email
      name:
        read-only: false
        external-auth-attribute: given_name
      surname:
        read-only: false
        external-auth-attribute: family_name
      username:
        read-only: false
        external-auth-attribute: preferred_username
```

`read-only` can be set to `true` if you want to prevent that the  value provided supplied by the ID is modified by the user.
**Note that if a field is defined as `read-only=true` and now value is not provided
by the IdP, it may result that the user cannot submit the account creation form if the field,
when it is required.**

`external-auth-attribue` must be the name of the IdP attribute, or token claim (when provided by SAML IdPs,
or OIDC Providers, respectively) to use for the mentioned account creation form field.

## User editable fields

Starting with version 1.6.0, IAM allows to limit which fields of the user profile are editable by users.

The default, backward-compatible settings that allow users to edit all their
profile fields are defined as follows:

```yaml

iam:
  user-profile:
    editable-fields:
      - email
      - name
      - picture
      - surname
```

To prevent modifications to any of the fields remove the field name from
`editable-fields` list.

External configuration can be managed by placing directives as shown above in a
[custom configuration
file][custom-config-file]

## Automatic enrollment trough SAML IdPs

In case of registration trough an external SAML Identity Provider, IAM offers
a flexible user enrollment flow, also without IAM admin intervention. The default IAM
behavior is that the user enrollment requires an administrator approval step.

In order to enable an automatic enrollment flow trough an external IdP, one
should set the following properties, under the `saml` hierarchy:

```yaml
saml:
  jit-account-provisioning:
    enabled: true
    # this will consider as trusted all the IdPs declared in your
    # application-saml.yml file
    trusted-idps: all
```

In order to directly declare the list of trusted SAML IdPs, a comma separated list of
entity IDs have to be set, e.g. `saml.jit-account-provisioning.trusted-idps=idp1,idp2,idp3`.

[custom-config-file]: {{< ref "/docs/reference/configuration/#overriding-default-configuration-templates" >}}
