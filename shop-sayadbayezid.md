# [Your Brand Name] — Website & Web-Development Marketplace (A–Z Build Prompt)

> Replace `[Your Brand Name]` throughout. **Everything on this site is in Bangla only — no English toggle, no bilingual fields anywhere** (a deliberate difference from every other prompt in this library).

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack e-commerce marketplace that sells website packages and custom web-development services, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Sells full-stack website templates/source code and customized web-development services, showcased via live demo links to already-built sites (Fashion, Gadgets, Herbal, Clothing, News Publishing, Blog Publisher, and more as the catalog grows)
- **Primary market:** Bangladesh small-business owners who want a website built, reached primarily through daily-run Facebook/Instagram ad creatives
- **Language:** Bangla only, site-wide — no English content, no language toggle

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving both the storefront/admin app and all `/api/*` routes. The admin dashboard must support full CRUD for every resource below.

The deliverable must include the following:

## 1. Brand, Business Model & Scope
- This is a **productized-service marketplace**, not a physical-goods store: each "product" is a website package (a specific niche/template tier) or a custom-build service, delivered as actual development work after purchase — not an instant download. Design the order flow around that reality (Section 5), not around instant digital delivery.
- Define the brand identity, tagline, and a premium "web agency" logo concept.
- Categories map to the niches already built: Fashion, Gadgets, Herbal, Clothing, News Publishing, Blog Publisher — each manageable (add/edit/activate/deactivate) from the admin, since the catalog will keep growing as new templates get built.
- Lead capture matters as much as direct purchase — many visitors want a quote or a chat before buying a website; both paths need to exist side by side.

## 2. Public Website — Design & Navigation
- **Visual style — deliberately not a generic storefront look:** deep indigo-to-near-black base with an electric-blue-to-violet mesh gradient as the signature accent (soft-glow, layered — not a harsh rainbow blend), crisp white text, bold modern sans-serif headlines — reads as a premium software/dev-agency product, not a clothing or gadget shop.
- **2026 baseline UX:** mobile-first; adaptive light/dark theming via color tokens; fast, lightweight pages (this audience judges your own site's speed as proof you can build a fast one for them); short demo clips/screen-recordings of each template in action, not just static screenshots.
- Site map: Home, Category pages, Package/Product detail page, Cart/Checkout, Account (Order/Project status), FAQ, About, Contact, Refund Policy, Terms, Privacy Policy — plus **unlimited admin-created landing pages** for specific ad campaigns.
- **Homepage:** hero with a rotating showcase of live demo sites (browser-frame mockups, clickable through to the real demo), category grid, "how it works" (pick a package → we build it → you launch), social proof (client count, testimonials), FAQ teaser, strong CTA.
- **Category page:** every package in that category, each card showing a showcase image, the package name, short description, starting price, and — where active — a **countdown offer timer** and a visible coupon code.
- **Package/Product detail page:** image gallery/screenshot carousel, an embedded or linked **live demo** (opens the actual deployed demo site, e.g. the Lk's Attire site for the Clothing category), full feature list, a **configurator** (base package + optional add-ons — extra pages, AI chatbot, custom domain setup, etc.) with the price **auto-recalculating live** as options are toggled, FAQ specific to that package, reviews, a clear "Order Now" and a secondary "Get a Quote" path for custom requirements.
- **Cart/Checkout:** order summary repeating the configured package + add-ons, coupon code field, payment method selection (Section 10), and — since this isn't instant delivery — a **requirements intake step** right after payment (business name, content/assets, domain preference, reference sites they like) so the project can actually start.

## 3. Category & Package Offer System
- Every category and every package can carry: an active offer (percentage or flat discount), a countdown timer shown to visitors, and one or more coupon codes (percentage/flat, expiry, usage limit, scoped to a category or sitewide).
- **Configurator pricing:** base price + sum of selected add-ons, recalculated instantly client-side and re-verified server-side at checkout (never trust the client-computed total for the actual charge).
- Admin controls every offer's start/end time, so a timer isn't just cosmetic — it should actually stop the discount when it expires.

## 4. Admin Dashboard — Description & Layout
- **Visual style:** same indigo/violet-gradient theme, calmer and denser for daily operations.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Categories, Packages, Landing Pages, Orders/Projects, Leads, Coupons, Banners/Popups, FAQ, Reviews, Chat Settings, AI Content Settings, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with KPI cards (today's orders, revenue, new leads, active chats, projects awaiting delivery), a sales chart, a recent-orders table.
  - **Right sidebar:** search, admin profile dropdown, notifications (new order, new lead, chat escalated to human), staff online status.
- **CRUD modules:**
  - **Categories** — name, image, active/inactive toggle, display order, category-level offer/coupon settings.
  - **Packages** — name, category, base price, add-on list (each with its own price delta), showcase images, live-demo URL, feature list, FAQ items specific to this package, status (active/inactive/coming soon).
  - **Landing Pages** — a page builder for creating unlimited standalone landing pages (their own hero/offer/CTA, independent of the main nav), intended for specific ad campaigns; each gets its own URL slug and its own tracking parameters.
  - **Orders/Projects** — the full project pipeline (Section 5): status, payment record, requirements submitted by the client, internal notes, delivery files/links, auto-generated invoice.
  - **Leads** — quote requests and chat-captured contacts that haven't purchased yet, with follow-up status.
  - **Coupons** — sitewide or category-scoped, percentage/flat, expiry, usage limits.
  - **Banners/Popups** — promotional pop-up banners and site banners, schedulable, editable without a developer.
  - **FAQ** — a managed FAQ list, assignable sitewide or to a specific package.
  - **Reviews** — moderate client reviews/testimonials.
  - **Chat Settings** — configure the multi-channel chat launcher (Section 6): which channels are enabled, WhatsApp number, Messenger page link, AI provider and its system prompt/knowledge.
  - **AI Content Settings** — configure the AI-assisted description/title generator (Section 7): active provider, masked API key, default tone/instructions.
  - **Staff & Roles** — Super Admin, Sales/Support, Project Manager, Read-only Viewer.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over forms; inline validation; confirmation dialog before delete; toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination everywhere.

## 5. Order & Project Delivery Flow
Because a "website" is built, not shipped, the order pipeline looks different from a normal store's:
**Pending Payment → Confirmed → Requirements Collected → In Development → Client Review → Revisions (if needed) → Delivered → Completed**
- Payment is captured first (Section 10), then the client is guided through a short requirements form — this both starts the actual project and reduces back-and-forth over email/WhatsApp later.
- Every status change is visible to the client in their own account area, and triggers an automatic notification (email/SMS/WhatsApp).
- Each order gets an auto-generated, unique, sequential **invoice number**, with a downloadable PDF invoice.
- A "Client Review" step should let the client leave feedback directly against the delivered preview before it moves to "Completed" — this is the natural place to also request the review/testimonial used in Section 2.

## 6. Multi-Channel Chat System
When a visitor clicks the chat bubble, show a short picker — not an immediate single chat window:
- **🤖 AI Support** — answers from the Workers AI (or connected alternative) chatbot, scoped to this site's own packages, pricing, FAQ, and process; escalates to a human if it can't answer confidently.
- **💬 WhatsApp** — opens a WhatsApp chat (via `wa.me` link or the WhatsApp Business API) with the configured number.
- **Messenger** — opens Facebook Messenger via the connected Page.
The visitor picks one and that's where the conversation continues — don't force everyone through the bot first.

## 7. AI-Assisted Content Generation
- In the admin's Package editor, an "Auto-suggest" button generates a draft title and description from a short input (package name + feature bullets), using the configured AI provider.
- **Default provider: Gemini**, with the same pluggable-provider pattern used elsewhere in this library (one internal AI-provider interface, a small adapter per provider so a second/third provider is a contained addition, key stored as a Wrangler secret, masked in the admin UI, never shipped to the client, a "Test connection" button).
- Generated text is always a **draft the admin reviews and edits**, never auto-published without a human looking at it.

## 8. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the storefront/admin assets and all `/api/*` routes, backed by D1, KV, and R2 (screenshots, demo assets, invoice PDFs). Provide a Mermaid architecture diagram showing visitor/admin browsers → Worker → D1/KV/R2, outbound calls to the payment gateway, the AI provider, and WhatsApp/Messenger, and the GitHub → Actions → Cloudflare deploy path.

## 9. Data Models
Provide schema tables for: `categories`, `packages`, `package_addons`, `landing_pages`, `orders`, `order_items`, `project_requirements`, `leads`, `coupons`, `banners`, `faqs`, `reviews`, `admins`, `ai_provider_configs` (masked keys only), `chat_settings`, `activity_log`.

Include one worked example: an **Order** model as a TypeScript interface, including the configured package + add-ons, `invoiceNumber`, `projectStatus` (matching Section 5's pipeline), and the submitted requirements reference.

## 10. Payments
- **Payment methods:** bKash, Nagad, Rocket, bank transfer, and card (Visa/Mastercard) via a gateway such as SSLCommerz for the Bangladesh market; if international clients are expected too, add Stripe as a second gateway rather than forcing everything through one.
- **On crypto:** leaving this out of the payment options. Bangladesh Bank has repeatedly stated that cryptocurrency transactions aren't authorized there and may fall under the Money Laundering Prevention Act — that guidance isn't limited to running an exchange, it covers transacting in crypto generally, so accepting it as a payment method carries the same issue. Worth getting real legal advice on specifically if it matters enough to revisit.
- Payment is captured before a project enters "Confirmed" status — the requirements-intake step (Section 5) only unlocks after payment clears.

## 11. Marketing, Tracking & Analytics
Since new ad creatives run daily, this has to be solid and honest, not approximate:
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set (PageView, ViewContent, Lead, InitiateCheckout, Purchase) from the browser and, in parallel, from the Worker via CAPI the moment each event is actually confirmed server-side — shared `event_id` for deduplication, hashed phone/email for matching. **Never fire a Purchase event without a real, gateway-confirmed payment behind it** — no test/simulated events reaching the production pixel, ever; accurate data is the entire point of tracking this closely.
- **Nothing here should block or delay pixel/CAPI firing** — don't put the core tracking behind a consent gate that effectively disables it; a lightweight, standard privacy notice is enough disclosure for ordinary browsing/ad analytics (this isn't sensitive personal data the way health or financial records are).
- GA4 for aggregate analytics, Microsoft Clarity for heatmaps/session recordings, UTM capture stored against every order and lead so the Reports section can show which specific ad/landing page actually converted.
- Every landing page (Section 2/4) should carry its own tracking so campaign performance is attributable per-creative, not just per-site.

## 12. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover): password hashing, HTTPS/HSTS, RBAC on every admin action, secrets (payment, AI provider, WhatsApp/Messenger) stored as Wrangler secrets and masked in the UI, admin audit logging, rate-limiting on auth/checkout, an incident-response outline, and a D1 backup/restore procedure. Client-submitted project requirements (Section 5) often include business details worth protecting — treat that data with the same care as payment data, not as an afterthought form submission.

## 13. Premium Design — What "Perfect" Actually Means Here
Since this site's whole job is to make visitors trust you can build *them* a great site, a few things matter more here than almost anywhere else in this library:
- **Dogfooding is the strongest sales argument:** the site's own speed, polish, and lack of template-generic feel should be the first proof point, before a single word of copy.
- **Show, don't just describe:** live demo links and short screen-recordings beat feature-bullet lists — let the actual built sites (Lk's Attire, etc.) do the convincing.
- **Pricing transparency builds trust fast** in a market where "how much will this actually cost" is the #1 hesitation — the configurator's live price recalculation is doing real trust work, not just convenience.
- **Consistent motion language:** the same transition/hover style across category cards, the configurator, and the checkout — small, but it's exactly the kind of polish detail that signals "these people are good at this" to a visitor evaluating whether to hire you.

## 14. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E covering configure → pay → requirements-intake, since that's the full conversion path; a test confirming the server-side price recalculation matches the client-side configurator total; JSON fixtures for package-list, create-order, and admin-login endpoints; TypeScript build check required before deploy.

## 15. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx) for both storefront and admin, consistent with the rest of this library; a small charting library (Chart.js) for analytics; full usability on tablet/phone, since you'll likely be managing leads and chats from a phone between other work.

## 16. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (categories, packages, landing-pages, orders, leads, chat, ai-content, admin)
│   │   ├── lib/              (payment, ai-providers/, invoice generator, whatsapp/messenger links)
│   │   └── index.ts
│   ├── migrations/
│   └── wrangler.toml
├── public/
├── admin/
├── tests/
├── .github/workflows/
└── docs/
```

## 17. Development Environment & Deployment Setup
Node.js version, `wrangler` CLI install/login, `wrangler d1 create`, KV namespace, R2 bucket, `wrangler secret put` for payment and AI-provider keys, sample `wrangler.toml` and `.dev.vars`, GitHub branch strategy, a GitHub Actions workflow that tests/builds/migrates/deploys on push to `main`, and custom-domain setup via Cloudflare DNS.

## 18. Suggested Initial Categories & Packages
Start with the categories already built out: Fashion/Clothing, Gadgets & Tech, Herbal/Natural, News Publishing, Blog Publisher — each with at least a Basic and a Premium package tier, add-ons for things like an AI chatbot, extra landing pages, or a custom domain setup. Add new categories as new templates get built.

## 19. Phased Roadmap
- **Phase 1 (MVP):** category/package browsing, configurator pricing, checkout with bKash/Nagad/card, basic admin CRUD, project pipeline (manual status updates).
- **Phase 2:** landing-page builder, coupons/offer timers, the multi-channel chat launcher, AI-assisted descriptions, Meta Pixel + CAPI.
- **Phase 3:** client self-service review/approval step, referral program, automated project-status notifications, PWA.

## 20. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; start from a clean checkout before editing existing code; never commit a real API or payment key.

## 21. Additional Premium Features
- **Client project portal** — a simple space where a buyer can see their project's current status, leave feedback, and download the final files once delivered.
- **Referral program** for past clients.
- **"Which package fits me?"** short quiz, feeding into the lead/configurator flow.
- **Automated lead follow-up** for quote requests that go quiet.
- **Honesty guardrail:** no fabricated "X people viewing" or "X sold today" — if shown, must reflect real data.

## 22. Owner-Friendly Operations & Onboarding
- **Guided first-run setup:** first category, first package, connect a payment method, connect WhatsApp/Messenger — one simple action per step.
- **A plain-language "Health Check" strip** on the dashboard home ("Payment: connected" / "Meta Pixel: not connected yet").
- **A "Needs your attention today" home view:** new leads, chats awaiting a human, orders stuck in one status too long — ahead of chart-heavy analytics.
- **One-tap actions:** approve an AI-generated description, move a project to the next status, mark a lead as followed up.
- **CSV export everywhere there's a list** — orders, leads, reviews.

## 23. Acceptance Checklist
- [ ] Entire site is Bangla-only, no English strings anywhere
- [ ] Configurator price recalculates live client-side and is re-verified server-side at checkout
- [ ] Category/package offer timers actually stop the discount when they expire
- [ ] Landing pages can be created, published, and retired from the admin without a developer
- [ ] The chat bubble shows the AI/WhatsApp/Messenger picker before opening any specific channel
- [ ] AI-generated descriptions always land as an editable draft, never auto-published
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id, and Purchase only fires on a real confirmed payment
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] The project pipeline status is visible to the client and triggers a real notification on each change
- [ ] No fabricated social-proof numbers anywhere on the site
- [ ] No secrets committed to the repository

## Output Format
Single structured document: labeled sections matching the numbering above; a Mermaid architecture diagram; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree.

## Notes
- The never-fake-events rule in Section 11 is a hard requirement — the entire point of this site's tracking setup is to make real ad-spend decisions off it.
- The payment-before-requirements-intake rule in Section 5 is a hard requirement, same reasoning as every other build in this library: don't start real delivery work before payment is actually confirmed.
- Flag any assumption (exact package tiers, exact add-on pricing, exact categories beyond the starting list) as adjustable and confirm before finalizing.