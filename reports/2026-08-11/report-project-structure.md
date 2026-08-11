# 📁 ساختار پروژه و پوشه‌بندی‌ها — agent-skills

- **تاریخ تهیه:** 2026-08-11
- **شاخه (Branch):** `main`

---

## ۱. نمای کلی

این مخزن یک **سیستم اسکیل (Skill) قابل استفاده برای عامل‌های هوش مصنوعی (AI Agents)** است؛ نه یک مخزن کد برنامه. هر فایل `.md` داخل پوشه‌های اسکیل، یک **متدولوژی کامل و قابل اجرای ممیزی (Audit)** است که به مدل (مثلاً DeepSeek-V4 Flash) دستورالعمل دقیق می‌دهد: نقش، بخش‌ها، قواعد تشخیص، مدل شدت، الزامات شواهد، و فرمت خروجی.

اسکیل‌ها بر اساس **فریم‌ورک** دسته‌بندی شده‌اند:

| پوشه | فریم‌ورک | وضعیت |
|---|---|---|
| `backend/nestjs/` | بک‌اند NestJS / TypeScript | ✅ کامیت و منتشر شده |
| `backend/mezzio/` | بک‌اند PHP 8.x / Mezzio & Laminas | ✅ کامیت و منتشر شده |
| `front/nuxtjs/` | فرانت‌اند Nuxt (Vue SSR) | 🆕 ساخته شده، هنوز کامیت نشده |
| `front/vuejs/` | فرانت‌اند Vue (SPA/CSR) | 🆕 ساخته شده، هنوز کامیت نشده |
| `seo/` | اسکیل سئو (Nuxt) | ✅ کامیت و منتشر شده |
| `infrastructure/` | امنیت مخزن زیرساخت/دیپلوی (Docker و…) | روی دیسک موجود، خارج از git |

---

## ۲. ساختار کامل (درخت پوشه‌ها)

```text
agent-skills/
├── README.md                                  ← توضیح کوتاه مخزن
├── .gitignore                                 ← شامل .idea ، .freebuff ، /report
│
├── backend/
│   ├── nestjs/                                ← ۱۲ اسکیل ممیزی بک‌اند NestJS
│   │   ├── 1-security-audit.md
│   │   ├── 2-performance-audit.md
│   │   ├── 3-typeorm-audit.md
│   │   ├── 4-compliance-audit.md
│   │   ├── 5-qa-audit.md
│   │   ├── 6-observability-sre-audit.md
│   │   ├── 7-cicd-infrastructure.md
│   │   ├── 8-async-architecture-audit.md
│   │   ├── 9-resilience-multitenancy-governance.md
│   │   ├── 10-rag-vector-llm-audit.md
│   │   ├── 11-master-consolidation-audit.md   ← هماهنگ‌کننده (اورکستراتور) مهارت‌های ۱ تا ۱۰
│   │   └── 12-universal-targeted-security-audit.md
│   │
│   └── mezzio/                                ← ۱۲ اسکیل معادل برای PHP 8.x / Mezzio
│       ├── 1-security-vulnerability-audit.md
│       ├── 2-architecture-performance-audit.md
│       ├── 3-database-layer-audit.md
│       ├── 4-engineering-compliance-scorecard.md
│       ├── 5-qa-testing-audit.md
│       ├── 6-observability-sre-audit.md
│       ├── 7-cicd-infrastructure-audit.md
│       ├── 8-async-queues-architecture-audit.md
│       ├── 9-resilience-multitenancy-governance.md
│       ├── 10-rag-vector-llm-audit.md
│       ├── 11-master-consolidation-report.md   ← هماهنگ‌کننده مهارت‌های ۱ تا ۱۰
│       └── 12-multistack-security-compliance-audit.md
│
├── front/
│   ├── README.md                              ← راهنمای کلی سوت فرانت‌اند
│   │
│   ├── nuxtjs/                                ← ۱۰ اسکیل + README (ساختار تخت، بدون زیرپوشه)
│   │   ├── README.md
│   │   ├── audit.md                           ← نقطه ورود اصلی / ترتیب اجرا
│   │   ├── security.md
│   │   ├── performance.md
│   │   ├── architecture.md
│   │   ├── ssr.md
│   │   ├── seo.md
│   │   ├── api.md
│   │   ├── infrastructure.md
│   │   ├── dependencies.md
│   │   └── testing.md
│   │
│   └── vuejs/                                 ← ۹ اسکیل + README (ساختار تخت)
│       ├── README.md
│       ├── audit.md                           ← نقطه ورود اصلی / ترتیب اجرا
│       ├── security.md
│       ├── performance.md
│       ├── architecture.md
│       ├── rendering.md                       ← به‌جای ssr (SPA-first، بدون فرض SSR)
│       ├── api.md
│       ├── infrastructure.md
│       ├── dependencies.md
│       └── testing.md
│
├── seo/
│   └── nuxt-seo-performance-audit-agent.md     ← اسکیل تخصصی سئو/پرفورمنس Nuxt
│
├── infrastructure/
│   └── infrastructure-repo-security-audit-skill.md ← امنیت مخزن زیرساخت (Docker/NATS/Postgres و…)
│
├── reports/                                   ← خروجی‌های اجرا (در gitignore — /report)
│   └── 2026-08-11/
│       └── report-project-structure.md        ← همین سند
│
├── .idea/                                     ← تنظیمات محلی IDE (در gitignore)
└── .freebuff/                                 ← داده‌های ابزار Freebuff (در gitignore)
```

---

## ۳. قواعد ساختاری کلیدی

### ۳.۱ ساختار تخت (Flat) در فرانت‌اند
طبق الزام معماری، **در `front/nuxtjs/` و `front/vuejs/` هیچ زیرپوشهٔ دسته‌بندی** (مثل `security/` یا `api/`) وجود ندارد؛ همهٔ اسکیل‌ها فایل `.md` مستقیم در همان پوشه هستند تا کشف و بارگذاری برای عامل هوش مصنوعی ساده باشد.

### ۳.۲ شماره‌گذاری بک‌اند
فایل‌های `backend/nestjs/` و `backend/mezzio/` با پیشوند عددی `1-` تا `12-` شماره‌گذاری شده‌اند و هر شماره با عنوان داخلی «Skill N of 12» همخوان است. مهارت ۱۱ نقش **اورکستراتور** را دارد و مهارت ۱۲ یک ممیزی مکمل چند-استکی است که در امتیازدهی اورکستراتور لحاظ نمی‌شود.

### ۳.۳ نام‌گذاری فرانت‌اند
فایل‌های فرانت‌اند **بدون پیشوند عددی** و با نام موضوعی kebab-case (`security.md`، `performance.md` و…) نام‌گذاری شده‌اند؛ چون هر پوشه فقط به یک فریم‌ورک اختصاص دارد و ترتیب اجرا توسط `audit.md` تعیین می‌شود، نه نام فایل.

---

## ۴. محتوای هر بخش

### `backend/nestjs/` — بک‌اند NestJS
۱۲ مهارت ممیزی برای اپلیکیشن‌های NestJS/TypeScript: امنیت، پرفورمنس، دیتابیس (TypeORM)، انطباق (Compliance)، QA، مشاهده‌پذیری/SRE، CI/CD، معماری async (BullMQ)، تاب‌آوری/چند-مستأجری، RAG/Vector/LLM (Qdrant)، گزارش تجمیعی، و امنیت چند-استکی.

### `backend/mezzio/` — بک‌اند Mezzio
نسخهٔ تبدیل‌شدهٔ همان ۱۲ مهارت برای **PHP 8.x / Mezzio / Laminas** با نگاشت‌های فنی: Controller/Guard → PSR-15 Middleware، DI دکوراتوری → Laminas ServiceManager (PSR-11)، class-validator → Laminas InputFilter/Validator، TypeORM → Doctrine/Laminas\Db، BullMQ → Enqueue/php-amqplib، Pino → Monolog.

### `front/nuxtjs/` — فرانت‌اند Nuxt
۱۰ مهارت شامل مفاهیم خاص Nuxt: Nitro server routes، `runtimeConfig`/`NUXT_` و افشای اسرار، `useFetch`/`useAsyncData` و deduplication، route rules (ISR/SWR/prerender)، hydration و نشت payload، سئو و ایندکس‌پذیری، زنجیرهٔ کش (مرورگر → CDN → Nitro → API).

### `front/vuejs/` — فرانت‌اند Vue
۹ مهارت با فرض **SPA/CSR-first** (SSR فقط در صورت وجود): هزینهٔ reactivit، watcher/computed، مجازی‌سازی لیست، Suspense/Teleport/async components، و تأکید بر اینکه escape پیش‌فرض interpolation امن است (جلوگیری از مثبت کاذب).

### `seo/` و `infrastructure/`
دو مهارت تخصصی مستقل: سئو/پرفورمنس Nuxt و امنیت مخزن زیرساخت (Docker Compose، NGINX، TLS، NATS، Redis، PostgreSQL، n8n، Qdrant، Ollama و…).

---

## ۵. سازگاری و یکپارچگی (Cross-Layer)

- هر مهارت فرانت‌اند شامل بخش **Cross-Layer Considerations** است و به مهارت‌های بک‌اند (`backend/nestjs/` یا `backend/mezzio/`) ارجاع می‌دهد.
- یافته‌هایی که به رفتار بک‌اند وابسته‌اند با برچسب **Cross-Layer** علامت می‌خورند (مثلاً route guard فرانت‌اند ≠ مجوز واقعی؛ امنیت در بک‌اند اعمال می‌شود).
- مدل شدت، فرمت یافته، و الزام شواهد بین همهٔ سوت‌ها یکسان است:
  - شدت: `CRITICAL / HIGH / MEDIUM / LOW / INFO` (+ `BLOCKER`)
  - هر یافته باید شامل: ID، شدت، دسته، عنوان، تأثیر، فایل‌های متأثر، شواهد، علت ریشه، سناریوی حمله/شکست، رفع، روش تأیید.

---

## ۶. وضعیت نسخه‌بندی (Git)

| مسیر | وضعیت |
|---|---|
| `backend/nestjs/` (۱۲ فایل) | ✅ کامیت شده |
| `backend/mezzio/` (۱۲ فایل) | ✅ کامیت شده |
| `seo/` (۱ فایل) | ✅ کامیت شده |
| `front/` (۲۲ فایل) | 🆕 **untracked — هنوز کامیت نشده** |
| `infrastructure/` | خارج از git |
| `reports/` | در `.gitignore` (خروجی‌های موقت اجرا) |
| `.freebuff/` ، `.idea/` | در `.gitignore` |

---

## ۷. نکات تکمیلی

- نسخهٔ فعلی پایدار فریم‌ورک‌ها هنگام ساخت مهارت‌های فرانت‌اند از منابع رسمی راستی‌آزمایی شد: **Nuxt 4.x** (Nuxt 3 در ۳۱ ژوئیهٔ ۲۰۲۶ EOL شد) و **Vue 3.5.x** (Vue 2 EOL)؛ مهارت‌ها به عامل اجراکننده دستور می‌دهند نسخهٔ نصب‌شده را در زمان اجرا بررسی کند و هیچ نسخه‌ای به‌صورت سخت‌کد نشده است.
- خروجی‌های اجرای مهارت‌ها طبق قرارداد در `reports/YYYY-MM-DD/` ذخیره می‌شوند؛ این پوشه عمداً در git قرار نمی‌گیرد.
