# Domain-specific non-functional requirements

Use this during Phase 4 of the interview.

- Pick the one or two domains that fit the product, and read only those sections.
- Choose the qualities that matter to this feature. Skip the rest.
- Example targets are starting points for discussion, not company standards. Confirm each with the user, or mark it `(assumed)` in the PRD.
- Name a regulation only if it applies, and add an open question for legal or compliance to confirm it.
- Keep requirements to what customers and the business need. "p95 search under 500 ms" belongs in a PRD; "add a search index" does not.

## Contents

- [Web and SaaS products](#web-and-saas-products)
- [Public APIs and developer platforms](#public-apis-and-developer-platforms)
- [Mobile apps](#mobile-apps)
- [Data, analytics and reporting](#data-analytics-and-reporting)
- [AI and LLM features](#ai-and-llm-features)
- [Payments and fintech](#payments-and-fintech)
- [Healthcare](#healthcare)
- [E-commerce and marketplaces](#e-commerce-and-marketplaces)
- [Internal tools and admin](#internal-tools-and-admin)
- [Any other domain](#any-other-domain)

## Web and SaaS products

| Category | What to pin down | Example target |
| --- | --- | --- |
| Performance | Page and action speed for real users | p95 page load under 2.5 s |
| Availability | Uptime customers can expect | 99.9% monthly |
| Accessibility | Standard the UI must meet | WCAG 2.2 AA |
| Tenant isolation | Separation of customer data | No cross-tenant data access in security tests |
| Browser support | Browsers and versions covered | Latest two versions of Chrome, Edge, Safari, Firefox |
| Privacy | Personal data handling, retention, deletion | Deletion requests completed within 30 days |

## Public APIs and developer platforms

| Category | What to pin down | Example target |
| --- | --- | --- |
| Latency | Response time per endpoint type | p95 under 300 ms for reads |
| Availability | Contractual or published uptime | 99.95% monthly |
| Compatibility | Versioning and deprecation promise | 12 months' notice before breaking changes |
| Rate limits | Limits per tier and client feedback | Limits documented, 429 with retry guidance |
| Error quality | Whether developers can act on errors | Every error has a documented code and fix |
| Developer experience | Time to get started | First successful call within 15 minutes using the docs |

## Mobile apps

| Category | What to pin down | Example target |
| --- | --- | --- |
| Startup | Time until the app is usable | Cold start under 2 s on a mid-range device |
| Stability | Crash rate | Crash-free sessions at 99.5% or higher |
| Offline | What works without a connection | Saved items readable offline, changes sync on reconnect |
| Battery and data | Cost to the customer's device and plan | Under 5 MB of background data per day |
| Device support | OS versions and screen sizes | Current and previous two OS versions |
| Store compliance | Platform policies | Passes App Store and Google Play review at first submission |

## Data, analytics and reporting

| Category | What to pin down | Example target |
| --- | --- | --- |
| Freshness | How old data can be | Dashboards no more than 15 minutes behind |
| Accuracy | Agreement with the source of truth | Totals within 0.5% of source |
| Completeness | Handling of missing or late data | Missing data flagged in the report, never shown as zero |
| Query speed | Wait time at expected data volume | p95 under 5 s on 12 months of data |
| Retention | How long data is kept | Raw events kept for 13 months |
| Access control | Who sees which data | Row-level access matches the user's account |

## AI and LLM features

| Category | What to pin down | Example target |
| --- | --- | --- |
| Quality | Correctness on a fixed evaluation set | 90% of answers rated correct on a 200-question set |
| Groundedness | Claims supported by supplied sources | 95% of claims traceable to a cited source |
| Safety | Harmful or policy-breaking output | No critical failures in the red-team set |
| Latency | Wait before and during a response | p95 time to first token under 2 s |
| Cost | Spend per use | Under $0.02 per request at launch volume |
| Human control | Where people review or undo | Users can edit or reject every generated action |
| Data use | Handling of customer data with model providers | Customer data not used for model training |
| Transparency | How customers know AI is involved | AI-generated content is labelled |

## Payments and fintech

| Category | What to pin down | Example target |
| --- | --- | --- |
| Transaction integrity | Protection against loss and duplication | Zero duplicate charges in failure and retry tests |
| Reconciliation | Agreement between internal and provider records | 100% of transactions reconciled daily |
| Auditability | Record of who did what | Every money movement logged and kept for the legal period |
| Compliance | Rules that apply, such as PCI DSS or KYC/AML | Legal or compliance sign-off before release |
| Availability | Checkout or transfer uptime, especially at peaks | 99.99% during peak periods |
| Fraud | Acceptable fraud and false-decline rates | False declines under 1% of legitimate payments |

## Healthcare

| Category | What to pin down | Example target |
| --- | --- | --- |
| Patient data protection | Rules that apply, such as HIPAA or GDPR | Legal or compliance sign-off before release |
| Audit trail | Record of access to patient data | Every read and change logged with user and time |
| Access control | Who sees which records | Role-based access reviewed quarterly |
| Clinical safety | Harm from wrong or missing information | Clinical review of all patient-facing content |
| Interoperability | Standards for exchanging records | Named standard such as FHIR R4 |
| Availability | Downtime procedure in care settings | Documented fallback for any outage over 15 minutes |

## E-commerce and marketplaces

| Category | What to pin down | Example target |
| --- | --- | --- |
| Peak load | Behaviour during sales and traffic spikes | Handles 5x normal traffic with no checkout errors |
| Checkout speed | Delay on purchase steps | p95 checkout step under 1.5 s |
| Inventory accuracy | Overselling | Oversold orders under 0.1% |
| Search quality | Whether shoppers find products | Under 5% of searches return no results |
| SEO | Search visibility of product pages | No loss of indexed pages after release |
| Localization | Currencies, taxes and languages | Named list of launch countries |

## Internal tools and admin

| Category | What to pin down | Example target |
| --- | --- | --- |
| Task efficiency | Time saved over the current process | Task completed in half the current time |
| Permissions | Who can do what | Least-privilege roles, reviewed quarterly |
| Auditability | Record of sensitive actions | All admin actions logged with user and time |
| Safety | Protection against costly mistakes | Bulk changes need a preview and can be undone |
| Adoption | Whether staff actually move to it | 80% of target staff using it within 60 days |

## Any other domain

If none of the above fit, derive the requirements by asking:

1. What failure would hurt the customer most, and how quickly would they notice?
2. What failure would hurt the business most: money, trust, legal exposure or support load?
3. What volume, peak or growth must this handle at launch and in a year?
4. What rules, contracts or platform policies apply?
5. Who needs access, who must not have it, and what must be traceable afterwards?

Turn each answer into a requirement with a target and a way to verify it.
