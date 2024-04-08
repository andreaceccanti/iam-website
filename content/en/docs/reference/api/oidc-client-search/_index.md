---
title: "OpenID Connect client search"
linkTitle: "OpenID Connect client search"
---

IAM supports searching for clients based on user-specified filters via a ``GET`` on the ``/iam/api/search/clients`` endpoint.

| Name | Values | Default | Description |
|:------------:|-------------|-------------|-------------|
| `search`<br/>**string**| A string up to 256 characters. | ``null`` | What to search. |
| `SearchType`<br/>**string**| One of: ``name``, ``contacts``, ``scope``, ``grantType``, ``redirectUri`` | ``name``, search is performed both on client name and clientId. | Where to search for the ``search`` string. |
| `drOnly`<br/>**boolean**| ``true`` or ``false`` | ``false`` | Dynamically registered only. |
| `sortProperties`<br/>**string**| One of: ``clientDescription``, ``reuseRefreshTokens``, ``dynamicallyRegistered``, ``allowIntrospection``, ``idTokenValiditySeconds``, ``clientId``, ``clientSecret``, ``accessTokenValiditySeconds``, ``refreshTokenValiditySeconds``, ``applicationType``, ``clientName``, ``tokenEndpointAuthMethod``, ``subjectType``, ``logoUri``, ``policyUri``, ``clientUri``, ``tosUri``, ``jwksUri``, ``jwks``, ``sectorIdentifierUri``, ``requestObjectSigningAlg``, ``userInfoSignedResponseAlg``, ``userInfoEncryptedResponseAlg``, ``userInfoEncryptedResponseEnc``, ``idTokenSignedResponseAlg``, ``idTokenEncryptedResponseAlg``, ``idTokenEncryptedResponseEnc``, ``tokenEndpointAuthSigningAlg``, ``defaultMaxAge``, ``requireAuthTime``, ``createdAt``, ``initiateLoginUri``, ``clearAccessTokensOnRefresh``, ``softwareStatement``, ``codeChallengeMethod``, ``softwareId``, ``softwareVersion``, ``deviceCodeValiditySeconds``, ``clientLastUsed.lastUsed`` | ``clientId`` | The property of the client used to sort the results. |
| `sortDirection`<br/>**string**| ``ASC`` or ``DESC`` | ``ASC`` | Ascending or descending. |
| `startIndex`<br/>**integer**| [1-n] | 1 | The 1-based index of the first query result. |
| `count`<br/>**integer**| [0-n] | 10 | The number of results to collect. |
