# v0.5 · Phase 2 — Build

## Tasks

### Builder
- [ ] Field palette and drag-and-drop canvas
- [ ] Section management: add, reorder, duplicate, collapse
- [ ] Property panel per field type
- [ ] Bilingual label editor, English and Arabic together
- [ ] Conditional-logic editor rendering as plain sentences
- [ ] Live preview: phone frame and PDF frame
- [ ] Pre-publish validation with actionable messages
- [ ] Version diff view and change note on publish
- [ ] Test mode: fill the form as an engineer would, then discard
- [ ] Duplicate a template; duplicate a section

### New field types
- [ ] multiselect, time, datetime, rating, checklist
- [ ] geolocation configuration, with accuracy threshold
- [ ] photo configuration: max count, annotation toggle
- [ ] signature configuration: require name, require role
- [ ] Renderers for each on web and in React Native
- [ ] Engine support for each in validation and visibility

### PDF service
- [ ] `services/pdf-renderer`: containerized headless Chromium
- [ ] Arabic and Latin fonts embedded in the image
- [ ] HTML template driven by the form version and the submission
- [ ] Tenant branding: logo, colours, header and footer
- [ ] Photos, signatures with metadata, location thumbnail
- [ ] RTL layout when the submission's locale is Arabic
- [ ] Triggered by a background job; result stored as a file record
- [ ] Download endpoint with a short-lived signed URL

### Verification
- [ ] Verify and reject actions with reasons
- [ ] Amendment flow preserving the original submission
- [ ] Verification queue for supervisors
- [ ] Audit entries for verify, reject and amend

### Reporting projection
- [ ] Extend the projection to the new field types
- [ ] A simple cross-submission query screen: pick a form, a field, a filter

## Exit criteria

- [ ] A form is built, previewed, validated and published entirely through the UI
- [ ] Publishing an invalid form is prevented with a clear explanation
- [ ] A submission renders as a branded PDF in both languages
- [ ] Verification and rejection work end to end
