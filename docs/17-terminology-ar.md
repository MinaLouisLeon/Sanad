# 17 — Arabic Terminology Glossary (Draft)

**Status:** Draft for native review. Owner: product owner. **Frozen before the
first user-facing screen in M1.**

Under ADR-014, English is the default locale and the source catalog; Arabic is
the single translation target. That makes this glossary the **complete
specification of the Arabic product vocabulary** — there is no second translation
to average against. A wrong word for "dispatch" is wrong regardless of which
locale is the default, and with only two locales it is also highly visible.

This is a starting point produced to accelerate the review, not an authority. A
native-Arabic **operations person from a design partner** must validate it — a
professional translator will render "dispatch" and "van stock" correctly as
language and incorrectly as maintenance vocabulary.

Terms marked ⚠️ are the ones where the natural translation and the industry usage
diverge most; review these first.

## Rules

1. One Arabic term per concept, everywhere — UI, notifications, PDFs, help,
   sales material.
2. Prefer the word a maintenance supervisor actually says on site over the
   formally correct one.
3. Where an English term is used in the industry (SLA, HVAC, QR), keep it in
   Latin script rather than inventing an Arabic coinage nobody uses.
4. Once frozen, changes require a product-owner decision and a full sweep.

## Core operations

| English | Arabic (proposed) | Note |
|---|---|---|
| Job / Work order | أمر عمل | The central noun in the product |
| Job number | رقم أمر العمل | |
| Task | مهمة | Step within a job |
| Assignment | تكليف | Person assigned to a job |
| Dispatch | توزيع المهام | ⚠️ "إرسال" reads as sending a message |
| Dispatch board | لوحة التوزيع | ⚠️ Confirm with a dispatcher |
| Dispatcher | منسق العمليات | ⚠️ Job title, must sound like a real one |
| Field engineer / technician | فني الميدان | Shortened to الفني in dense UI |
| Supervisor | مشرف | |
| Schedule (noun) | الجدول | |
| Schedule (verb) | جدولة | |
| Visit | زيارة | |
| Failed visit | زيارة غير مكتملة | ⚠️ Must not read as the engineer's failure |
| En route | في الطريق | |
| On hold | معلّق | |
| Completed | مكتمل | |
| Verified | معتمد | Supervisor approval |
| Closed | مغلق | |
| Priority | الأولوية | |
| Emergency | طارئ | |

## Customers and assets

| English | Arabic (proposed) | Note |
|---|---|---|
| Customer | عميل | |
| Contact | جهة اتصال | |
| Site | موقع | |
| Contract | عقد | |
| Annual maintenance contract (AMC) | عقد صيانة سنوي | The core commercial object in HVAC |
| Asset / Equipment | أصل | ⚠️ Register uses الأصول; technicians say المعدات. Decide one |
| Asset tag | رمز الأصل | |
| Serial number | الرقم التسلسلي | |
| Warranty | الضمان | |
| Preventive maintenance | صيانة وقائية | Standard industry term |
| Corrective maintenance | صيانة تصحيحية | |
| Service history | سجل الصيانة | |

## Forms

| English | Arabic (proposed) | Note |
|---|---|---|
| Form | نموذج | |
| Form template | قالب نموذج | |
| Form version | إصدار النموذج | |
| Submission | ⚠️ سجل النموذج | **Weakest term in this list — needs a native decision.** Alternatives: تقرير (report), الاستمارة المعبأة. In inspection contexts تقرير may be clearer to users |
| Field (form field) | حقل | |
| Section | قسم | |
| Checklist | قائمة تحقق | |
| Inspection | فحص | |
| Signature | توقيع | |
| Photo | صورة | |
| Score | النتيجة | |
| Pass / Fail | ناجح / راسب | ⚠️ راسب carries an academic tone; consider مطابق / غير مطابق for compliance contexts |
| Not applicable | لا ينطبق | |

## Inventory

| English | Arabic (proposed) | Note |
|---|---|---|
| Inventory / Stock | المخزون | |
| Item | صنف | |
| Spare part | قطعة غيار | |
| Warehouse | مستودع | |
| Van stock | مخزون المركبة | ⚠️ Confirm what storekeepers actually call it |
| Stock movement | حركة مخزون | |
| Transfer | تحويل | |
| Consumption | استهلاك | |
| Return | إرجاع | |
| Adjustment | تسوية | |
| Stock count | جرد | |
| Variance | فرق الجرد | |
| Reorder point | حد إعادة الطلب | |
| Purchase order | أمر شراء | |
| Supplier | مورّد | |
| Batch | دفعة | |
| Expiry date | تاريخ الانتهاء | |

## Meetings

| English | Arabic (proposed) | Note |
|---|---|---|
| Meeting | اجتماع | |
| Attendee | مشارك | |
| Agenda | جدول الأعمال | |
| Minutes | محضر الاجتماع | |
| Action item | إجراء مطلوب | |
| Toolbox talk | ⚠️ اجتماع السلامة | No settled Arabic term; "لقاء السلامة" also used. Pick what Civil Defence documentation uses |

## Platform and account

| English | Arabic (proposed) | Note |
|---|---|---|
| Branch | فرع | |
| Team | فريق | |
| Role | الصلاحية | ⚠️ دور is literal; الصلاحية matches permissions usage |
| Permission | إذن | |
| User | مستخدم | |
| Subscription | الاشتراك | |
| Seat | مقعد | ⚠️ Verify — رخصة (licence) may read better commercially |
| Invoice | فاتورة | |
| Audit log | سجل التدقيق | |
| Report | تقرير | |
| Dashboard | لوحة المعلومات | |
| Sync | مزامنة | |
| Offline | غير متصل | |
| SLA | SLA | Keep in Latin script — universally used in Gulf contracting |

## Metrics

| English | Arabic (proposed) |
|---|---|
| First-time fix rate | معدل الإصلاح من الزيارة الأولى |
| Utilization | معدل الاستغلال |
| Response time | زمن الاستجابة |
| Resolution time | زمن الإنجاز |
| SLA breach | إخلال بمستوى الخدمة |
| Overdue | متأخر |

## Review process

1. Product owner reviews and corrects this draft.
2. A native-Arabic operations person at a design partner reviews it **out loud
   with a dispatcher and a technician present** — the test is whether they
   recognize the word, not whether it is correct.
3. Terms marked ⚠️ are decided explicitly and the reasoning recorded here.
4. The result is frozen and loaded into `packages/i18n` as the `ar` base catalog
   before the first M1 screen is built.
