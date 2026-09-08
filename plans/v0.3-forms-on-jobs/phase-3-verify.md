# v0.3 · Phase 3 — Verify

## Tasks

### Versioning — the critical tests
- [ ] Publishing never mutates an existing version, asserted at the database level
- [ ] A submission always renders against its own version
- [ ] A draft in progress survives a publish and completes on its original version
- [ ] Renaming a field key on a published form is rejected
- [ ] Reusing a retired field key is rejected
- [ ] Version diff reports added, removed and changed fields correctly

### Engine
- [ ] Property-based tests over generated schemas and answer sets
- [ ] **The same schema and answers produce the same visibility and validation
      result in the browser and on the server.** This is the bug class that
      produces submissions rejected in the field
- [ ] Deeply nested and contradictory conditions behave predictably
- [ ] Validation covers every rule in the vocabulary

### Storage
- [ ] Projection rows match `data` for every submission
- [ ] Rebuilding the projection from scratch reproduces it exactly
- [ ] A cross-submission query returns correct results — the reason the projection exists

### Isolation and access
- [ ] New tables and routes added to the isolation suite
- [ ] A submission cannot be read across tenants by id
- [ ] Permissions: who may publish, who may submit, who may view others' submissions

### Interface
- [ ] Manual QA of the renderer in English and **Arabic RTL** — labels, help text,
      validation messages, radio and checkbox alignment, date fields
- [ ] Long Arabic labels and options do not break layout
- [ ] Extend `QA.md`

## Exit criteria

- [ ] Version immutability proven by test, not by convention
- [ ] Engine parity between client and server proven
- [ ] Projection correctness and rebuild proven
- [ ] Isolation suite extended and green
- [ ] Arabic RTL pass complete
