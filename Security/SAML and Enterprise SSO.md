---
id: a1b2c3d4-1128-4000-8000-000000000128
title: SAML and Enterprise SSO
language: markdown
tags: [security, saml, sso, authentication]
selection: null
isPinned: false
timestamp: 1781500001128
---

**Links**: [[OAuth and Authentication Protocols]] | [[OAuth 2.0 in Practice]] | [[JSON Web Tokens]] | [[Web Security]] | [[Zero Trust Architecture]] | [[TLS 1.3 Deep Dive]]


# SAML and Enterprise SSO

SAML (Security Assertion Markup Language) is an XML-based enterprise SSO protocol. It enables users to authenticate once with an Identity Provider (IdP) and access multiple Service Providers (SPs).

## SAML vs OIDC

| Aspect | SAML | OIDC |
|--------|------|------|
| Format | XML | JSON |
| Protocol | SOAP, HTTP Redirect/Post | REST, HTTP |
| Token | SAML Assertion | JWT |
| Use case | Enterprise SSO | Consumer apps, mobile |
| Complexity | High (schema, signatures, XML) | Low |
| Mobile support | Poor | Excellent |
| Session management | IdP-initiated + SP-initiated | Similar |

## SAML Flow (SP-Initiated)

```
User → Service Provider (SP)
          │
          │ Generate AuthnRequest
          ↓
     Identity Provider (IdP)
          │
          │ Authenticate user (password, MFA, cert)
          │ Generate SAML Assertion (XML, signed)
          ↓
User → Service Provider (SP)
          │
          │ Validate assertion (signature, audience, expiry)
          │ Create local session
          ↓
     Access granted
```

## SAML Assertion (XML)

```xml
<saml:Assertion xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
                ID="_abc123" IssueInstant="2026-06-13T10:00:00Z">
  <saml:Issuer>https://idp.example.com</saml:Issuer>
  <ds:Signature>
    <!-- XML Signature over the assertion -->
  </ds:Signature>
  <saml:Subject>
    <saml:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress">
      alice@example.com
    </saml:NameID>
    <saml:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
      <saml:SubjectConfirmationData NotOnOrAfter="2026-06-13T10:10:00Z"
                                     Recipient="https://sp.example.com/acs" />
    </saml:SubjectConfirmation>
  </saml:Subject>
  <saml:Conditions NotBefore="2026-06-13T10:00:00Z"
                   NotOnOrAfter="2026-06-13T10:10:00Z">
    <saml:AudienceRestriction>
      <saml:Audience>https://sp.example.com</saml:Audience>
    </saml:AudienceRestriction>
  </saml:Conditions>
  <saml:AttributeStatement>
    <saml:Attribute Name="email">
      <saml:AttributeValue>alice@example.com</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="roles">
      <saml:AttributeValue>admin</saml:AttributeValue>
      <saml:AttributeValue>user</saml:AttributeValue>
    </saml:Attribute>
  </saml:AttributeStatement>
</saml:Assertion>
```

## IdP-Initiated SSO

```
User → IdP (already authenticated)
          │
          | User selects an application from portal
          │
          │ IdP generates assertion, POSTs to SP's ACS URL
          ↓
     Service Provider (SP) → Access granted
```

## Metadata Exchange

SP and IdP exchange XML metadata for configuration:

```xml
<EntityDescriptor entityID="https://sp.example.com">
  <SPSSODescriptor>
    <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
                              Location="https://sp.example.com/saml/acs" />
  </SPSSODescriptor>
</EntityDescriptor>
```

## Implementation (Python with PySAML2)

```python
from saml2 import BINDING_HTTP_POST
from saml2.client import Saml2Client
from saml2.config import SPConfig

def create_saml_client():
    config = SPConfig()
    config.load({
        "entityid": "https://sp.example.com",
        "service": {
            "sp": {
                "endpoints": {
                    "assertion_consumer_service": [
                        ("https://sp.example.com/saml/acs", BINDING_HTTP_POST)
                    ],
                },
                "required_attributes": ["email"],
                "optional_attributes": ["roles"],
                "name_id_format": "urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress",
            }
        },
        "metadata": {
            "remote": [{"url": "https://idp.example.com/metadata"}]
        },
        "key_file": "sp.key",
        "cert_file": "sp.crt",
    })
    return Saml2Client(config)

# Create AuthnRequest
client = create_saml_client()
req_id, request = client.create_authn_request(
    destination="https://idp.example.com/sso",
    binding=BINDING_HTTP_POST
)

# Redirect user to IdP
redirect_url = client.apply_binding(BINDING_HTTP_POST, request)
```

## Key Differences from OAuth

| Feature | SAML | OAuth 2.0 / OIDC |
|---------|------|-------------------|
| Primary use | Enterprise SSO | API authorization |
| User info | In assertion XML | ID token JWT + UserInfo endpoint |
| Sign-out | SLO (Single Logout) protocol | RP-initiated logout |
| Session | IdP manages session | Client manages tokens |
| Mobile | Poor (HTTP-POST binding) | Excellent (PKCE) |

## Popular IdP Providers

| Provider | Type | Features |
|----------|------|----------|
| Okta | Cloud | Universal Directory, MFA, lifecycle |
| Azure AD | Cloud | Microsoft 365 integration |
| OneLogin | Cloud | User provisioning, directory sync |
| Keycloak | Open source | Self-hosted, OIDC + SAML |
| Ping Identity | Enterprise | On-prem + cloud, federation |

**Links**: [[OAuth and Authentication Protocols]] | [[OAuth 2.0 in Practice]] | [[JSON Web Tokens]] | [[API Security]] | [[Web Security]]
