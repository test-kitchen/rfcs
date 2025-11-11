# Decouple chef Provisioner from Core Test Kitchen

---

| **RFC** | **Author**                              | **Status** | **Type**     |
|---------|-----------------------------------------|------------|--------------|
| 0001    | Corey Hemminger <hemminger@hotmail.com> | Aproved    | Major Change |

---

## Summary

This RFC proposes removing the chef provisioner from the core test-kitchen gem and creating three separate plugin gems:

- kitchen-chef-enterprise: Official plugin maintained by Progress Chef.
- kitchen-omnitruck-chef: Community-maintained plugin preserving current behavior with deprecation warnings.
- kitchen-cinc: Community-maintained plugin for Cinc users.

This change improves maintainability, clarifies ownership, and supports future evolution of both Progress Chef and community-driven workflows.

---

## Motivation

The chef provisioner is currently tightly coupled with the core test-kitchen gem. This creates challenges for:

- Progress Chef: Maintaining licensing and support boundaries.
- Community: Supporting alternative distributions like Cinc.
- Contributors: Navigating diverging goals between Progress Chef and community users.

By decoupling the provisioner, we enable:

- Independent evolution of provisioners.
- Clear maintainership and support boundaries.
- Easier contribution and upstream sync between forks.

---

## Proposal

1. Remove chef Provisioner from Core
   - Extract the current chef provisioner into a standalone gem: kitchen-chef-enterprise.
   - Maintained by Progress Chef, supporting licensed Chef Infra usage.
2. Create kitchen-omnitruck-chef Plugin
   - Clone the current provisioner code into a new gem: kitchen-omnitruck-chef.
   - Add deprecation warnings for Omnitruck-based downloads.
   - Warn users that Omnitruck will be shut down in 2026.
   - Recommend migration to kitchen-chef-enterprise or kitchen-cinc.
3. Create kitchen-cinc Plugin
   - Community-maintained plugin for Cinc users.
   - Mirrors functionality of chef but uses Cinc downloads and tooling.
   - Use cinc_(name) naming scheme for provioners to replace chef_infra, chef_zeo, etc...
4. Update Default Behavior Post-Omnitruck Shutdown
   - After Omnitruck is shut down:
     - Community test-kitchen will default to kitchen-cinc.
     - Progress Chef will maintain a fork defaulting to kitchen-chef-enterprise.

This allows both ecosystems to co-exist without conflict.

---

## Benefits

- Modularization: Easier maintenance and testing of provisioners.
- Clear Ownership: Progress Chef owns kitchen-chef-enterprise; community owns others.
- Future-Proofing: Avoids breakage when Omnitruck is shut down.
- Contribution Flow: Simplifies upstream/downstream sync between forks.

---

## Implementation Plan

1. Extract chef provisioner into kitchen-chef-enterprise.
2. Create kitchen-omnitruck-chef and kitchen-cinc gems.
3. Update documentation and migration guides.
4. Add warnings to kitchen-omnitruck-chef.
5. Coordinate with Progress Chef and community maintainers on release of changes.
6. Update default behavior provisioners post-Omnitruck shutdown.

---

## Alternatives Considered

- Keeping chef in core: Leads to long-term maintenance and support issues.
- Single plugin with flags for Chef vs. Cinc: Adds complexity and blurs maintainablility.

---

## Feedback Requested

- Does this separation meet the needs of both Progress Chef and the community?
- Are there concerns about migration or compatibility?
- Suggestions for naming, messaging, or rollout?

---

## Anticipated Downstream Impact

- kitchen-dokken
  - will need to update deps to add kitchen-cinc
  - will need to update rb files requiring chef_infra or chef_zero methods available
- kitchen-dsc
  - will need to update deps to add kitchen-cinc
  - will need to update rb files requiring chef_infra or chef_zero methods available

Possibly others I may not be aware of that rely on chef_infra or chef_zero provisioner methods.

---

## Copyright

This work is in the public domain. In jurisdictions that do not allow for this,
this work is available under CC0. To the extent possible under law, the person
who associated CC0 with this work has waived all copyright and related or
neighboring rights to this work.

---
