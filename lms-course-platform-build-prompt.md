# [Your Brand Name] — Premium LMS / Online Course Platform (A–Z Build Prompt)

> Replace `[Your Brand Name]` throughout before using. Example name directions: "Shikhon" ("shikhon" = learning), "Academy BD", "SkillVerse". This is a learning platform, not a physical-goods store — the payment/order system here is about course enrollment, not shipping.

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack online course (LMS) platform with a complete admin dashboard, built for genuine speed and a premium, non-generic look, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Online course / learning platform — course sales, student learning experience, certificates
- **Primary market:** Bangladesh learners (with an easy path to go international later), mobile-first, Bangla + English speaking

The backend must run on **Cloudflare** (Workers in Hono + TypeScript, D1, KV, R2, Cloudflare Stream for video), with source hosted on **GitHub** and deployed via GitHub Actions. Speed is a first-class requirement, not a nice-to-have — see Section 10 for exactly what that means architecturally.

The deliverable must include the following:

## 1. Brand, Purpose & Business Requirements
- Define the brand identity, a short tagline, and a premium "prestige academy" logo concept direction — this should not read as a generic template site.
- Decide and state: single-instructor/single-brand platform (your own courses only) vs. multi-instructor (several teachers, admin-onboarded) — this changes the data model and dashboard scope, so fix this before building. Default assumption below: admin-onboarded instructors, not an open self-serve instructor marketplace (that's a bigger, different build — flag it as a possible Phase 4 if wanted later).
- All customer-facing and admin UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- Business model: course purchase via bKash/Nagad/Rocket/card (SSLCommerz) as the core market; decide whether to also take international cards (Stripe) from day one or add it later if the audience turns out to be partly global.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.

## 2. Public Website — Design & Course Discovery
- **Visual style (premium, not generic):** deep emerald/forest-green base with warm gold accents and an ivory/cream background — a "prestige academy" palette (green = growth/learning, gold = achievement/certificates), serif display type for course titles and section headers paired with a clean, highly readable sans-serif body font, generous white space, a thin gold rule under section headers rather than heavy boxes or harsh gradients.
- **Per-category color tagging:** if courses span multiple subjects (Web Development, Design, Business, Language), give each its own small accent tag used consistently across cards and pages — a wayfinding cue, not a full re-theme.
- Site map: Home, Course List/Grid, Course Detail, Blog, Instructor profile pages, About/Contact, Search, Login/Register, Student "My Learning" area (Section 3), Cart/Checkout.
- **Homepage:** hero banner, the course grid (see below), a "why learn with us" trust strip, instructor highlights, student testimonials/ratings, blog teaser, newsletter signup, footer with policies and social links.
- **Course Grid (home + course list page) — built exactly to spec:** each card shows a banner image, course name, a short description, a **Read More** button, and either an **Enroll Now** button with the price, or — for unreleased courses — a **"Coming Soon"** badge in place of Enroll, with an optional "notify me when this launches" email capture instead of a price.
- **Course Detail page:** full description, curriculum/syllabus (modules → lessons, with free-preview lessons marked if offered), instructor name/photo linking to their profile, price (or Coming Soon state), course rating (average + count) and a reviews list, a certificate preview/mention ("Certificate on completion"), Enroll button, related courses.
- **Instructor profile page:** photo, bio, credentials, social/portfolio links, a list of their courses with ratings.
- **Blog:** a simple, fast content section for SEO and free-value articles — same bilingual, sector-tagged pattern as the course grid, kept lightweight (see Section 10).
- **Reviews & rating:** a star rating plus written review, **restricted to students who actually purchased/enrolled in that course** — this is a trust requirement, not a detail to skip.

## 3. Student Experience — Enrollment, Learning & Certificates
- **Login/registration:** email+password (and optionally Google login), with a simple "forgot password" flow.
- **My Learning:** every enrolled course with a progress bar (% of lessons completed); clicking in opens the course player.
- **Course player:** lesson list down one side (video, text, or downloadable-resource lessons), video playback via Cloudflare Stream (adaptive bitrate — see Section 10 for why this matters), a lesson can also simply embed an external video link (YouTube/Vimeo) where the instructor prefers that, and downloadable resource links/files per lesson; "mark complete" per lesson, auto-advancing progress.
- **Certificates:** once 100% of a course's lessons are marked complete, auto-generate a downloadable PDF certificate (student name, course name, completion date, brand name) with a **unique verification code/URL** that anyone can check to confirm it's genuine — this protects the certificate's value as the student's credential, and protects the brand from fake-certificate claims.
- **Order history:** past purchases, invoices, and certificates earned, all in one place.

## 4. Admin Dashboard — Description & Layout
- **Visual style:** same emerald/gold/ivory palette as the public site, calmer and denser for day-to-day data work, rounded cards, soft shadows.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Courses, Instructors, Enrollments/Orders, Students, Reviews, Blog, Coupons, Certificates, Chatbot/AI Settings, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with KPI cards (today's enrollments, revenue, new students, pending "Coming Soon" notify-list signups, average course rating), a monthly sales chart, a recent-enrollments table.
  - **Right sidebar:** search, admin profile dropdown, notifications (new enrollment, new review, certificate issued), staff online status.
- **CRUD modules:**
  - **Courses** — title, bilingual description (short + long), banner image, category/tags, price (or marked "Coming Soon" with no price shown), status (Draft/Coming Soon/Published/Archived), curriculum builder (modules → lessons, each lesson a video upload/Stream reference or external link, plus optional resource files), certificate template toggle, SEO fields.
  - **Instructors** — name, photo, bio, credentials, linked courses.
  - **Enrollments/Orders** — student, course(s), payment method/status, amount, auto-generated invoice.
  - **Students** — profile, enrolled courses and progress, certificates earned, block/unblock.
  - **Reviews** — moderate (approve/reject/reply); only ever generated from the enrolled-student-only rule in Section 2.
  - **Blog** — simple CRUD for posts, bilingual, with the same SEO-preview approach described in the news-publisher build in this library, kept lighter here since it's a secondary feature.
  - **Coupons/Discounts** — percentage/flat, minimum order, expiry, usage limits, optionally scoped to specific courses.
  - **Certificates** — a searchable log of every certificate ever issued, with its verification code, for support and audit purposes.
  - **Chatbot/AI Settings** — choose and configure the AI provider for the support chatbot (Section 5), with the same masked-key-storage pattern as the news-publisher build.
  - **Reports/Analytics** — revenue by course/date range, completion rates, best-rated courses, CSV export.
  - **Staff & Roles** — Super Admin, Content Manager (courses/blog), Support (students/reviews), Read-only Viewer.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over create-edit forms; inline bilingual validation; confirmation dialog before delete (soft-delete/trash); toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination on every list.

## 5. AI Support Chatbot
- A chat widget on the public site and inside the course player, answering common questions (course content, pricing, certificate process, refund policy) and, within a course, questions scoped to that course's own syllabus/description.
- **Provider:** default to **Cloudflare Workers AI**, since it runs in the same Cloudflare account with no separate billing relationship to set up and includes a free daily usage allowance suitable for a support chatbot's typical volume — genuinely the simplest "free" starting option given the rest of the stack. Offer **Gemini** as a swappable alternative/upgrade for higher-quality responses, using the same pluggable-provider pattern as the news-publisher build in this library (one internal AI-provider interface, a small adapter per provider, key stored as a Wrangler secret, masked in the admin UI, never shipped to the client).
- Keep the chatbot's scope honest: it should say "I'm not sure, here's how to contact support" rather than guessing on refund/legal/payment questions it isn't confidently grounded on.

## 6. Full-Stack Architecture
A hybrid architecture chosen specifically for speed:
- **Public marketing pages** (Home, Course List, Course Detail, Blog, Instructor pages) are rendered fast and lightweight directly by the Cloudflare Worker (Hono + TypeScript) — minimal JavaScript, aggressively cached at the edge (Cache API/KV), since these are the pages SEO and first-impression conversion depend on.
- **The logged-in experience** (My Learning, course player, admin dashboard) is a React (Vite) app, where richer interactivity is worth the extra JS weight because the user is already committed/authenticated, not a cold visitor who'll bounce on a slow load.
- Both are served by the same Worker, backed by D1 (relational data), KV (sessions/cache), R2 (images, resource files), and **Cloudflare Stream** for video (handles transcoding and adaptive-bitrate delivery automatically, which matters far more for perceived speed than hosting raw video files would).
- GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`). Provide a Mermaid architecture diagram showing the browser (marketing pages vs. the React app) → Worker → D1/KV/R2/Stream, the AI-provider call for the chatbot, the payment-gateway calls, and the GitHub → Actions → Cloudflare deploy path.

## 7. Data Models
Provide schema tables for: `courses` (status incl. Coming Soon, price, category), `modules`, `lessons` (type: video/Stream-id, external-link, text, resource-file), `instructors`, `enrollments`/`orders`, `order_items`, `students`, `reviews` (gated to enrolled students), `certificates` (student, course, issued-at, verification code), `coupons`, `blog_posts`, `ai_provider_configs` (masked keys only), `admins`/`staff`, `notify_signups` (for Coming Soon courses), `activity_log`.

**Invoice numbering:** every confirmed enrollment order gets an auto-generated, unique, sequential invoice number, e.g. `INV-[BrandCode]-YYYYMMDD-####`, with a downloadable PDF invoice.

Include one worked example: a **Course** model as a TypeScript interface, including `status: "draft" | "coming_soon" | "published" | "archived"`, a `modules` array with nested `lessons`, and the certificate-template reference.

## 8. Payments & Enrollment Flow
- Payment methods: bKash, Nagad, Rocket, card via SSLCommerz (or similar) as the primary market; optionally Stripe for international cards per the Section 1 decision.
- Enrollment is granted the instant payment is confirmed by the gateway (webhook-driven, not client-redirect-driven) — the student should never be left waiting or uncertain whether they're enrolled.
- Support course bundles/discounted multi-course packages at checkout, and coupon codes.
- Free courses (price = 0) skip the payment step entirely and enroll on a single click.

## 9. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover): password hashing, HTTPS/HSTS, RBAC on every admin action, every third-party key (payment gateway, AI provider) stored as a Wrangler secret and masked in the UI, admin audit logging, rate-limiting on login/checkout/chatbot endpoints, an incident-response outline, and a D1 backup/restore procedure. Video and resource files in R2/Stream should only be accessible to enrolled students (signed URLs or access tokens), not publicly guessable links — course content is the product being sold, so leaking it is a direct revenue loss, not just a security nicety.

## 10. Performance & Speed Requirements
This was explicitly asked for, so treat it as a real requirement with real techniques, not a vague goal:
- Marketing pages (Section 6) stay lightweight: no heavy client-side framework on the cold-entry pages, compressed/responsive images (WebP/AVIF), edge-cached HTML.
- Video through Cloudflare Stream, never raw video files from R2 — adaptive bitrate means a slow connection still gets a playable video instead of buffering.
- Core Web Vitals targets for mobile on every marketing page, checked as part of CI where feasible.
- Lazy-load below-the-fold content (reviews, related courses) rather than loading everything on first paint.
- Database queries for the course grid/list should be indexed and paginated from the start, not an afterthought once the catalog grows.

## 11. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E covering enroll → pay → access course → complete → certificate issued, since that's the full value chain; JSON fixtures for course-list, create-order, and admin-login endpoints; a Lighthouse/Core Web Vitals check in CI for the marketing pages; TypeScript build check required before deploy.

## 12. Dashboard Development Plan
React (Vite) for the student app and the admin CMS; a small charting library (Chart.js) for analytics; full usability on tablet for admins; defined loading/empty/error states everywhere, especially around video upload/processing status.

## 13. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (courses, enrollments, students, instructors, reviews, certificates, chatbot, admin)
│   │   ├── lib/
│   │   │   ├── ai-providers/   (workers-ai.ts, gemini.ts)
│   │   │   ├── certificate.ts  (PDF + verification code generation)
│   │   │   └── payment.ts
│   │   └── index.ts
│   ├── migrations/
│   └── wrangler.toml
├── marketing/                 (lightweight public pages: home, course list/detail, blog)
├── app/                       (React: My Learning, course player)
├── admin/                     (React admin CMS)
├── tests/
├── .github/workflows/
└── docs/
```

## 14. Development Environment & Deployment Setup
Node.js version, `wrangler` CLI install/login, `wrangler d1 create`, KV namespace, R2 bucket, Cloudflare Stream enabled on the account, `wrangler secret put` for payment-gateway and AI-provider keys, sample `wrangler.toml` and `.dev.vars`, GitHub branch strategy, a GitHub Actions workflow that tests/builds/migrates/deploys on push to `main`, and custom-domain setup via Cloudflare DNS.

## 15. SEO, Marketing & Analytics
Meta tags, Open Graph/Twitter cards, sitemap.xml, robots.txt, `Course` JSON-LD (Google explicitly supports course structured data, including price and rating) on every course page, GA4, Microsoft Clarity, Meta Pixel + Conversions API if running paid social, WhatsApp click-to-chat for pre-enrollment questions.

## 16. Suggested Course Categories & Content Plan
Decide the actual subject focus and list starting categories (e.g. Web Development, Digital Marketing, Design, Language Learning, Business/Freelancing) — adjustable starting points, not fixed.

## 17. Phased Roadmap
- **Phase 1 (MVP):** course grid/detail, enrollment + payment, basic admin CRUD for courses/students/orders, simple video-link lessons, invoice generation.
- **Phase 2:** Cloudflare Stream video hosting, certificates with verification, reviews/ratings, coupons, blog, the AI chatbot.
- **Phase 3:** quizzes/assessments, course bundles, international payment option, loyalty/referral program, PWA/offline lesson download.

## 18. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code; never commit a real API or payment key, including in test files.

## 19. Additional Premium Features
- **Course bundles** at a combined discount.
- **Drip content** (optional: release modules on a schedule rather than all at once).
- **Referral program** for students.
- **"Coming Soon" notify-list** turned into an automatic launch-day email/SMS blast.
- **Instructor Q&A** on individual lessons (students ask, instructor or admin answers).
- **Honesty guardrail:** no fabricated "X students enrolled" or "X watching now" counters — any such number must reflect real data.

## 20. Owner/Admin-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** add your first course (even with placeholder lessons), connect a payment method, connect the chatbot — one simple action per step.
- **A plain-language "Health Check" strip** on the dashboard home ("Payment: connected" / "Chatbot: not connected yet").
- **A "Needs your attention today" home view:** new enrollments, pending reviews to moderate, Coming Soon notify-list size, support chatbot escalations — ahead of chart-heavy analytics.
- **One-tap actions:** approve/reject a review, mark a "Coming Soon" course live, reissue a certificate.
- **CSV export everywhere there's a list** — enrollments, students, certificates, reviews.
- **A masked-key display and "Test connection" button** for the chatbot's AI provider, so a non-technical owner can confirm it's wired up correctly.

## 21. Acceptance Checklist
- [ ] Bilingual public site, student app, and admin, working language toggle
- [ ] Course grid shows banner, name, description, Read More, and either Enroll+price or a Coming Soon state with notify-me
- [ ] Enrollment payment works end-to-end via at least one local gateway; enrollment is granted only after gateway confirmation
- [ ] A student can watch a course's lessons, mark progress, and receive an auto-generated certificate with a working verification link at 100% completion
- [ ] Reviews can only be left by students enrolled in that specific course
- [ ] The chatbot works with Workers AI by default and can be switched to Gemini without code changes
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] Marketing pages hit solid Core Web Vitals scores on mobile; video plays via Cloudflare Stream, not raw file links
- [ ] Course/resource/video access is restricted to enrolled students, not publicly guessable
- [ ] A non-technical owner can add a course and connect the chatbot without developer help
- [ ] No fabricated enrollment/viewer counts anywhere on the site
- [ ] No secrets committed to the repository

## Output Format
Single structured document: labeled sections matching the numbering above; a Mermaid architecture diagram; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- The enrolled-only review rule and the certificate-verification-code system are hard requirements — they're what keeps ratings and certificates meaningful rather than gameable.
- Speed is a hard requirement specifically on the marketing pages (Section 10) — don't let a heavy admin-style framework leak into the course-browsing experience just because it's convenient to build everything the same way.
- Confirm with the requester, before finalizing, the single-vs-multi-instructor decision from Section 1 and whether international payment is needed at launch or later.
