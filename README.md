# Spec: Secret References v0.0.1

A set of rules for safely and securely keep track of secrets referenced in Uqido repositories through the BitWarden Password and Secret Manager.

## Dependencies

- https://github.com/Uqido/spec-specification-repository/tree/0.0.1

## Adoption Impact

This specification defines the standardized syntax and semantics for managing secret references—used throughout the development process—in Uqido repositories, in order to:
- Version secret references directly within the repository while keeping actual secrets secure, avoiding reliance on fragile external sources like credential documents
- Enable autonomous access to secrets for authorized users, without requiring DevOps intervention
- Place secret references precisely where they're needed—such as alongside usernames in README files, in .env files, etc
- Support seamless secret rotation without requiring changes to the codebase
- Establish a single source of truth, shared across the company and accessible to guests or external collaborators
- Allow both humans and AI systems to track secret references throughout the codebase

## Normative Content

This specification applies only to secrets used during the *development process* of the software components hosted in the repository.  
Developers **should** not store secrets related to end users or test users in repositories.

All explicit references to secrets **shall** be stored as BitWarden URLs.  
This URL **shall** point directly to the given secret item containing the secret it refers to.

Developers **should** not describe what a secret URL points to. Instead, the URL should be placed exactly where the plain secret value would normally appear:

*OK*:
```
In order to access this service, you should login with
username: test-user
password: https://vault.bitwarden.com/#/vault?organizationId=...
```

*NOT OK*:
```
In order to access this service, you should login with the user 'test-user'
you can find the password at https://vault.bitwarden.com/#/vault?organizationId=...
```

When a secret is part of a machine-readable file—such as a .env file, a Docker Compose file, etc.—the file **should** not contain the secret URL directly, unless it is explicitly legit to do so.  
Instead, a versioned copy of the file **should** be created with a recognizable postfix.
The local file **shall** be added to .gitignore and **will** contain the local, unsecured secret values:

*OK*:
```
.env.example content:
SERVICE_SECRET=https://vault.bitwarden.com/#/vault?organizationId=...
.env content (git ignored):
SERVICE_SECRET=some_credential
```

*NOT OK*:
```
.env content:
SERVICE_SECRET=https://vault.bitwarden.com/#/vault?organizationId=...
```

Missing secrets **should** be signaled by sensible errors whenever requested by the repository tool chain.  
