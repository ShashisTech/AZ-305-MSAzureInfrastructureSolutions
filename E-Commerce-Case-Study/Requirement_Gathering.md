1.  Business

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Overall Business Model | Product Owner, Finance, Legal/Compliance | Are we confirming a hybrid marketplace supporting both Auctions and Buy Now for all eligible categories? |
| Revenue & Fees | Finance, Product Owner | What platform fees (listing, success, payment) and rent charges apply to sellers across plans? |
| KPIs & Targets | Product Owner, Analytics | What are target KPIs (GMV, conversion rate, auction win rate, ASP) for the first two quarters? |
| Role Model | Product Owner, IAM, Security | Are Buyer, Seller, and Administrator the only roles, or do we need sub‑roles (Ops Admin, Finance Admin)? |
| Geo & Currency Scope | Product Owner, Finance | Which countries, currencies, and tax regimes will be supported at launch? |

1.  Buyer

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Home – Top 5 Surfacing | Product Owner, UX, Merchandising | How should Top 5 items/categories be computed (sales, views, curation) and refreshed (real‑time, daily)? |
| Home – Category Navigation | Product Owner, UX | What is the final category hierarchy and order for navigation? |
| Anonymous vs Logged‑in View | Product Owner, UX | What differences apply between anonymous home and buyer home (content, promos, CTAs)? |
| Search – Fields | Product Owner, Search Platform | Which fields are searchable (title, description, seller name, category)? |
| Search – Advanced Filters | Product Owner, Search Platform | Which advanced filters are mandatory (category, price, condition, seller location, auction/buy now)? |
| Search – Ranking | Product Owner, Search Platform | What ranking strategy should be used (relevance, popularity, recency) with tie‑breakers? |
| Search – UX | UX, Product Owner | Do we need autosuggest, typo tolerance, synonyms, and facets with counts? |
| Search – Performance | Architecture, SRE | What p95/p99 latency targets and pagination strategy apply for search results? |
| Registration – Required Data | Product Owner, Legal, Security | Which registration fields are mandatory and what validations (email/phone OTP, DOB) are required? |
| Registration – Privacy | Legal/Compliance, Security | What consent texts, data retention, and deletion policies apply to buyer data? |
| Login & AuthN | Security/IAM, Product Owner | Do we require MFA and social/SSO login options, and what session timeouts apply? |
| View Items – Listing Data | Product Owner, UX | Which listing attributes must be shown (image, name, latest bid, buy price, shipping, seller, bid window)? |
| View Items – Sorting | Product Owner, UX | What sorting options are needed (price, popularity, ending soon) and default order? |
| Item Detail – Required Fields | Product Owner, UX | Which details must appear (condition, current highest bid/bidder, timings, buy price, shipping, seller info)? |
| Item Detail – Badges | Product Owner, UX | Do we display badges such as New/Used, Verified Seller, Free Shipping, Reserve Met? |
| Bidding – Increments | Product Owner, Marketplace Ops | What bid increment rules and minimum/maximum limits should apply per price band? |
| Bidding – Reserve/Threshold | Product Owner, Marketplace Ops | How is the reserve (minimum threshold) handled and shown, and when is a winner declared? |
| Bidding – Anti‑Sniping | Product Owner, Engineering | Should we extend auction end time if bids occur in the last X minutes? |
| Bidding – Notifications | Product Owner, Marketing Ops | Which notifications are needed (outbid, winning, auction ending soon) and via which channels? |
| Buy Now – Precedence | Product Owner, Order Mgmt | Should Buy Now immediately close the auction and cancel all active bids? |
| Buy Now – Reservation | Order Mgmt, Engineering | What inventory reservation/capture rules and cart timeouts should apply? |
| Compare Prices – Columns | Product Owner, UX | Which columns to show (image, seller, location, buy price, shipping, total landed cost)? |
| Past Transactions – History | Product Owner, Finance | What fields are required and do buyers need invoice/receipt downloads? |
| Watch List – Triggers | Product Owner, UX | Which events should trigger watch notifications (price change, new bid, time extension)? |
| Notifications – Preferences | Product Owner, Marketing Ops | How can users manage notification preferences (email/SMS/push) for bids, wins, and watches? |

1.  Admin

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Seller Status – Workflow | Admin Ops, Compliance | Is a maker‑checker approval required for activating/deactivating sellers? |
| Transaction History – Filters | Admin Ops, Finance | Which filters, fields, and export formats are required for admin transaction views? |
| Defaulted Rent – Dunning | Finance/Billing, Admin Ops | What rules apply for delinquency, reminders, suspension, and reactivation? |

1.  Operations

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Shipping Cost – Calculation | Product Owner, Operations | How is shipping cost calculated per item and can sellers override per auction? |
| Returns & Disputes – Policy | Customer Support, Legal | What RMA process, dispute resolution steps, and SLAs apply for auctions and Buy Now? |
| Customer Support – SLA | Customer Support, Product Owner | What SLAs and channels (email/phone/chat) will support disputes, returns, and payment issues? |

1.  Payments

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Payment Methods – Scope | Payments, Finance | Which methods are supported at launch (PayPal, Credit Card, Demand Draft, Cheque, COD)? |
| Payment Gateway – Flow | Payments, Security | Do we authorize then capture or capture immediately for Buy Now and auction wins? |
| Offline Payments – Proof | Payments, Finance | What verification is needed before dispatch for cheque/DD, and how is proof recorded? |
| Refunds – Timelines | Customer Support, Finance | What refund timelines apply per method and who bears fees for cancellations/returns? |
| Tax & Invoicing | Finance, Legal | How will taxes (GST/VAT) be calculated and shown on checkout and invoices for buyers and sellers? |

1.  Security

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| PII Protection | Security, Privacy/Legal | How will we encrypt PII at rest/in transit and restrict access to sensitive buyer/seller data? |
| Credentials & Secrets | Security/IAM | What password policy, secret Q&A handling, rate limiting, and lockout rules are required? |
| Compliance – PCI/PayPal | Security, Compliance | What is the PCI scope for card handling and how will PayPal webhooks be secured and verified? |

1.  Seller

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Seller Registration – KYC | Compliance/KYC, Marketplace Ops | What KYC documents and checks are required before activation? |
| Seller Activation – Rent | Finance/Billing, Admin Ops | What rent plans (monthly/quarterly/yearly), grace periods, and activation SLAs apply? |
| Shop Setup – Inputs | Product Owner, UX | What fields are required for shop setup (name, description, accepted payment options)? |
| Item Management – Fields | Product Owner, UX | Which item fields must be provided (image, name, description, price, category, quantity, reserve, shipping, condition)? |
| Item Management – Bulk Upload | Product Owner, Engineering | Do we need CSV/API bulk upload with templates and image handling guidelines? |
| Setup Bids – Scheduling | Product Owner, UX | What limits apply to auction start/end times and duration; can a live auction be edited? |
| Items on Sale – Dashboard | Product Owner, Analytics | Which status fields are required (sold/unsold, method, selling price, payment made, payment option, buyer details)? |
| Dispatch – Carrier Integration | Operations/Logistics, Engineering | Do we integrate with couriers for labels and tracking numbers, and show ETA to buyers? |
| Dispatch – Stock Decrement | Product Owner, Engineering | Should inventory decrement at payment capture or at dispatch confirmation? |
| Stock Update – Controls | Product Owner, Security | Do we need an audit trail and role permissions for stock/price edits? |
| Past Transactions – Reporting | Finance, Analytics | What exports and filters are required for seller transaction history? |
| Quality Standards & Listing Policy | Compliance, Marketplace Ops | What content guidelines and prohibited items policies must sellers follow? |

1.  Technology

| Topic | Client POC Team | Questions to Ask |
| --- | --- | --- |
| Availability & Uptime | Architecture, SRE | What availability target (e.g., 99.9%+) and zero‑downtime deployment approach will we adopt? |
| Performance – Peak Events | Architecture, SRE | How will the system handle auction end‑time bursts and search spikes at peak load? |
| Observability & Audit | SRE, Security | What logging, metrics, tracing, and audit events must be captured with retention policies? |
| Integrations – Internal Systems | Architecture, Product Owner | Which internal systems must be integrated at launch and what interfaces will be used? |
| Internationalization & Time Zones | Product Owner, Localization | How will we present auction start/end times and price formats across locales and time zones? |
| Accessibility | UX, QA | What accessibility level (e.g., WCAG 2.1 AA) and keyboard/timer behavior must be supported? |
| Data Retention & Purge | Security, Legal, Data | What data retention periods and purge processes apply for user, order, and auction data? |