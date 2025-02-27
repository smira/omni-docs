---
title: "Auto-assign roles to SAML users"
description: "A guide on how to assign Omni roles to SAML users automatically."
draft: false
weight: 40
---

This guide shows you how to configure your Omni instance, so that new users logging in
with SAML authentication are automatically assigned to a role based on their SAML role attributes.

Create the file `assign-operator-to-engineers-label.yaml` for the `SAMLLabelRule` resource, with the following content:

```yaml
metadata:
  namespace: default
  type: SAMLLabelRules.omni.sidero.dev
  id: assign-operator-to-engineers-label
spec:
  assignroleonregistration: Operator
  matchlabels:
    - saml.omni.sidero.dev/role/engineers
```

As an admin, create it on your Omni instance using `omnictl`:

```bash
omnictl apply -f assign-operator-to-engineers-label.yaml
```

This will create a resource that assigns the `Operator` role to any user that logs in with SAML
and has the SAML attribute `Role` with the value `engineers`.

Log in to Omni as a new SAML user with the SAML attribute with name `Role` and value `engineers`.

This will cause the user created on the Omni side to be labeled as `saml.omni.sidero.dev/role/engineers`.

This label will match the `SAMLLabelRule` resource we created above,
and the user will automatically be assigned the `Operator` role.

{{% alert title="Note" color="info" %}}
When there are multiple matches from different `SAMLLabelRule` resources,
the matched role with the highest access level will be assigned to the user.
{{% /alert %}}

{{% alert title="Warning" color="warning" %}}
This role assignment will **only work for the new users** logging in with SAML.

The SAML users who have already logged in to Omni at least once
**will not be matched** by the `SAMLLabelRule` resource and their roles will not be updated.
{{% /alert %}}

{{% alert title="Warning" color="warning" %}}
If the logged in SAML user is the **very first user** logging in to an Omni instance,
**it will not be matched** by the `SAMLLabelRule` resource
and always be assigned the `Admin` role.
{{% /alert %}}
