---
id: e1518760-bf22-44b7-87b4-c8cc8b04fc6d
title: Coffee
language: markdown
tags: ''
selection: null
isPinned: false
customIcon: ☎️
timestamp: 1781531072659
---
# Coffee Cafe — Full-Stack E-Commerce Application


A full-stack coffee shop application with **Next.js 16**, **TypeScript**, **Tailwind CSS**, **Supabase** (auth, database, realtime, storage), and **Midtrans** payment gateway. Customers can browse products, manage a cart, checkout with bank transfer/credit card/COD, and track orders. Admins get a full dashboard with CRUD for products/blog/orders, analytics charts, and payment approval.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How to Run](#how-to-run)
- [Environment Variables](#environment-variables)
- [Database Setup](#database-setup)
- [Routes](#routes)
- [Role System](#role-system)
- [Data Flow](#data-flow)
- [Components Walkthrough](#components-walkthrough)
- [Supabase Schema](#supabase-schema)
- [Libraries & Dependencies](#libraries--dependencies)
- [Customization & Theming](#customization--theming)
- [License](#license)

---

## Features

- **Authentication** — Email/password sign-up, login, password reset via Supabase Auth
- **Role-based access** — Admin, Manager, Customer roles with different dashboard permissions
- **Product catalog** — Dynamic menu from Supabase `products` table, filterable by category (Coffee/Dessert)
- **Shopping cart** — Persistent cart via localStorage, quantity controls, WhatsApp order option
- **Checkout** — Address form with Zod validation, multiple payment methods (Bank Transfer, Credit Card, COD)
- **Payment gateway** — Midtrans Snap popup for VA/credit card, webhook for status updates
- **Order management** — Dashboard orders table with search, filter, sort, pagination, manual approve
- **Order detail page** — Full order view with items, customer info, shipping, payment breakdown
- **Realtime updates** — Supabase realtime subscriptions sync dashboard when orders change
- **Analytics dashboard** — Revenue chart (Recharts), order status pie chart, top products bar chart
- **Blog** — CRUD blog posts with Markdown content, image upload to Supabase Storage
- **Product management** — CRUD products with image upload, shipping methods toggle
- **Store settings** — WhatsApp number, Midtrans API keys configuration via dashboard
- **Contact form** — Messages saved to Supabase, viewable in dashboard
- **Responsive design** — Mobile-first layout with sidebar navigation on dashboard
- **Guest checkout** — Users can order without signing in

---

## Tech Stack

| Technology | Purpose |
|---|---|
| **Next.js 16** | React framework, App Router, API routes, server components |
| **React 19** | UI library, hooks, client components |
| **TypeScript 5.6** | Type safety, interfaces |
| **Tailwind CSS 3.4** | Utility CSS, custom theme, responsive design |
| **Supabase** | Auth, PostgreSQL database, realtime subscriptions, file storage |
| **Midtrans** | Payment gateway (Snap popup, webhook) |
| **Zod 4** | Schema validation (checkout form, product/blog CRUD) |
| **Recharts** | Charts for analytics dashboard |
| **react-markdown** | Blog post rendering |
| **react-icons** | Icons throughout the app |
| **AOS** | Scroll animations on landing page |
| **react-slick** | Testimonials carousel |
| **nextjs-toploader** | Navigation progress bar |

---

## Project Structure

```
coffeeshop-ui-2/
├── public/                          # Static assets (images, icons, manifest)
│   ├── favicon.ico
│   ├── manifest.json
│   ├── coffee_logo.png
│   ├── coffee2.png, coffee-white.png
│   ├── coffee-texture.jpg, coffee-beans-bg.png, coffee-footer.jpg
│   ├── hero_bg_*.png, promo_banner_*.png, newsletter_bg_*.png
│   ├── product images (dalgona, ice coffee, cake, etc.)
│   ├── app_store.png, play_store.png
│   └── media__*.png
├── src/
│   ├── app/
│   │   ├── layout.tsx               # Root layout: CartProvider, Navbar, NextTopLoader, SEO
│   │   ├── page.tsx                 # Home page: Hero, MenuSection, Blog, Contact, Footer
│   │   ├── globals.css              # Tailwind + custom styles
│   │   ├── loading.tsx              # Root loading state
│   │   ├── robots.ts, sitemap.ts    # SEO
│   │   │
│   │   ├── login/page.tsx           # Login with Supabase auth
│   │   ├── register/page.tsx        # Registration + user_profiles insert
│   │   ├── forgot-password/page.tsx # Password reset via OTP email
│   │   │
│   │   ├── checkout/page.tsx        # Checkout form with Midtrans Snap
│   │   ├── checkout/success/page.tsx # Post-checkout: VA number, countdown timer
│   │   │
│   │   ├── blog/[id]/page.tsx       # Blog post detail (Markdown)
│   │   │
│   │   ├── dashboard/
│   │   │   ├── layout.tsx           # Dashboard sidebar + navbar wrapper
│   │   │   ├── page.tsx             # Orders table, search, filter, approve
│   │   │   ├── analytics/page.tsx   # Revenue/status/products charts
│   │   │   ├── products/page.tsx    # CRUD products
│   │   │   ├── orders/[id]/page.tsx # Order detail view
│   │   │   ├── blog/page.tsx        # CRUD blog posts
│   │   │   ├── contacts/page.tsx    # Contact messages table
│   │   │   └── settings/page.tsx    # Profile, password, store, API keys
│   │   │
│   │   └── api/
│   │       ├── checkout/route.ts     # Midtrans Snap transaction creation
│   │       └── webhook/midtrans/route.ts # Payment status webhook
│   │
│   ├── components/
│   │   ├── Navbar/          # Responsive navbar with auth, cart badge
│   │   ├── Hero/            # Landing hero section
│   │   ├── MenuSection/     # Dynamic product grid from Supabase
│   │   ├── CartDrawer/      # Sliding cart with checkout/WhatsApp
│   │   ├── Checkout/        # Checkout form + SuccessContent
│   │   ├── Categories/      # Category filter icons
│   │   ├── LatestPosts/     # Blog cards from Supabase
│   │   ├── Contact/         # Contact form
│   │   ├── Services/        # Static coffee cards (deprecated)
│   │   ├── Banner/          # Premium blend banner
│   │   ├── AppStore/        # App store promo
│   │   ├── Newsletter/      # Email subscription
│   │   ├── Gallery/         # Image grid
│   │   ├── Testimonials/    # Carousel (react-slick)
│   │   ├── PromoBanner/     # CTA banner
│   │   ├── Footer/          # Site footer
│   │   ├── CookieBanner/    # Cookie consent
│   │   ├── BackToTop/       # Scroll-to-top button
│   │   │
│   │   └── dashboard/
│   │       ├── DashboardProvider.tsx  # Role + auth context
│   │       ├── Sidebar.tsx            # Role-based nav
│   │       ├── AdminNavbar.tsx        # Top bar with Dropdown
│   │       ├── Dropdown.tsx           # User menu with logout
│   │       ├── DashboardContent.tsx   # Orders table + approve + realtime
│   │       ├── AnalyticsContent.tsx   # Recharts dashboards
│   │       ├── ProductsContent.tsx    # CRUD product modals
│   │       ├── BlogsContent.tsx       # CRUD blog modals
│   │       ├── ContactsContent.tsx    # Messages table
│   │       ├── SettingsContent.tsx    # Profile/store/API settings
│   │       └── Pagination.tsx         # Page navigation
│   │
│   └── ui/
│       └── ConfirmModal.tsx           # Reusable confirm dialog
│   │
│   ├── context/
│   │   └── CartContext.tsx            # Cart state + localStorage
│   │
│   ├── lib/
│   │   ├── supabase.ts               # Browser Supabase client
│   │   ├── supabaseAdmin.ts          # Server admin client (service role)
│   │   ├── supabase/server.ts        # Server component client
│   │   ├── supabase/middleware.ts     # Auth middleware for dashboard
│   │   └── constants.ts              # Asset paths, contact info
│   │
│   ├── data/
│   │   ├── menu.ts                   # Nav links
│   │   ├── services.ts               # Static service cards
│   │   ├── testimonials.ts           # Customer reviews
│   │   ├── footer.ts                 # Footer links
│   │   ├── gallery.ts                # Gallery images
│   │   └── opening-hours.ts          # Store hours
│   │
│   └── types/
│       └── index.ts                  # Shared TypeScript interfaces
│
├── supabase/
│   └── tables.sql                    # Full schema: tables, RLS, functions, cron
│
├── next.config.mjs                   # Strict mode, image remotePatterns
├── tailwind.config.js                # Custom theme (colors, fonts, animations)
├── tsconfig.json                     # Path alias @/*
├── postcss.config.cjs                # Tailwind + autoprefixer
├── eslint.config.mjs                 # ESLint 9 flat config
├── vercel.json                       # Vercel deployment config
├── netlify.toml                      # Netlify deployment config
├── .env.example                      # Required env vars template
└── package.json
```

---

## How to Run

**Prerequisites:** Node.js 20+.

1. **Clone and install dependencies:**

   ```bash
   git clone <repository-url>
   cd coffeeshop-ui-2
   npm install
   ```

2. **Set up environment variables:**

   Copy `.env.example` to `.env.local` and fill in your Supabase and Midtrans credentials:

   ```bash
   cp .env.example .env.local
   ```

   See [Environment Variables](#environment-variables) below for details.

3. **Set up the database:**

   Run the SQL in `supabase/tables.sql` in your Supabase project's SQL Editor. This creates all tables, RLS policies, helper functions, and the hourly cleanup cron job.

4. **Start the development server:**

   ```bash
   npm run dev
   ```

   Open [http://localhost:3000](http://localhost:3000).

5. **Build for production:**

   ```bash
   npm run build
   npm run start
   ```

6. **Lint:**

   ```bash
   npm run lint
   ```

---

## Environment Variables

Create a `.env.local` file in the project root with these variables:

```env
# Supabase (required)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# Midtrans (required for payments)
NEXT_PUBLIC_MIDTRANS_CLIENT_KEY=SB-Mid-client-your-client-key
MIDTRANS_SERVER_KEY=SB-Mid-server-your-server-key
```

**Where to find them:**

- **Supabase:** Project Settings → API → Project URL + anon/public key
- **Midtrans Client Key:** Midtrans Dashboard → Settings → Access Keys → Client Key
- **Midtrans Server Key:** Same page → Server Key (keep secret, server-side only)

Midtrans keys can also be configured in-app via Dashboard → Settings → API Keys (stored in the `api_keys` and `store_settings` tables). If not set via env vars, the in-app settings are used as fallback.

---

## Database Setup

1. Open your Supabase project's SQL Editor.
2. Paste and run all of `supabase/tables.sql`.
3. This creates:
   - **7 tables** — `user_profiles`, `orders`, `products`, `blogs`, `contact_messages`, `store_settings`, `api_keys`
   - **RLS policies** — Row-level security for each table
   - **Storage buckets** — `products`, `blogs`, `avatars` (public read, auth write)
   - **Helper functions** — `get_order_details()`, `search_orders()`, `get_auth_role()`
   - **Cron job** — Auto-deletes expired unpaid orders after 24 hours
4. Run `INSERT INTO user_profiles` to assign yourself as the first `admin`.

---

## Routes

### Public Routes

| Route | Description |
|---|---|
| `/` | Landing page with hero, menu, blog, contact |
| `/login` | Email/password login |
| `/register` | New user registration |
| `/forgot-password` | Password reset via OTP email |
| `/checkout` | Checkout form with shipping + payment |
| `/checkout/success?orderId=...` | Post-checkup: VA number, countdown, order summary |
| `/blog/[id]` | Blog post detail with Markdown rendering |

### Dashboard Routes (require authentication)

| Route | Access | Description |
|---|---|---|
| `/dashboard` | All roles | Orders table with search, filter, approve |
| `/dashboard/analytics` | Admin, Manager | Revenue chart, order status pie, top products |
| `/dashboard/products` | Admin, Manager | CRUD product management |
| `/dashboard/orders/[id]` | All roles | Full order detail view |
| `/dashboard/blog` | Admin, Manager | CRUD blog post management |
| `/dashboard/contacts` | Admin, Manager | Contact messages inbox |
| `/dashboard/settings` | All roles | Profile, password, store settings, API keys |

### API Routes

| Route | Method | Description |
|---|---|---|
| `/api/checkout` | POST | Creates Midtrans Snap transaction token |
| `/api/webhook/midtrans` | POST | Midtrans payment status notification (signed) |

---

## Role System

| Role | Permissions |
|---|---|
| **Admin** | Full access: orders, approve payments, analytics, CRUD products/blog, settings, contacts |
| **Manager** | Same as Admin but cannot manage API keys in settings |
| **Customer** | Dashboard limited to viewing own orders, no approve/CRUD access |

Roles are stored in the `user_profiles` table and checked via middleware for dashboard route protection. The middleware at `src/lib/supabase/middleware.ts` redirects unauthorized users.

---

## Data Flow

### Auth Flow
1. User registers/logs in via Supabase Auth (email/password)
2. On login, `user_profiles` is queried for role
3. Middleware checks session + role for dashboard routes
4. DashboardProvider exposes `role` and `loading` state to all dashboard components

### Cart & Checkout Flow
1. User adds items to cart (stored in localStorage via CartContext)
2. On checkout, form is validated with Zod schema
3. Order is inserted into Supabase `orders` table
4. For COD: redirect to success page immediately
5. For Bank Transfer/Credit Card: Midtrans Snap popup opens
6. On success/pending: redirect to success page
7. Midtrans webhook updates `payment_status` in the background

### Realtime Updates
- Dashboard subscribes to `postgres_changes` on the `orders` table
- When an order is created, updated, or deleted, the dashboard re-fetches automatically
- This keeps the orders table, stats, and revenue in sync without manual refresh

### Payment Flow
1. Checkout → order created with `payment_status: "Awaiting Payment"` or `"Pending (COD)"`
2. Midtrans Snap popup → user completes payment
3. Midtrans sends webhook to `/api/webhook/midtrans`
4. Webhook verifies signature, updates `payment_status` to `"Paid"` or `"Failed"`
5. Dashboard realtime subscription picks up the change
6. Admin can also manually approve via the "Approve" button in dashboard

---

## Components Walkthrough

### Key Client Components

- **Navbar** (`src/components/Navbar/Navbar.tsx`) — Auth-aware top bar with cart badge count, mobile menu, scroll-spy active section, hides on checkout/dashboard routes.

- **MenuSection** (`src/components/MenuSection/MenuSection.tsx`) — Dynamic product grid reading from Supabase `products` table. Filter tabs (All/Coffee/Dessert), "Add to Cart" with toast feedback, image from Supabase Storage.

- **CartDrawer** (`src/components/CartDrawer/CartDrawer.tsx`) — Slide-over cart panel with quantity increment/decrement, per-item remove, subtotal, checkout button, and WhatsApp order link.

- **Checkout** (`src/components/Checkout/Checkout.tsx`) — Full checkout form with Zod validation, address fields, courier selection, payment method radio group (Bank Transfer, Credit Card, COD), Midtrans Snap integration.

- **DashboardContent** (`src/components/dashboard/DashboardContent.tsx`) — Orders table with search (by ID/name/phone), status filter (All/Awaiting Payment/Paid), sort (date/amount), pagination, approve payment modal, Supabase realtime subscription.

- **AnalyticsContent** (`src/components/dashboard/AnalyticsContent.tsx`) — Recharts dashboard with revenue line chart, order status donut chart, top products horizontal bar chart, summary metric cards.

### Shared UI

- **ConfirmModal** (`src/components/ui/ConfirmModal.tsx`) — Reusable modal for approve payment confirmation and checkout alerts.

---

## Supabase Schema

The full database schema is in `supabase/tables.sql`. Key tables:

| Table | Purpose |
|---|---|
| `user_profiles` | Extended user info + role (admin/manager/customer) |
| `orders` | Customer orders with items (JSONB), payment/order status, courier, address |
| `products` | Menu items with name, price, image, shipping methods (JSONB) |
| `blogs` | Blog posts with Markdown content, excerpt, image |
| `contact_messages` | Contact form submissions |
| `store_settings` | WhatsApp number, Midtrans client key |
| `api_keys` | Midtrans server key (encrypted at rest) |

**RLS Policies:** All tables have row-level security. Admins/managers have full read access. Customers see only their own orders and profiles. Guest users can insert orders (checkout) and contact messages.

**Helper Functions:**
- `get_order_details(p_order_id)` — Security definer function for guest order lookup
- `search_orders(search_term)` — ILIKE search across order ID, customer name, phone

---

## Libraries & Dependencies

| Package | Role |
|---|---|
| **next** | Framework: App Router, API routes, Image optimization |
| **react**, **react-dom** | UI rendering |
| **@supabase/ssr**, **@supabase/supabase-js** | Auth, database, realtime, storage |
| **midtrans-client** | Midtrans payment gateway (server-side) |
| **zod** | Form and API validation |
| **recharts** | Analytics charts |
| **react-markdown**, **remark-gfm** | Blog post Markdown rendering |
| **react-slick**, **slick-carousel** | Testimonials carousel |
| **aos** | Scroll animations |
| **react-icons** | Full icon set (Fa, Gr, Io, Gi, Si) |
| **nextjs-toploader** | Page transition progress bar |
| **react-spinners** | Loading indicators |
| **@tailwindcss/typography** | Markdown content styling |
| **tailwindcss**, **postcss**, **autoprefixer** | CSS pipeline |
| **typescript** | Type checking |
| **eslint**, **eslint-config-next** | Linting |

---

## Customization & Theming

- **Colors & fonts:** Edit `tailwind.config.js` — `theme.extend.colors` (primary: `#854d3d`, secondary: `#4a1e1b`, brandDark: `#270c03`) and `theme.extend.fontFamily` (Poppins, Pacifico, Sriracha).

- **Store info:** Update `src/lib/constants.ts` for app name, slogan, contact details, asset paths.

- **Content:** Edit data files in `src/data/` for nav links, testimonials, gallery, opening hours. Products and blogs are managed via the dashboard.

- **Animations:** AOS is configured in `src/app/page.tsx`. The `.spin` class is in `globals.css`.

- **Supabase:** All backend logic is in `src/lib/supabase*.ts` and `supabase/tables.sql`. Swap to your own Supabase project by changing `.env.local` credentials.

- **Midtrans:** Payment keys are configurable in Dashboard → Settings → API Keys, or via environment variables.

---

## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT). Feel free to use, modify, and distribute the code as per the terms of the license.

---

## Happy Coding! 🎉

This is an **open-source project** — feel free to use, enhance, and extend this project further.

If you have any questions or want to share your work, reach out via GitHub or at [https://www.arnobmahmud.com](https://www.arnobmahmud.com).

**Enjoy building and learning!** 🚀

Thank you! 😊

---

