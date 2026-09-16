# WorkOrbit — HRM & Workforce Management SaaS

WorkOrbit is a full-featured, multi-module Human Resource Management (HRM) SaaS platform built on **Laravel 12** (backend) and **React 19 + Inertia.js + TypeScript** (frontend), styled with **Tailwind CSS v4** and **shadcn/Radix UI** components. It ships with a guided web installer, role & permission management, subscription billing with 20+ payment gateways, and dozens of HR modules covering the full employee lifecycle — from recruitment to offboarding.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Requirements](#requirements)
- [Installation](#installation)
  - [Option A — Guided Web Installer (recommended)](#option-a--guided-web-installer-recommended)
  - [Option B — Manual / CLI Installation](#option-b--manual--cli-installation)
- [Environment Configuration](#environment-configuration)
- [Payment Gateway Configuration](#payment-gateway-configuration)
- [Queues, Scheduler & Storage](#queues-scheduler--storage)
- [Local Development](#local-development)
- [Building for Production](#building-for-production)
- [Testing](#testing)
- [Project Structure](#project-structure)
- [Roles & Permissions](#roles--permissions)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Features

### Core HR & Organization
- Company / branch / department / designation management (multi-branch support)
- Employee records, documents, contracts, transfers, promotions, resignations, terminations
- Role-based access control with granular permissions (Spatie Permission)
- User impersonation for support/admin (Laravel Impersonate)
- IP restriction & login history / audit trail

### Recruitment & Onboarding
- Job requisitions, job postings & categories, public careers page
- Candidate pipeline, sourcing, assessments, interviews & interview feedback
- Offer letters & offer templates, candidate onboarding checklists
- Document acknowledgment workflows

### Attendance, Leave & Time
- Shifts, attendance policies, attendance records & regularization
- Biometric attendance integration
- Leave types, leave policies, leave applications & balance tracking/sync
- Time entry / timesheet tracking

### Payroll & Compensation
- Salary components, employee salary structures
- Payroll runs & payroll entries, payslip generation (PDF)
- Benefits & payout requests

### Performance & Growth
- Goals & goal types, performance indicators & categories
- Employee review cycles & review ratings
- Awards & award types, warnings, complaints
- Training programs, sessions, attendance & assessments
- Employee contracts & contract renewals/templates

### Assets, Meetings & Operations
- Asset types, assignments, maintenance & depreciation tracking
- Meeting rooms, meetings, attendees & meeting minutes
- Action items / task checklist tracking
- Trips & trip expense management
- Announcements, newsletters, document templates (NOC, joining letter, experience certificate)

### Platform / SaaS Layer
- Subscription **Plans**, **Plan Orders**, **Plan Requests**, **Coupons**
- Referral program & referral settings
- Landing page builder with custom pages
- Multi-currency support, multi-language (i18next) UI
- Email templates & multi-language email templates, SMTP test tool
- Webhooks, system/brand/SEO/reCAPTCHA/cookie-consent settings
- Google Calendar integration, OpenAI (ChatGPT) integration
- Media library (Spatie Media Library) for file/document management

### Payments (20+ gateways supported out of the box)
Stripe, PayPal, Razorpay, PayTabs, Mollie, Authorize.Net, Cashfree, CoinGate (crypto), FedaPay, Flutterwave, Iyzipay, Khalti, Midtrans, MercadoPago, Nepalste, Ozow, Paiement, PayHere, PayTR, Paystack, Skrill, Tap, ToyyibPay, Xendit, YooKassa (YooMoney), Aamarpay, Easebuzz, CinetPay, Bank Transfer.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend framework | Laravel 12 (PHP 8.2+) |
| Frontend bridge | Inertia.js v2 |
| Frontend framework | React 19 + TypeScript |
| Styling / UI | Tailwind CSS v4, Radix UI, shadcn-style components, `lucide-react` icons |
| Build tool | Vite 6 |
| Auth & Access | Laravel built-in auth, Spatie `laravel-permission`, `laravel-impersonate` |
| Media | Spatie `laravel-medialibrary` |
| PDF / Office | `barryvdh/laravel-dompdf`, `phpoffice/phpspreadsheet`, `phpoffice/phpword` |
| Calendar/UI extras | FullCalendar, `@hello-pangea/dnd`, TipTap rich text editor |
| Charts | Recharts |
| i18n | i18next / react-i18next |
| Installer | `rachidlaasri/laravel-installer` |
| Error tracking | Larabug / Sentry (optional) |
| Testing | Pest PHP |

---

## Requirements

- **PHP** >= 8.2 with extensions: `openssl`, `pdo`, `mbstring`, `tokenizer`, `json`, `curl`, `fileinfo`, `gd`, `zip`, `xml`
- **Composer** 2.x
- **Node.js** >= 18 and **npm**
- **MySQL** >= 5.7 / MariaDB (or any DB supported by Laravel)
- **Web server**: Apache (with `mod_rewrite`) or Nginx
- Writable permissions on: `storage/`, `bootstrap/cache/`, `public/storage/`

---

## Installation

You can install WorkOrbit either through its built-in **web installer wizard** (easiest, ideal for shared hosting/cPanel) or manually via the command line (recommended for developers).

### Option A — Guided Web Installer (recommended)

1. Upload/extract the project to your web server so the **`public/`** folder is your document root (or point your vhost to `public/`).
2. Make sure these are writable by the web server:
   ```bash
   chmod -R 775 storage bootstrap/cache public/storage storage/uploads resources/lang
   ```
3. Visit your domain in a browser — you'll be redirected to `/installer`.
4. Follow the wizard:
   - **Requirements & Permissions check** — confirms PHP version/extensions and folder permissions.
   - **Environment setup** — enter database credentials and app URL; the installer writes `.env` for you.
   - **Migrate & seed** — the installer runs migrations and seeds default roles, permissions, plans and settings.
   - **Final step** — creates the admin account and generates the `APP_KEY`.
5. Once finished, the installer locks itself via the `storage/installed` marker file. Delete/rename `public/installer` afterward if you want to fully disable re-running it.

### Option B — Manual / CLI Installation

```bash
# 1. Clone or extract the project
cd workorbit

# 2. Install PHP dependencies
composer install --optimize-autoloader --no-dev
# (use `composer install` without --no-dev for a local dev environment)

# 3. Install JS dependencies
npm install

# 4. Copy and configure the environment file
cp .env.example .env   # if .env.example is not present, create .env manually (see below)

# 5. Generate the application key
php artisan key:generate

# 6. Configure your database in .env, then run migrations
php artisan migrate --seed

# 7. Link the public storage disk
php artisan storage:link

# 8. Build frontend assets
npm run build

# 9. Set folder permissions
chmod -R 775 storage bootstrap/cache public/storage storage/uploads

# 10. (Optional) Mark the installer as completed so it doesn't redirect to /installer
echo "installed" > storage/installed

# 11. Serve the app
php artisan serve
```

Then create an admin user (if not seeded already):

```bash
php artisan tinker
>>> \App\Models\User::create(['name' => 'Admin', 'email' => 'admin@example.com', 'password' => bcrypt('password')]);
```

...and assign the admin role via the Roles UI or a seeder as appropriate for your setup.

---

## Environment Configuration

Minimum required variables in your `.env`:

```env
APP_NAME="WorkOrbit"
APP_ENV=production
APP_KEY=                # generated via php artisan key:generate
APP_DEBUG=false
APP_URL=https://your-domain.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=workorbit
DB_USERNAME=workorbit_user
DB_PASSWORD=your_secure_password

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_CONNECTION=sync   # use "database" or "redis" in production for background jobs

MAIL_MAILER=smtp
MAIL_HOST=smtp.yourprovider.com
MAIL_PORT=587
MAIL_USERNAME=
MAIL_PASSWORD=
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="no-reply@your-domain.com"
MAIL_FROM_NAME="${APP_NAME}"
```

> ⚠️ **Security note:** the `.env` bundled in this archive contains real-looking database credentials and a live-format `APP_KEY`/`APP_URL`. Treat that file as compromised — **generate a fresh `APP_KEY`** (`php artisan key:generate`) and set new database credentials before deploying anywhere, and never commit `.env` to version control.

Additional optional integrations are configured from **Settings → System** inside the app once logged in (not just `.env`):
- SMTP / email settings (`settings/email`)
- Currency & working days (`settings/currency`, `settings/working-days`)
- Branding, SEO, reCAPTCHA, cookie consent (`settings/*`)
- ChatGPT/OpenAI API key (`config/openai.php`, `settings/chatgpt`)
- Google Calendar API credentials (`settings/google-calendar`)
- Webhooks (`settings/webhooks`)
- Biometric attendance (Zekto) device settings

---

## Payment Gateway Configuration

Each gateway has its own controller under `app/Http/Controllers/*PaymentController.php` and is toggled/configured from **Settings → Payment Settings** in the UI (backed by `config/services.php`, `config/paytabs.php`, and the `PaymentSetting` model). At minimum you'll need API keys/secrets for whichever gateways you plan to enable, e.g.:

```env
STRIPE_KEY=
STRIPE_SECRET=

PAYPAL_CLIENT_ID=
PAYPAL_CLIENT_SECRET=
PAYPAL_MODE=sandbox   # or "live"

RAZORPAY_KEY=
RAZORPAY_SECRET=

# ...equivalent KEY/SECRET pairs exist for PayTabs, Mollie, Authorize.Net,
# Cashfree, CoinGate, FedaPay, Flutterwave, Iyzipay, Khalti, Midtrans,
# MercadoPago, Paystack, Skrill, Tap, ToyyibPay, Xendit, YooKassa, etc.
```

Only enable and configure the gateways relevant to your target markets — leaving the rest disabled in Payment Settings is safe and recommended.

---

## Queues, Scheduler & Storage

WorkOrbit uses Laravel's queue and scheduler for background work (emails, payroll processing, leave balance sync, etc.). In production:

```bash
# Run the queue worker (use a process manager like Supervisor in production)
php artisan queue:work --tries=3

# Register the scheduler in your server crontab
* * * * * cd /path-to-app && php artisan schedule:run >> /dev/null 2>&1
```

Set `QUEUE_CONNECTION=database` (run `php artisan queue:table && php artisan migrate` first) or `redis` for real async processing instead of `sync`.

File uploads (employee documents, assets, media) are served through the `storage` symlink — always re-run `php artisan storage:link` after deploying to a new environment.

---

## Local Development

Run backend, queue listener, logs and Vite dev server together:

```bash
composer install
npm install
composer dev
```

This uses the `dev` Composer script (via `concurrently`) to start:
- `php artisan serve`
- `php artisan queue:listen`
- `php artisan pail` (live log viewer)
- `npm run dev` (Vite HMR)

For SSR-enabled development:

```bash
composer dev:ssr
```

Useful individual commands:

```bash
npm run dev          # Vite dev server only
npm run lint         # ESLint (auto-fix)
npm run format       # Prettier (writes resources/)
npm run types        # TypeScript type-check only
php artisan pail      # Tail application logs
```

---

## Building for Production

```bash
composer install --optimize-autoloader --no-dev
npm run build            # or: npm run build:ssr

php artisan config:cache
php artisan route:cache
php artisan view:cache
```

Remember to re-run these `:cache` commands after any deployment, and clear them (`php artisan optimize:clear`) whenever you change `.env` values.

---

## Testing

The project uses **Pest** for testing:

```bash
composer install
php artisan test
# or
./vendor/bin/pest
```

---

## Project Structure

```
app/
  Console/          Artisan commands
  Events/           Domain events
  Helpers/          Global helper functions (autoloaded via composer.json "files")
  Http/Controllers/ ~150 controllers (HR modules + payment gateways + settings)
  Libraries/        Third-party integration wrappers
  Listeners/        Event listeners
  Mail/             Mailables
  Models/           ~100 Eloquent models
  Observers/        Model observers
  PathGenerators/   Media library path generators
  Providers/        Service providers
  Services/         Business logic / service layer
  Traits/
config/             App, auth, payment gateway, permissions, media, installer configs
database/
  migrations/        123 migrations
  seeders/            Default roles, permissions, plans, settings
resources/
  css/               Tailwind entry
  js/                React + TypeScript SPA (Inertia pages, components, hooks)
  lang/              Translation files (i18next)
  views/             Blade wrapper views for Inertia
routes/
  web.php            Main application routes
  auth.php           Authentication routes
  settings.php       Settings routes
  console.php        Scheduled command definitions
public/
  installer/         Web installer assets
  build/             Compiled frontend assets (generated by `npm run build`)
```

---

## Roles & Permissions

Access control is powered by `spatie/laravel-permission`. Default roles and granular permissions (e.g. `manage-ip-restriction-settings`, `manage-biomatric-attedance-settings`, `update-noc`, `update-joining-letter`, `update-experience-certificate`, and module-level CRUD permissions) are seeded via `database/seeders`. Manage roles/permissions from **Settings → Roles & Permissions** in the app, or via the `RoleController` / `PermissionController` API routes.

---

## Troubleshooting

| Issue | Fix |
|---|---|
| Redirected to `/installer` after already installing | Ensure `storage/installed` file exists; check it's writable and not being reset by deployment scripts |
| 500 error, blank page | Set `APP_DEBUG=true` temporarily and check `storage/logs/laravel.log` |
| Assets not loading / styles broken | Run `npm run build` and confirm `public/build/manifest.json` exists |
| File uploads return 404 | Run `php artisan storage:link` and check `public/storage` symlink target |
| Permission denied on storage writes | `chmod -R 775 storage bootstrap/cache` and confirm web server user ownership |
| Payment webhook not firing | Verify the gateway's webhook URL is publicly reachable (not `localhost`) and matches `settings/webhooks` config |
| Scheduled jobs (leave sync, payroll) not running | Confirm the cron entry for `schedule:run` is registered on the server |

---

## License

This codebase is built on the MIT-licensed Laravel React starter kit; verify the licensing terms of your specific WorkOrbit purchase/build before redistribution, as it bundles commercial-grade HR/payroll functionality and third-party paid integrations.
