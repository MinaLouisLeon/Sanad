# v0.5 · Phase 3 — Verify

## Tasks

### Builder
- [ ] Every field type can be added, configured, previewed and published
- [ ] Validation catches each failure case it is supposed to catch
- [ ] Renaming a published field key is impossible through the UI
- [ ] Reordering and nesting produce a valid schema
- [ ] The diff summary matches the actual change
- [ ] A form built in the builder renders identically on web and on mobile

### PDF — the highest-risk area in this version
- [ ] English PDF: layout, photos, signatures, page breaks, long content
- [ ] **Arabic PDF with real Arabic content**: correct shaping and joining, right
      alignment, mixed Arabic and Latin (an Arabic sentence containing an English
      model number), numerals as configured
- [ ] Confirm no missing-glyph boxes, no reversed text, no broken ligatures
- [ ] A submission with the maximum photos renders without timing out
- [ ] A five-year-old-style submission on an old version renders against that version
- [ ] Branding applied correctly; a tenant with no logo degrades gracefully

### Verification
- [ ] Rejection returns the submission to the submitter with the reason visible
- [ ] Amendment preserves the original and both are retrievable
- [ ] Permissions: an engineer cannot verify their own submission

### Isolation and access
- [ ] New routes added to the isolation suite
- [ ] A PDF URL from one tenant is not retrievable by another
- [ ] Signed URLs expire

### Interface
- [ ] Builder QA in English and **Arabic RTL** — drag-and-drop in RTL is a known
      trouble spot and needs real testing, not inspection
- [ ] Extend `QA.md`

## Exit criteria

- [ ] Builder produces valid forms across every supported type
- [ ] Arabic PDF verified correct with real content on a real document
- [ ] Verification flow proven
- [ ] Isolation extended, signed URLs scoped and expiring
- [ ] Builder usable in Arabic RTL
