Google Cloud GitHub CI/CD template
==================================

This is my minimal template for setting up CI/CD early in a Google
Cloud project. Simply, I clone this repo, point it somewhere else,
add the configuration referred to in
[DEPLOYMENT.md](./infra/DEPLOYMENT.md),
and build on top of it.

FAQ
---

### How can I deploy to multiple environments (`DEV`, `STG`, `PROD`)?

Just create `dev.tfvars`, `stg.tfvars` and `prod.tfvars`. Or come up
with a more complex setup. But in my humble opinion, if it passes the
tests, it belongs to `prod`. Make sure your test smartly and use the
types, Luke!

### The GitHub action is failing in this repo, does that mean it doesn't work?

No. It means I did not

- add configuration files
- run `terraform apply`

before pushing to this repo. I did that in another project that shares
history with this one, and it works. But there's no point in paying
for infrastructure to be deployed if I'm just creating a template.
