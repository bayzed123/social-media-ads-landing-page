# [Your Brand Name] — Personalized Gifts & Print-on-Demand E-commerce (A–Z Build Prompt)

> Replace `[Your Brand Name]` and `[Your Location]` throughout before using. Example name directions: "Naam" ("naam" = name), "Engrave & Co.", "Smriti Gifts" ("smriti" = memory). Technically distinct from the other prompts: products here are made-to-order around customer-entered text/images, not fixed size/color variants.

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack e-commerce website with a fully-featured admin dashboard, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Personalized gifts & print-on-demand retail (custom name jewelry/keychains, personalized mugs & frames, custom apparel printing, engraved gifts)
- **Location:** [Your Location], Bangladesh
- **Primary market:** Bangladesh, gift buyers (birthdays, anniversaries, Eid, weddings) and younger creative shoppers, mobile-first, Bangla + English speaking

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving both the static storefront/admin app and all `/api/*` routes. The admin dashboard must support full CRUD for every resource below, with validation, confirmation prompts, and success/failure feedback.

The deliverable must include the following:

## 1. Brand, Market & Business Requirements
- Define the brand identity, a short tagline, and a warm, joyful, modern-craft logo concept direction.
- Target customer: gift buyers marking an occasion, and shoppers who want something that feels one-of-a-kind rather than mass-produced.
- All customer-facing and admin UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- Business model: Cash on Delivery as default, plus mobile financial services and card payment. Because items are made to order, set clear **production lead-time** expectations (e.g. "ships in 2–4 business days after you approve the design") everywhere a price is shown.
- Feature [Your Location] on the Contact/About page, footer, and structured data.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.

## 2. Public Website — Description & Layout
- **Visual style:** warm, joyful, gift-shop-like, not generic. Crisp White and Digital Lavender as the base pairing with Muted Rose as a warm accent (all three straight off current web color-trend reports); a soft handwritten/script accent font used sparingly for personalization previews, paired with a clean sans-serif for everything else.
- **2026 baseline UX expectations to build in from the start:** mobile-first and thumb-friendly; adaptive light/dark "mood mode" theming driven by color tokens; short videos showing the engraving/printing process (builds trust that this is real craft, not a stock photo); fast, lightweight pages.
- Site map: Home, Shop (category & subcategory), Product Detail (with Live Customizer), Cart, Checkout, Account (Login/Register, Orders, Wishlist, Addresses), About/Contact, Search Results, optional Occasion Guides (Eid, Wedding, Birthday).
- **Homepage:** hero banner, category tiles (Jewelry, Drinkware, Frames & Albums, Apparel, Engraved Gifts), an occasion-based collection banner, best sellers, customer photos of the finished personalized product, newsletter signup, footer with location and policies.
- **Product listing:** filters by category, occasion, price, and personalization type (text engraving, photo upload, name only); sort by price/newest/popularity/rating.
- **Product detail page — Live Customizer (the core differentiator of this build):** the customer types the name/text (and, for photo items, uploads an image) directly on the product page and sees a live or near-live preview of the finished item before adding to cart; clear character limits and font/color choices where relevant; a visible production lead-time note; price updates automatically if personalization options carry a surcharge; Add to Cart/Buy Now only enabled once the customization is complete; customer reviews, related/upsell products.
- **Optional proof-approval step:** for complex customizations, the order can be held at "Awaiting Proof Approval" — the shop sends a rendered preview via WhatsApp/email, and production only starts once the customer approves it (protects against costly misprints from typos).
- **Cart & checkout:** guest checkout, Bangladesh address form with tiered delivery fee, payment method selection (COD, bKash, Nagad, Rocket, card via SSLCommerz), order summary clearly repeating each item's personalization text for the customer to double-check, order confirmation with SMS/WhatsApp notification.
- **Account area:** order history with tracking and a copy of each past personalization (handy for reordering the same design), wishlist, saved addresses.

## 3. Admin Dashboard — Description & Layout
- **Visual style:** warm and light — crisp-white background, lavender and muted-rose accents, soft rounded cards.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Products, Categories, Orders, Proof Approvals, Customers, Coupons, Inventory, Reviews, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with a monthly sales chart, KPI cards (today's orders, revenue, pending COD, orders awaiting proof approval, low-stock alerts), recent-orders table, top-selling products.
  - **Right sidebar:** search, admin profile dropdown, notifications (new order, proof approved/rejected, new review), staff online status.
- **CRUD modules:**
  - **Products** — name, bilingual description, category, base price, personalization type (text/photo/both), character limit, font/color choices, surcharge rules, images, production lead-time (days), status, SEO slug, auto-generated SKU (see Section 5); bulk CSV import/export.
  - **Categories & Subcategories** — nested, drag-to-reorder.
  - **Orders** — line items with SKUs **and the exact personalization text/image submitted for each item**, customer, address, payment method/status, delivery pipeline (Pending → Confirmation Attempted [logged call/OTP outcomes, one-tap buttons — see Section 20] → Confirmed → **Awaiting Proof Approval** (where applicable) → In Production → Packed → Shipped [courier + tracking, ingesting courier sub-statuses like Out for Delivery] → Delivered, with three distinct off-ramps: Cancelled, Refused at Delivery, and Returned, kept separate for risk scoring and stock), **auto-generated invoice number and a printable PDF invoice** (see Section 5), refund handling.
  - **Proof Approvals** — a queue of items awaiting customer sign-off on their rendered preview, with the preview image, approve/reject log, and a one-tap way to resend.
  - **Customers** — profile, order history, block/unblock.
  - **Coupons/Discounts** — percentage/flat, minimum order, expiry, usage limits.
  - **Inventory** — per-SKU stock of blank/raw materials (blank mugs, chain stock, fabric), low-stock alerts, adjustment log.
  - **Reviews** — approve/reject/reply.
  - **Staff & Roles** — Super Admin, Manager, Order Processor, Read-only Viewer.
  - **Reports/Analytics** — sales by category/product/date range, best sellers, average turnaround time, CSV export.
  - **System Settings** — store info, delivery zones & rates, payment-gateway keys, notification templates, default production lead-time.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over create-edit forms; inline bilingual validation; confirmation dialog before delete (soft-delete/trash); toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination on every list. The exact personalization text for each order line must be impossible to lose or truncate between checkout and the production floor.

## 4. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the storefront/admin assets and all `/api/*` routes, backed by D1, KV, and R2 (for both product photos and customer-uploaded personalization images). GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`); Cloudflare is the runtime. Provide a Mermaid architecture diagram showing browsers → Worker → D1/KV/R2, outbound calls to payment/courier/notification providers, and the GitHub → Actions → Cloudflare deploy path.

## 5. Data Models
Provide schema tables for: `products`, `categories`, `orders`, `order_items` (including a `personalization` field: text, font/color choice, uploaded-image reference), `proof_approvals` (order item id, preview image, status, approved/rejected at), `customers`, `addresses`, `reviews`, `coupons`, `admins`, `inventory_log`, `delivery_zones`.

**SKU & Invoice Numbering (required):**
- Every base product gets an auto-generated, unique **SKU** on creation, following a pattern such as `GFT-[CategoryCode]-[Sequence]` (e.g. `GFT-MUG-0014`) — personalization data lives on the order item, not the SKU, since the base product doesn't change. Enforce uniqueness at the database level.
- Every confirmed order gets an auto-generated, unique, sequential **invoice number**, e.g. `INV-GFT-YYYYMMDD-####`, generated at order confirmation. Provide a downloadable/printable PDF invoice per order with itemized SKUs, the personalization summary, quantities, unit prices, discounts, delivery fee, and total.

**Additional tables for fraud/marketing/premium features:** `abandoned_checkouts`; `order_confirmation_attempts`; `return_requests` (note: returns on personalized items need a distinct "misprint/our error" vs. "customer changed their mind" reason, since a confirmed-correct custom item usually isn't returnable); `stock_notify_requests`; `referral_codes`. Add `utm_source`/`utm_medium`/`utm_campaign` and `confirmation_method` to `orders`; add `total_orders`, `delivered_count`, `refused_or_returned_count`, and `risk_level` to `customers`.

Include one worked example: an **Order** model as a TypeScript interface, where each order item includes `sku`, a `personalization` object (text, font, color, imageUrl), and the order includes `invoiceNumber`.

## 6. Payments, Delivery & Third-Party Integrations
- Payments: COD, bKash, Nagad, Rocket, card via SSLCommerz (or similar).
- Delivery: Steadfast as primary courier, Pathao Courier and RedX as alternatives; tracking-ID sync and status webhooks.
- Live delivery-fee calculation by Division/District/Upazila.
- SMS/WhatsApp order-confirmation, proof-approval request, and status-update notifications.
- Optional: Cloudflare Workers AI to auto-suggest a few personalization phrasings/occasion messages the customer can pick or edit.

## 7. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover), scoped for retail e-commerce: password hashing, HTTPS/HSTS, RBAC on every admin action, secrets in Wrangler secrets, PCI-scope minimization via hosted payment redirects, admin audit logging, rate-limiting on auth/checkout, an incident-response outline, and a D1 backup/restore procedure. Customer-uploaded images must be scanned/validated server-side (file type, size limit) before storage.

## 8. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E covering the full customize → preview → checkout → proof-approval flow specifically, since that's the part most likely to break; JSON fixtures for product-list, create-order, and admin-login endpoints; TypeScript build check required before deploy.

## 9. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx) for the admin; the storefront's Live Customizer will need a small canvas/preview layer (plain JS canvas or a lightweight library is sufficient — avoid a heavy page-builder dependency). A small charting library (Chart.js) for analytics. Full usability on tablet/large phone, and defined loading/empty/error states for every view.

## 10. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (products, orders, customers, auth, admin, proofs)
│   │   ├── lib/              (payment, courier, sms integrations, sku + invoice generators, image upload)
│   │   └── index.ts
│   ├── migrations/
│   └── wrangler.toml
├── public/                   (includes the Live Customizer script)
├── admin/
├── tests/
├── .github/workflows/
└── docs/
```

## 11. Development Environment & Deployment Setup
Node.js version, `wrangler` CLI install/login, `wrangler d1 create`, KV namespace, R2 bucket (for product photos and customer uploads), sample `wrangler.toml` and `.dev.vars`, GitHub branch strategy, a GitHub Actions workflow that tests/builds/migrates/deploys on push to `main`, and custom-domain setup via Cloudflare DNS.

## 12. SEO, Performance & Marketing
Meta tags, Open Graph/Twitter cards, sitemap.xml, robots.txt, `Product` JSON-LD for listings, Core Web Vitals targets for mobile, WhatsApp click-to-chat, Facebook/Instagram Shop feed, occasion-based landing pages (Eid gifts, wedding gifts) for seasonal SEO.

## 13. Suggested Product Categories & Content Plan
Personalized Jewelry, Custom Mugs & Drinkware, Photo Frames & Albums, Custom Apparel, Engraved Gifts, Occasion Gift Sets (Eid, Wedding, Birthday) — adjustable starting points.

## 14. Phased Roadmap
- **Phase 1 (MVP):** catalog, cart with basic text personalization (no live preview yet), COD checkout, basic admin CRUD for products/orders/categories, SKU + invoice generation live.
- **Phase 2:** live visual customizer/preview, proof-approval workflow, bKash/Nagad/SSLCommerz, Steadfast API, coupons, reviews, reports.
- **Phase 3:** AI-assisted phrasing suggestions, occasion-based landing pages, loyalty program, PWA/offline support.

## 15. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code.

## 16. Fraud Prevention & Abandoned-Checkout Capture
COD-heavy markets see a meaningful share of fake or prank orders, and a lot of recoverable revenue sits in checkouts that never finish — and made-to-order items make a fake order especially costly, since materials get used before a refusal is known. Build for both:
- **Phone verification at checkout:** send an SMS OTP before an order can move past "Pending"; an unverified number blocks automatic confirmation.
- **Courier fraud-check lookup:** check the customer's phone number against a courier/fraud-checker service before confirming a COD order, and surface that history on the order screen.
- **Order-risk scoring:** a Low/Medium/High badge per customer; require a manual confirmation call before dispatch for anything above "Low" — and for personalized items, hold production (not just dispatch) until confirmation, so a refused order doesn't waste materials.
- **Trusted-customer fast lane:** skip manual confirmation for customers with a strong delivery history.
- **Velocity & bot checks:** flag multiple orders in a short window from the same phone/address/IP; add Cloudflare Turnstile to checkout.
- **Abandoned-checkout capture:** autosave partial checkout data (and, ideally, the in-progress personalization) server-side as the customer types; if unconfirmed within a set window, it surfaces in the admin dashboard as an **Abandoned Checkout** with follow-up actions.

## 17. Marketing, Ads & Conversion Tracking
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set from the browser and, in parallel, from the Worker via CAPI — shared `event_id` for deduplication, hashed phone/email for matching.
- **Google Ads conversion tracking + GA4:** standard e-commerce events plus a conversion tag on order confirmation.
- **Microsoft Clarity:** heatmaps and session recordings — genuinely useful here to see where people abandon the customizer.
- **UTM capture & attribution** stored against each order.
- **Occasion-based campaign landing pages** (a single hero + offer + one CTA) for Eid/wedding/birthday ad pushes.
- **Click-to-WhatsApp ad support** with referring-ad attribution.

## 18. Additional Premium Features
- **Abandoned-checkout recovery automation** (SMS/WhatsApp nudge).
- **Automated post-delivery review request**, timed a few extra days later than other niches to allow for production lead-time.
- **Back-in-stock notifications** for raw materials/blanks.
- **Return/refund self-service**, with the misprint-vs-change-of-mind distinction noted above.
- **Referral program.**
- **Admin two-factor authentication** for Super Admin and Manager.
- **Google Merchant Center feed.**
- **PWA push notifications**, including a proof-ready alert.
- **VAT/tax-inclusive invoicing**, off by default.
- **Honesty guardrail on urgency signals:** real stock/turnaround-time claims only, never fabricated.

## 19. Acceptance Checklist
- [ ] Bilingual storefront and admin, working language toggle
- [ ] Guest checkout with Division/District/Upazila address and live delivery-fee calculation
- [ ] COD works end-to-end; at least one MFS integration functional or clearly stubbed
- [ ] Every base product has a unique, correctly-formatted SKU generated automatically
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice showing the personalization
- [ ] The Live Customizer preview matches what actually gets sent to production, with no truncation
- [ ] Proof-approval orders cannot enter production before the customer approves
- [ ] A COD order can't reach "Confirmed" without OTP verification or a logged manual confirmation call
- [ ] Abandoned checkouts are visible and actionable in the admin dashboard
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id
- [ ] "Refused at delivery," "Returned," and "Cancelled" are tracked as distinct order outcomes
- [ ] A non-technical owner can complete first-time setup without outside help
- [ ] Every list view has a CSV export
- [ ] No secrets committed to the repository

## 20. Owner-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** first product with personalization fields configured, delivery zone, payment method, WhatsApp — one simple action per step.
- **A plain-language "Health Check" strip** on the dashboard home.
- **Plain-language risk badges, not scores:** 🟢 Trusted / 🟡 New / 🔴 Verify before shipping.
- **A "Needs your attention today" home view:** confirmation calls due, proofs awaiting approval, abandoned checkouts, low stock — ahead of chart-heavy analytics.
- **One-tap actions on every order:** `tel:` "Call customer" and a WhatsApp button with pre-written, editable bilingual templates, including a ready-made "please confirm your spelling" template.
- **Bulk actions with a plain confirmation.**
- **CSV/Excel export everywhere there's a list.**
- **Optional phone + OTP login** for day-to-day staff roles, password + 2FA kept for Super Admin/Manager.

## Output Format
Single structured document: labeled sections matching the numbering above; Mermaid diagrams where applicable; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- Treat COD plus MFS as the primary payment reality; card payments are secondary.
- The SKU and invoice-numbering system is a hard requirement, not optional — remember that personalization lives on the order item, not the SKU.
- Fraud-prevention checks and honest urgency-signal data are hard requirements too, and matter more here than in a stock-inventory store because materials are consumed per order.
- Flag any assumption (palette details, exact delivery-fee tiers, exact category list, exact lead-time) as adjustable and confirm with the requester before finalizing.