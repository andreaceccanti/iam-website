---
title: "JWT profiles support"
linkTitle: "JWT profiles support"
weight: 1
---

Starting with version 1.6.0, IAM introduces support for JWT _profiles_.

A JWT profile is a named set of rules that defines which information is
included in access tokens, id tokens,  userinfo and introspection responses
issued by IAM in an OAuth/OIDC message exchanges.

IAM allows to define a default profile that is used for all clients that are
not explicitly bound to one. This allows to use the same profile across
all clients in an organization.

It's also possible to define the profile to be used at client level, using
scopes, i.e. adding among the scope allowed for the client the name of the
profile the client should use. The scope, in this context, does not identify a
set of permissions but an encoding of authentication/authorization informations
in tokens and responses issued by IAM.

This mechanism enables integration of the same IAM instance with resources
relying on different profiles.

IAM currently supports four JWT profiles:

- the `iam` profile
- the `wlcg` WLCG profile 
- the `aarc` profile
- the `kc` profile


## Setting the default profile

The IAM JWT default profile is set using the `IAM_JWT_DEFAULT_PROFILE`
environment variable, e.g.:

```
IAM_JWT_DEFAULT_PROFILE=iam
```

The default profile will be used for all clients that do not explicitly and
correctly request the use of a profile using scopes. 

## Using scopes to select the JWT profile for a client

To select the profile used by a client, include one of the following scopes
in the list of scopes authorized for a client:

- `iam`, for the IAM profile
- `wlcg`, for the WLCG profile 
- `aarc`, for the AARC profile
- `kc`, for the Keycloak profile

Clients should only link to one profile. When multiple profiles are linked to a
client, IAM falls back to the default profile configured for the IAM instance.


## Using system scopes to control profile selection

MitreID **System Scopes** can be used to control access to profile selection.

The scopes `iam`, `wlcg` and `aarc` used to select the JWT profile are not available in IAM
by default; an administrator has to add all of them manually, as described in the
in [System Scopes documentation][system-scopes].

## Supported JWT profiles

### The IAM profile

This is the default profile for IAM.

With this profile:

- groups are encoded in the `groups` claim; all the groups the user is member
  of are included in the groups claim;

- authentication information (username, email, groups) is not by default
  included in access tokens; this behaviour can be changed by setting the
  `IAM_ACCESS_TOKEN_INCLUDE_AUTHN_INFO=true` environment variable;

- scope information is not by default included in access tokens; this behaviour
  can be changed by setting the `IAM_ACCESS_TOKEN_INCLUDE_SCOPE=true`
  environment variable;

- the `nbf` (not before) claim is not set in access tokens; this behaviour
  can be changed by setting the `IAM_ACCESS_TOKEN_INCLUDE_NBF=true`
  environment variable.

This profile is assigned to clients using the `iam` scope.

### The WLCG profile

This profile implements the [Common JWT Token profile][wlcg-profile].

In particular:

- the `scope` claim is always included in access tokens;
- groups are not included by default in access and ID tokens.

This profile is assigned to clients using the `wlcg` scope.

With this profile:

- groups are encoded in the `wlcg.groups` claim; all the non-optional groups the user is member
  of are included in the wlcg.groups claim;

- authentication information (name, preferred username and email) is not by default
  included in access tokens; this behaviour can be changed by setting the
  `IAM_ACCESS_TOKEN_INCLUDE_AUTHN_INFO=true` environment variable and by requesting
  * `email` scope to include the `email` claim in access tokens;
  * `profile` scope to include the `name` and `preferred_username` claims in access tokens;

- the `nbf` (not before) claim is not set in access tokens; this behaviour
  can be changed by setting the `IAM_ACCESS_TOKEN_INCLUDE_NBF=true`
  environment variable.


#### Requesting groups with the WLCG profile

When the WLCG profile is used, groups are not automatically included in access
tokens and ID tokens, but can be requested using the `wlcg.groups` scope
(which has to be added among the [System Scopes][system-scopes]),
following the rules described in the [WLCG
profile](https://github.com/WLCG-AuthZ-WG/CommonJWTProfile/blob/master/profile.md#scope-based-group-selection).

#### Optional groups

Optional groups are groups whose membership is only asserted in tokens on
explicit request coming from a user.

In order to configure a IAM group as an optional group,
add the `wlcg.optional-group` label to the group.


### The AARC profile

This profile encodes group membership information following the rules defined
by the [AARC G002][aarc-g002] profile document.

In particular:

- groups and organisation name are not included by default in access and ID tokens;
- organisation name can be requested using the `eduperson_scoped_affiliation` scope and it's encoded in the `eduperson_scoped_affiliation` claim;
- groups can be requested using the `eduperson_entitlement` scope and they're encoded as URN in the `eduperson_entitlement` claim.

All the mapping rules are described in the [White Paper for implementation mappings between SAML 2.0 and OpenID Connect in Research and Education](https://docs.google.com/document/d/1b-Mlet3Lq7qKLEf1BnHJ4nL1fq-vMe7fzpXyrq2wp08/edit).

This profile is assigned to clients using the `aarc` scope.

[system-scopes]: {{< ref "docs/reference/configuration/system-scopes" >}}
[wlcg-profile]: https://zenodo.org/record/3460258
[aarc-g002]: https://aarc-project.eu/guidelines/aarc-g002/

### The Keycloak profile

This profile allows the integration with a Keycloak environment.

With this profile:

* the `scope` claim is always included in access tokens;
* groups are always included in access and ID tokens; they are encoded in the `roles` claim.

Having **roles** claim instead of **groups** allows integration with the OIDC client - a conteinerised MISP deployment - which requires them to map IAM users.

This profile is assigned to clients using the `kc` scope.