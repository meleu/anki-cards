# DevOps::GCP

## 5 traits of Cloud Computing

1. On demand self-service (no humans to get resources).
2. Broad network access (access anywhere).
3. Resource pooling (provider shares resources to customers).
4. Rapid elasticity (get more resources quickly).
5. Measured service (pay only what you consume).


## 3 main things defined in IAM

- who
- can do what
- on which resource


## Principle of least privilege

Each user should have only those privileges needed to do their jobs.


## Four ways to interact with GCP management layers

1. web based console
2. SDK & CLI tools
3. APIs
4. mobile app


## IAM: possible "who"

- Google account
- Google group
- Service account
- an entire G Suite or Cloud Identity domain


## IAM: how is the "can do what" defined?

By an IAM role


## IAM: types of roles

1. Primitive
2. Predefined
3. Custom


## IAM: primitive roles

- Owner
- Editor
- Viewer
- Billing administrator

**Warning:** when you have several prople working together on a project that contains sensitive data, primitive roles are probably not enough.

