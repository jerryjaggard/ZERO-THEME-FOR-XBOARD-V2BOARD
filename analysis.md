# XBoard Complete Repository Analysis
## The Ultimate Knowledge Base for Plugin Development & System Modernization

---

## 1. TITLE BLOCK

| Attribute | Value |
|-----------|-------|
| **Repository Name** | XBOARD-FULL-FILES-AND-ANALYSIS-TO-DEVELOP-NEW-FEATURES |
| **Original Source** | XBoard (based on v2board) - https://github.com/cedar2025/Xboard |
| **Analysis Commit Hash** | 95e8e7bca771b7c827cc01289f393c653eeb3431 |
| **Database Schema Date** | 2025-11-25 |
| **Analysis Date/Time** | 2025-11-25T20:34:57.792Z |
| **Tool Used** | GitHub Copilot Pro Agent |
| **Total Files Analyzed** | 351 |
| **Total Lines of Code** | 51,415+ |
| **PHP Files** | 315 |

---

## 2. EXECUTIVE SUMMARY

### 2.1 What is XBoard?

XBoard is an advanced VPN/proxy subscription management panel forked from v2board. It provides a complete solution for:

- **User Management**: Registration, authentication, subscription lifecycle
- **Subscription Services**: Plan management, traffic allocation, expiration handling
- **Node Management**: Multi-protocol proxy server management (V2Ray, Shadowsocks, Trojan, Hysteria, TUIC, VLESS, etc.)
- **Payment Processing**: Multi-gateway payment integration with plugin architecture
- **Admin Dashboard**: Complete administrative interface for system management
- **API Platform**: RESTful APIs for frontend applications and node communication

### 2.2 Technical Stack Overview

| Component | Technology |
|-----------|------------|
| **Backend Framework** | Laravel 12.x |
| **PHP Version** | PHP 8.2+ |
| **Database** | MySQL 8.0+ / MariaDB |
| **Cache/Queue** | Redis |
| **Queue Worker** | Laravel Horizon |
| **HTTP Server** | Laravel Octane (Swoole/FrankenPHP) |
| **Frontend Admin** | Vue.js (Pre-built assets) |
| **Frontend Theme** | Blade Templates + Vue/React themes |
| **Authentication** | Laravel Sanctum (API Tokens) |
| **Task Scheduling** | Laravel Scheduler |

### 2.3 How Backend Works

1. **Request Flow**: nginx → Laravel Octane (Swoole) → Middleware Pipeline → Controller → Service Layer → Model/Database
2. **API Architecture**: RESTful endpoints with version namespacing (V1/V2)
3. **Plugin System**: Hook-based architecture for extensibility
4. **Queue Processing**: Redis-backed with Horizon for monitoring
5. **Caching**: Redis for settings, session, and data caching

### 2.4 How Frontend Works

1. **Admin Panel**: Pre-compiled Vue.js SPA served from `/public/assets/admin/`
2. **User Dashboard**: Theme-based system with Blade templates
3. **Theme System**: Pluggable themes stored in `/theme/` directory
4. **API Consumption**: REST API calls with Sanctum token authentication

### 2.5 How Nodes Integrate

1. **Node Registration**: Servers register via API with authentication tokens
2. **User Sync**: Nodes fetch active user lists periodically
3. **Traffic Reporting**: Nodes push traffic data to central server
4. **Health Monitoring**: Heartbeat system for online/offline status

### 2.6 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         NGINX / Load Balancer                        │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     Laravel Octane (Swoole/FrankenPHP)              │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    Middleware Pipeline                        │   │
│  │  TrustProxies → InitializePlugins → ForceJson → Language     │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                    │                                 │
│                                    ▼                                 │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                      Route Groups                            │   │
│  │  /api/v1/* (User/Guest/Client/Server/Passport)              │   │
│  │  /api/v2/* (Admin/User/Server/Passport)                     │   │
│  │  /* (Web Routes)                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                    │                                 │
│                                    ▼                                 │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                     Controllers                              │   │
│  │  V1: User, Guest, Client, Server, Passport                  │   │
│  │  V2: Admin (Config, User, Order, Server, Plugin, etc.)      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                    │                                 │
│                                    ▼                                 │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                   Service Layer                              │   │
│  │  OrderService, UserService, PaymentService, PluginManager   │   │
│  │  ServerService, TelegramService, TrafficResetService        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                    │                                 │
│                                    ▼                                 │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    Model Layer                               │   │
│  │  User, Order, Plan, Server, Payment, Coupon, Ticket, etc.   │   │
│  └─────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
                                    │
            ┌───────────────────────┼───────────────────────┐
            ▼                       ▼                       ▼
    ┌───────────────┐      ┌───────────────┐      ┌───────────────┐
    │    MySQL      │      │    Redis      │      │   Horizon     │
    │  (Database)   │      │ (Cache/Queue) │      │ (Queue UI)    │
    └───────────────┘      └───────────────┘      └───────────────┘
```

### 2.7 Risks

| Risk | Severity | Description |
|------|----------|-------------|
| **Single Point of Failure** | HIGH | No built-in clustering for database/Redis |
| **Plugin Security** | MEDIUM | Plugins can execute arbitrary code |
| **Traffic Data Integrity** | MEDIUM | Traffic reporting relies on node honesty |
| **API Rate Limiting** | LOW | Throttling disabled by default |
| **Password Hashing** | MEDIUM | Uses MD5 for legacy migrations (DEPRECATED - migrate to bcrypt) |

---

## 3. TECHNOLOGY MATRIX

### 3.1 Backend Technologies

| Category | Technology | Version | Purpose |
|----------|------------|---------|---------|
| **Framework** | Laravel | 12.x | Core MVC framework |
| **PHP** | PHP | 8.2+ | Runtime |
| **HTTP Server** | Laravel Octane | 2.11.x | High-performance server |
| **Authentication** | Laravel Sanctum | 4.x | API token auth |
| **Queue** | Laravel Horizon | 5.30+ | Queue monitoring |
| **ORM** | Eloquent | Built-in | Database abstraction |
| **DBAL** | Doctrine DBAL | 4.x | Database migrations |

### 3.2 Composer Dependencies

```json
{
    "php": "^8.2",
    "bacon/bacon-qr-code": "^2.0",
    "doctrine/dbal": "^4.0",
    "google/cloud-storage": "^1.35",
    "google/recaptcha": "^1.2",
    "guzzlehttp/guzzle": "^7.8",
    "laravel/framework": "^12.0",
    "laravel/horizon": "^5.30",
    "laravel/octane": "2.11.*",
    "laravel/prompts": "^0.3",
    "laravel/sanctum": "^4.0",
    "laravel/tinker": "^2.10",
    "linfo/linfo": "^4.0",
    "paragonie/sodium_compat": "^1.20",
    "php-curl-class/php-curl-class": "^8.6",
    "spatie/db-dumper": "^3.4",
    "stripe/stripe-php": "^7.36.1",
    "symfony/http-client": "^7.0",
    "symfony/mailgun-mailer": "^7.0",
    "symfony/yaml": "*",
    "zoujingli/ip2region": "^2.0"
}
```

### 3.3 Cache System

| Setting | Value |
|---------|-------|
| **Driver** | Redis |
| **Prefix** | Configurable via .env |
| **TTL** | Varies by cache type |

### 3.4 Session Configuration

| Setting | Value |
|---------|-------|
| **Driver** | Database/Redis (configurable) |
| **Lifetime** | 120 minutes (default) |

### 3.5 Hashing Algorithm

| Type | Algorithm |
|------|-----------|
| **User Passwords** | Bcrypt (Laravel default) |
| **Legacy Support** | MD5 with salt (DEPRECATED - upgrade to bcrypt on login) |
| **API Tokens** | SHA256 |

### 3.6 Queue System

| Setting | Value |
|---------|-------|
| **Driver** | Redis |
| **Connection** | default |
| **Monitor** | Laravel Horizon |

### 3.7 Logging Framework

| Setting | Value |
|---------|-------|
| **Driver** | Stack (single + mysql) |
| **MySQL Logger** | Custom MysqlLoggerHandler |
| **Level** | Configurable |

### 3.8 Payment Libraries

| Library | Purpose |
|---------|---------|
| **Stripe PHP** | Stripe payments |
| **Plugin System** | EPay, Coinbase, BTCPay, etc. |

### 3.9 Frontend Technologies

| Component | Technology |
|-----------|------------|
| **Admin Panel** | Vue.js 3 (pre-compiled) |
| **User Themes** | Blade + Vue/React |
| **Build System** | Vite (for themes) |
| **CSS Framework** | Tailwind/Custom |

### 3.10 Server Requirements

```
- nginx (or Apache with mod_rewrite)
- php8.2 with extensions:
  - php-fpm
  - php-mysql
  - php-redis
  - php-json
  - php-mbstring
  - php-curl
  - php-openssl
  - php-zip
  - php-bcmath
  - php-sodium
  - php-fileinfo
  - php-xml
  - php-swoole (for Octane)
- MySQL 8.0+ / MariaDB 10.6+
- Redis 6.0+
- Supervisor (for queue workers)
- Cron (for scheduled tasks)
- Ports: 80/443 (web), 3306 (mysql), 6379 (redis)
```

### 3.11 Node Integration Protocols

| Protocol | Status |
|----------|--------|
| VMess (V2Ray) | ✅ Supported |
| VLESS | ✅ Supported |
| Trojan | ✅ Supported |
| Shadowsocks | ✅ Supported |
| Hysteria/Hysteria2 | ✅ Supported |
| TUIC | ✅ Supported |
| AnyTLS | ✅ Supported |
| SOCKS5 | ✅ Supported |
| HTTP | ✅ Supported |
| Naive | ✅ Supported |
| Mieru | ✅ Supported |

---

## 4. COMPLETE FILE INDEX

### 4.1 Core Application Files

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Http/Kernel.php` | PHP | HTTP kernel, middleware registration | 97 |
| `app/Console/Kernel.php` | PHP | Console kernel, scheduled tasks | 70 |
| `bootstrap/app.php` | PHP | Application bootstrap | 55 |
| `artisan` | PHP | CLI entry point | - |
| `public/index.php` | PHP | Web entry point | - |

### 4.2 Controllers (V1 - User/Guest API)

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Http/Controllers/V1/User/UserController.php` | PHP | User profile management | 225 |
| `app/Http/Controllers/V1/User/OrderController.php` | PHP | Order operations | 211 |
| `app/Http/Controllers/V1/User/GiftCardController.php` | PHP | Gift card redemption | 193 |
| `app/Http/Controllers/V1/User/TicketController.php` | PHP | Support tickets | 154 |
| `app/Http/Controllers/V1/User/KnowledgeController.php` | PHP | Knowledge base | 150 |
| `app/Http/Controllers/V1/User/InviteController.php` | PHP | Referral system | 79 |
| `app/Http/Controllers/V1/User/PlanController.php` | PHP | Subscription plans | 38 |
| `app/Http/Controllers/V1/User/ServerController.php` | PHP | Server listing | 31 |
| `app/Http/Controllers/V1/User/StatController.php` | PHP | User statistics | 26 |
| `app/Http/Controllers/V1/User/NoticeController.php` | PHP | Announcements | 26 |
| `app/Http/Controllers/V1/User/TelegramController.php` | PHP | Telegram binding | 26 |
| `app/Http/Controllers/V1/User/CouponController.php` | PHP | Coupon validation | 25 |
| `app/Http/Controllers/V1/User/CommController.php` | PHP | Common configs | 39 |
| `app/Http/Controllers/V1/Guest/CommController.php` | PHP | Guest configs | 39 |
| `app/Http/Controllers/V1/Guest/TelegramController.php` | PHP | Telegram webhook | 126 |
| `app/Http/Controllers/V1/Guest/PaymentController.php` | PHP | Payment callbacks | 52 |
| `app/Http/Controllers/V1/Guest/PlanController.php` | PHP | Public plans | 25 |
| `app/Http/Controllers/V1/Passport/AuthController.php` | PHP | Authentication | 175 |
| `app/Http/Controllers/V1/Passport/CommController.php` | PHP | Email verification | 76 |
| `app/Http/Controllers/V1/Client/ClientController.php` | PHP | Subscription delivery | 246 |
| `app/Http/Controllers/V1/Client/AppController.php` | PHP | App version check | 90 |
| `app/Http/Controllers/V1/Server/UniProxyController.php` | PHP | Universal node API | 276 |
| `app/Http/Controllers/V1/Server/TrojanTidalabController.php` | PHP | Trojan node API | 107 |
| `app/Http/Controllers/V1/Server/ShadowsocksTidalabController.php` | PHP | SS node API | 64 |

### 4.3 Controllers (V2 - Admin API)

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Http/Controllers/V2/Admin/GiftCardController.php` | PHP | Gift card management | 622 |
| `app/Http/Controllers/V2/Admin/UserController.php` | PHP | User management | 519 |
| `app/Http/Controllers/V2/Admin/StatController.php` | PHP | Statistics | 507 |
| `app/Http/Controllers/V2/Admin/PluginController.php` | PHP | Plugin management | 332 |
| `app/Http/Controllers/V2/Admin/ConfigController.php` | PHP | System configuration | 316 |
| `app/Http/Controllers/V2/Admin/SystemController.php` | PHP | System status | 300 |
| `app/Http/Controllers/V2/Admin/OrderController.php` | PHP | Order management | 252 |
| `app/Http/Controllers/V2/Admin/TrafficResetController.php` | PHP | Traffic reset | 234 |
| `app/Http/Controllers/V2/Admin/CouponController.php` | PHP | Coupon management | 186 |
| `app/Http/Controllers/V2/Admin/TicketController.php` | PHP | Ticket management | 156 |
| `app/Http/Controllers/V2/Admin/ThemeController.php` | PHP | Theme management | 150 |
| `app/Http/Controllers/V2/Admin/PaymentController.php` | PHP | Payment gateways | 133 |
| `app/Http/Controllers/V2/Admin/PlanController.php` | PHP | Plan management | 132 |
| `app/Http/Controllers/V2/Admin/Server/ManageController.php` | PHP | Server management | 126 |
| `app/Http/Controllers/V2/Admin/KnowledgeController.php` | PHP | Knowledge management | 113 |
| `app/Http/Controllers/V2/Admin/NoticeController.php` | PHP | Notice management | 101 |
| `app/Http/Controllers/V2/Admin/Server/GroupController.php` | PHP | Server groups | 66 |
| `app/Http/Controllers/V2/Admin/Server/RouteController.php` | PHP | Server routes | 64 |
| `app/Http/Controllers/V2/Admin/UpdateController.php` | PHP | System updates | 27 |

### 4.4 Services

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Services/Plugin/PluginManager.php` | PHP | Plugin lifecycle management | 726 |
| `app/Services/UpdateService.php` | PHP | System update management | 457 |
| `app/Services/OrderService.php` | PHP | Order processing | 428 |
| `app/Services/ThemeService.php` | PHP | Theme management | 424 |
| `app/Services/TrafficResetService.php` | PHP | Traffic reset logic | 414 |
| `app/Services/StatisticalService.php` | PHP | Statistics calculation | 378 |
| `app/Services/GiftCardService.php` | PHP | Gift card operations | 334 |
| `app/Services/UserService.php` | PHP | User operations | 287 |
| `app/Services/Plugin/HookManager.php` | PHP | Hook system | 285 |
| `app/Services/MailService.php` | PHP | Email sending | 249 |
| `app/Services/Plugin/AbstractPlugin.php` | PHP | Plugin base class | 221 |
| `app/Services/PlanService.php` | PHP | Plan operations | 194 |
| `app/Services/Auth/RegisterService.php` | PHP | User registration | 192 |
| `app/Services/TelegramService.php` | PHP | Telegram integration | 160 |
| `app/Services/Auth/LoginService.php` | PHP | User login | 153 |
| `app/Services/PaymentService.php` | PHP | Payment processing | 127 |
| `app/Services/TicketService.php` | PHP | Ticket operations | 125 |
| `app/Services/CouponService.php` | PHP | Coupon operations | 122 |
| `app/Services/UserOnlineService.php` | PHP | Online status tracking | 122 |
| `app/Services/ServerService.php` | PHP | Server operations | 114 |
| `app/Services/CaptchaService.php` | PHP | CAPTCHA verification | 111 |
| `app/Services/Plugin/PluginConfigService.php` | PHP | Plugin config | 110 |
| `app/Services/Auth/MailLinkService.php` | PHP | Magic link login | 99 |
| `app/Services/AuthService.php` | PHP | Auth utilities | 87 |

### 4.5 Models

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Models/Server.php` | PHP | Server/Node model | 470 |
| `app/Models/Plan.php` | PHP | Subscription plan model | 352 |
| `app/Models/GiftCardTemplate.php` | PHP | Gift card template | 253 |
| `app/Models/User.php` | PHP | User model | 191 |
| `app/Models/TrafficResetLog.php` | PHP | Traffic reset logs | 148 |
| `app/Models/Order.php` | PHP | Order model | 120 |
| `app/Models/GiftCardUsage.php` | PHP | Gift card usage | 111 |
| `app/Models/Setting.php` | PHP | Settings model | 68 |
| `app/Models/TicketMessage.php` | PHP | Ticket message | 56 |
| `app/Models/StatServer.php` | PHP | Server statistics | 33 |
| `app/Models/Coupon.php` | PHP | Coupon model | 28 |
| `app/Models/InviteCode.php` | PHP | Invite code | 19 |
| `app/Models/Payment.php` | PHP | Payment gateway | 18 |
| `app/Models/Notice.php` | PHP | Announcement | 18 |
| `app/Models/ServerRoute.php` | PHP | Server routing | 17 |
| `app/Models/Log.php` | PHP | System log | 17 |
| `app/Models/Stat.php` | PHP | Statistics | 16 |
| `app/Models/MailLog.php` | PHP | Mail log | 16 |
| `app/Models/ServerStat.php` | PHP | Server stats | 16 |
| `app/Models/CommissionLog.php` | PHP | Commission log | 16 |

### 4.6 Protocols (Subscription Formats)

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Protocols/ClashMeta.php` | PHP | Clash Meta format | 569 |
| `app/Protocols/Stash.php` | PHP | Stash format | 520 |
| `app/Protocols/SingBox.php` | PHP | Sing-Box format | 508 |
| `app/Protocols/Shadowrocket.php` | PHP | Shadowrocket format | 364 |
| `app/Protocols/Clash.php` | PHP | Clash format | 333 |
| `app/Protocols/Surge.php` | PHP | Surge format | 310 |
| `app/Protocols/QuantumultX.php` | PHP | QuantumultX format | 298 |
| `app/Protocols/Surfboard.php` | PHP | Surfboard format | 289 |
| `app/Protocols/Loon.php` | PHP | Loon format | 285 |
| `app/Protocols/General.php` | PHP | General URI format | 245 |
| `app/Protocols/Shadowsocks.php` | PHP | SS SIP002 format | 120 |

### 4.7 Middleware

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Http/Middleware/Server.php` | PHP | Node authentication | 59 |
| `app/Http/Middleware/TrustProxies.php` | PHP | Proxy trust | 47 |
| `app/Http/Middleware/InitializePlugins.php` | PHP | Plugin init | 36 |
| `app/Http/Middleware/Client.php` | PHP | Client auth | 33 |
| `app/Http/Middleware/Staff.php` | PHP | Staff auth | 30 |
| `app/Http/Middleware/Admin.php` | PHP | Admin auth | 30 |
| `app/Http/Middleware/EnsureTransactionState.php` | PHP | Transaction state | 29 |
| `app/Http/Middleware/User.php` | PHP | User auth | 27 |
| `app/Http/Middleware/RedirectIfAuthenticated.php` | PHP | Guest redirect | 26 |
| `app/Http/Middleware/RequestLog.php` | PHP | Request logging | 24 |
| `app/Http/Middleware/ForceJson.php` | PHP | Force JSON response | 22 |
| `app/Http/Middleware/VerifyCsrfToken.php` | PHP | CSRF protection | 22 |
| `app/Http/Middleware/TrimStrings.php` | PHP | String trimming | 19 |
| `app/Http/Middleware/CheckForMaintenanceMode.php` | PHP | Maintenance mode | 18 |
| `app/Http/Middleware/Authenticate.php` | PHP | Base auth | 17 |
| `app/Http/Middleware/Language.php` | PHP | Language detection | 17 |
| `app/Http/Middleware/EncryptCookies.php` | PHP | Cookie encryption | 16 |

### 4.8 Jobs (Queue)

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Jobs/StatServerJob.php` | PHP | Server statistics | 161 |
| `app/Jobs/StatUserJob.php` | PHP | User statistics | 157 |
| `app/Jobs/UpdateAliveDataJob.php` | PHP | Node alive update | 108 |
| `app/Jobs/OrderHandleJob.php` | PHP | Order processing | 56 |
| `app/Jobs/TrafficFetchJob.php` | PHP | Traffic fetching | 49 |
| `app/Jobs/SendTelegramJob.php` | PHP | Telegram sending | 43 |
| `app/Jobs/SendEmailJob.php` | PHP | Email sending | 42 |

### 4.9 Console Commands

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Console/Commands/XboardInstall.php` | PHP | Installation wizard | 429 |
| `app/Console/Commands/MigrateFromV2b.php` | PHP | V2Board migration | 350+ |
| `app/Console/Commands/XboardStatistics.php` | PHP | Daily statistics | 200+ |
| `app/Console/Commands/ResetTraffic.php` | PHP | Traffic reset | 150+ |
| `app/Console/Commands/CheckOrder.php` | PHP | Order checking | 100+ |
| `app/Console/Commands/CheckCommission.php` | PHP | Commission check | 100+ |
| `app/Console/Commands/CheckTicket.php` | PHP | Ticket checking | 80+ |
| `app/Console/Commands/SendRemindMail.php` | PHP | Reminder emails | 100+ |
| `app/Console/Commands/ResetLog.php` | PHP | Log cleanup | 50+ |
| `app/Console/Commands/ClearUser.php` | PHP | User cleanup | 80+ |
| `app/Console/Commands/CheckServer.php` | PHP | Server checking | 60+ |
| `app/Console/Commands/BackupDatabase.php` | PHP | Database backup | 100+ |
| `app/Console/Commands/ResetPassword.php` | PHP | Password reset | 50+ |
| `app/Console/Commands/HookList.php` | PHP | List all hooks | 50+ |
| `app/Console/Commands/CleanupExpiredOnlineStatus.php` | PHP | Online status cleanup | 40+ |
| `app/Console/Commands/XboardUpdate.php` | PHP | System update | 100+ |
| `app/Console/Commands/XboardRollback.php` | PHP | Rollback update | 80+ |
| `app/Console/Commands/ExportV2Log.php` | PHP | Export logs | 60+ |
| `app/Console/Commands/Test.php` | PHP | Testing command | 30+ |

### 4.10 Providers

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Providers/RouteServiceProvider.php` | PHP | Route registration | 91 |
| `app/Providers/ProtocolServiceProvider.php` | PHP | Protocol registration | 49 |
| `app/Providers/HorizonServiceProvider.php` | PHP | Horizon config | 43 |
| `app/Providers/OctaneServiceProvider.php` | PHP | Octane config | 42 |
| `app/Providers/SettingServiceProvider.php` | PHP | Settings loading | 33 |
| `app/Providers/EventServiceProvider.php` | PHP | Event registration | 28 |
| `app/Providers/AuthServiceProvider.php` | PHP | Auth policies | 28 |
| `app/Providers/PluginServiceProvider.php` | PHP | Plugin init | 28 |
| `app/Providers/BroadcastServiceProvider.php` | PHP | Broadcasting | 21 |

### 4.11 Routes

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `app/Http/Routes/V2/AdminRoute.php` | PHP | Admin routes | 275 |
| `app/Http/Routes/V1/UserRoute.php` | PHP | User routes | 83 |
| `app/Http/Routes/V1/ServerRoute.php` | PHP | Server routes | 45 |
| `app/Http/Routes/V1/PassportRoute.php` | PHP | Auth routes | 27 |
| `app/Http/Routes/V2/PassportRoute.php` | PHP | Auth routes V2 | 27 |
| `app/Http/Routes/V1/GuestRoute.php` | PHP | Guest routes | 27 |
| `app/Http/Routes/V2/ServerRoute.php` | PHP | Server routes V2 | 26 |
| `app/Http/Routes/V1/ClientRoute.php` | PHP | Client routes | 23 |
| `app/Http/Routes/V2/UserRoute.php` | PHP | User routes V2 | 20 |
| `routes/web.php` | PHP | Web routes | 88 |
| `routes/console.php` | PHP | Console routes | - |
| `routes/channels.php` | PHP | Broadcast channels | - |

### 4.12 Plugins (Built-in)

| Path | Type | Purpose | LOC |
|------|------|---------|-----|
| `plugins/Telegram/Plugin.php` | PHP | Telegram binding | 436 |
| `plugins/Epay/Plugin.php` | PHP | EPay payment | 96 |
| `plugins/AlipayF2f/Plugin.php` | PHP | Alipay F2F | 150+ |
| `plugins/Btcpay/Plugin.php` | PHP | BTCPay payment | 100+ |
| `plugins/Coinbase/Plugin.php` | PHP | Coinbase payment | 100+ |
| `plugins/CoinPayments/Plugin.php` | PHP | CoinPayments | 100+ |
| `plugins/Mgate/Plugin.php` | PHP | MGate payment | 100+ |


---

## 5. ARCHITECTURE OVERVIEW

### 5.1 Backend Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                             │
├─────────────────────────────────────────────────────────────────┤
│  Controllers (V1/V2)                                            │
│    ├── User Controllers (profile, order, ticket, etc.)          │
│    ├── Admin Controllers (config, user, server, etc.)           │
│    ├── Guest Controllers (payment callbacks, public API)        │
│    ├── Server Controllers (node sync, traffic reporting)        │
│    └── Passport Controllers (auth, registration)                │
├─────────────────────────────────────────────────────────────────┤
│  Services Layer                                                  │
│    ├── OrderService - Order lifecycle management                │
│    ├── UserService - User operations                            │
│    ├── PaymentService - Payment gateway abstraction             │
│    ├── PluginManager - Plugin lifecycle                         │
│    ├── ServerService - Node management                          │
│    ├── TrafficResetService - Traffic reset logic                │
│    └── AuthService - Authentication utilities                   │
├─────────────────────────────────────────────────────────────────┤
│  Models Layer (Eloquent ORM)                                    │
│    ├── User, Order, Plan, Server, Payment                       │
│    ├── Coupon, Ticket, Notice, Knowledge                        │
│    └── Settings, Log, Statistics                                │
├─────────────────────────────────────────────────────────────────┤
│  Support Layer                                                   │
│    ├── Settings Manager - Configuration handling                │
│    ├── ProtocolManager - Subscription format handling           │
│    └── HookManager - Plugin hook system                         │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Frontend Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    ADMIN PANEL (Vue.js SPA)                      │
│  Location: /public/assets/admin/                                │
│  Entry: /admin_path (configurable)                              │
│  ├── Components: Dashboard, User, Order, Server, Config, etc.  │
│  ├── State: Vuex/Pinia                                          │
│  └── API: REST calls to /api/v2/*                               │
├─────────────────────────────────────────────────────────────────┤
│                    USER DASHBOARD (Theme-based)                  │
│  Location: /theme/{theme_name}/                                 │
│  Entry: / (root)                                                │
│  ├── Themes: Xboard, v2board, custom                            │
│  ├── Template: Blade + Vue/React                                │
│  └── API: REST calls to /api/v1/*                               │
├─────────────────────────────────────────────────────────────────┤
│                    SUBSCRIPTION CLIENT                           │
│  Endpoint: /{subscribe_path}/{token}                            │
│  Formats: Clash, SingBox, Surge, Shadowrocket, etc.             │
└─────────────────────────────────────────────────────────────────┘
```

### 5.3 API Architecture

```
/api/
├── v1/                          # User-facing API
│   ├── passport/               # Authentication
│   │   ├── auth/login
│   │   ├── auth/register
│   │   └── comm/sendEmailVerify
│   ├── user/                   # User operations
│   │   ├── info, update, changePassword
│   │   ├── order/save, checkout, fetch
│   │   ├── ticket/save, reply, close
│   │   └── server/fetch
│   ├── guest/                  # Public endpoints
│   │   ├── comm/config
│   │   ├── payment/notify/{method}/{uuid}
│   │   └── telegram/webhook
│   ├── client/                 # Subscription delivery
│   │   └── subscribe
│   └── server/                 # Node sync
│       ├── UniProxy/config, user, push, alive
│       ├── TrojanTidalab/config, user, submit
│       └── ShadowsocksTidalab/user, submit
│
└── v2/                          # Admin API
    └── {admin_path}/           # Dynamic admin path
        ├── config/fetch, save
        ├── plan/fetch, save, drop
        ├── server/group, route, manage
        ├── order/fetch, update, cancel
        ├── user/fetch, update, ban
        ├── stat/getOverride, getStats
        ├── plugin/getPlugins, install, enable
        └── theme/getThemes, upload
```

### 5.4 Node Reporting Architecture

```
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│   Node 1      │     │   Node 2      │     │   Node N      │
│  (V2Ray/SS)   │     │   (Trojan)    │     │   (Hysteria)  │
└───────┬───────┘     └───────┬───────┘     └───────┬───────┘
        │                     │                     │
        │ GET /server/UniProxy/user                 │
        │ (fetch active users)                      │
        │◄────────────────────┼─────────────────────┤
        │                     │                     │
        │ POST /server/UniProxy/push                │
        │ (report traffic)                          │
        ├─────────────────────┼─────────────────────►
        │                     │                     │
        │ POST /server/UniProxy/alive               │
        │ (heartbeat + online count)                │
        ├─────────────────────┼─────────────────────►
        │                     │                     │
        ▼                     ▼                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    XBoard Server                             │
│  ├── Validate node token                                    │
│  ├── Process traffic data → Queue Job                       │
│  ├── Update user u/d counters                               │
│  ├── Record server statistics                               │
│  └── Update server online status                            │
└─────────────────────────────────────────────────────────────┘
```

### 5.5 Payment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Payment Flow                                │
└─────────────────────────────────────────────────────────────────┘

User Request                    Payment Gateway                 Callback
     │                               │                              │
     ▼                               │                              │
┌──────────┐                         │                              │
│ 1. User  │                         │                              │
│ creates  │                         │                              │
│ order    │                         │                              │
└────┬─────┘                         │                              │
     │                               │                              │
     ▼                               │                              │
┌──────────┐     ┌───────────┐       │                              │
│ 2. Get   │────►│ Payment   │       │                              │
│ payment  │     │ Service   │       │                              │
│ URL      │     └─────┬─────┘       │                              │
└──────────┘           │             │                              │
                       ▼             │                              │
              ┌────────────────┐     │                              │
              │ 3. Plugin      │     │                              │
              │ generates      │     │                              │
              │ payment URL    │─────┼──────────────────────────────►
              └────────────────┘     │                              │
                                     │                              │
                                     │     ┌────────────────────┐   │
                                     │     │ 4. User pays on    │   │
                                     │     │ gateway page       │   │
                                     │     └─────────┬──────────┘   │
                                     │               │              │
                                     │               ▼              │
                                     │     ┌────────────────────┐   │
                                     │     │ 5. Gateway sends   │───┤
                                     │     │ callback           │   │
                                     │     └────────────────────┘   │
                                     │                              │
                                     │                              ▼
                                     │     ┌────────────────────────────┐
                                     │     │ 6. /api/v1/guest/payment/ │
                                     │     │    notify/{method}/{uuid}  │
                                     │     └───────────────┬────────────┘
                                     │                     │
                                     │                     ▼
                                     │     ┌────────────────────────────┐
                                     │     │ 7. PaymentService::notify │
                                     │     │    - Validate signature    │
                                     │     │    - Update order status   │
                                     │     │    - Dispatch OrderHandle  │
                                     │     └────────────────────────────┘
```

### 5.6 Cron Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Laravel Scheduler (Cron)                      │
│                    Runs every minute via crontab                 │
└─────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│ Every Minute  │    │    Daily      │    │   Periodic    │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ check:order   │    │ xboard:       │    │ horizon:      │
│ check:commis  │    │  statistics   │    │  snapshot     │
│ check:ticket  │    │ reset:log     │    │  (5 min)      │
│ reset:traffic │    │ send:remind   │    │               │
│ cleanup:      │    │  Mail         │    │               │
│  expired      │    │               │    │               │
└───────────────┘    └───────────────┘    └───────────────┘
```

### 5.7 Worker/Queue System

```
┌─────────────────────────────────────────────────────────────────┐
│                    Laravel Horizon                               │
│                    Redis Queue Backend                           │
└─────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│ OrderHandle   │    │ SendEmail     │    │ StatServer    │
│ Job           │    │ Job           │    │ Job           │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ - Open order  │    │ - Send via    │    │ - Record      │
│ - Update user │    │   configured  │    │   server      │
│ - Grant plan  │    │   mail driver │    │   traffic     │
│ - Process     │    │               │    │   stats       │
│   commission  │    │               │    │               │
└───────────────┘    └───────────────┘    └───────────────┘

┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│ StatUser      │    │ SendTelegram  │    │ UpdateAlive   │
│ Job           │    │ Job           │    │ Data Job      │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ - Record user │    │ - Send to     │    │ - Update node │
│   traffic     │    │   Telegram    │    │   online      │
│   statistics  │    │   via bot     │    │   status      │
└───────────────┘    └───────────────┘    └───────────────┘
```

### 5.8 Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Production Deployment                         │
└─────────────────────────────────────────────────────────────────┘

            Internet
               │
               ▼
┌─────────────────────────────┐
│     nginx / Load Balancer   │  ← Port 80/443
│   (SSL Termination)         │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│   Laravel Octane (Swoole)   │  ← Port 8000
│   or php-fpm                │
└──────────────┬──────────────┘
               │
     ┌─────────┴─────────┐
     ▼                   ▼
┌─────────────┐    ┌─────────────┐
│   MySQL     │    │   Redis     │
│  Database   │    │ Cache/Queue │
│  Port 3306  │    │  Port 6379  │
└─────────────┘    └─────────────┘

┌─────────────────────────────┐
│   Supervisor                │
│   ├── php artisan horizon   │  ← Queue Worker
│   └── php artisan octane:   │  ← HTTP Server
│       start                 │
└─────────────────────────────┘

┌─────────────────────────────┐
│   Cron                      │
│   * * * * * php artisan     │
│   schedule:run              │
└─────────────────────────────┘
```

---

## 6. FULL REQUEST LIFECYCLE

### 6.1 nginx Routing

```nginx
server {
    listen 80;
    server_name example.com;
    root /path/to/xboard/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        # Option 1: Octane (Swoole)
        proxy_pass http://127.0.0.1:8000;
        
        # Option 2: php-fpm
        # fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        # fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        # include fastcgi_params;
    }
}
```

### 6.2 index.php Bootstrap

```php
// public/index.php
<?php

use Illuminate\Http\Request;

define('LARAVEL_START', microtime(true));

// Composer autoloader
require __DIR__.'/../vendor/autoload.php';

// Bootstrap application
$app = require_once __DIR__.'/../bootstrap/app.php';

// Handle request
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

$response = $kernel->handle(
    $request = Request::capture()
)->send();

$kernel->terminate($request, $response);
```

### 6.3 Middleware Pipeline

```
Request
   │
   ▼
1. HandleCors (CORS headers)
   │
   ▼
2. TrustProxies (X-Forwarded headers)
   │
   ▼
3. CheckForMaintenanceMode
   │
   ▼
4. ValidatePostSize
   │
   ▼
5. TrimStrings
   │
   ▼
6. ConvertEmptyStringsToNull
   │
   ▼
7. InitializePlugins (Load enabled plugins)
   │
   ▼
[Route Matched]
   │
   ▼
8. ForceJson (API routes)
   │
   ▼
9. Language (Set locale)
   │
   ▼
10. SubstituteBindings
   │
   ▼
11. Route-specific middleware:
    ├── user → User auth check
    ├── admin → Admin auth check
    ├── client → Client token check
    ├── server → Node token check
    └── log → Request logging
   │
   ▼
Controller
```

### 6.4 Controller Loading

```php
// RouteServiceProvider loads routes
Route::group([
    'prefix' => '/api/v1',
    'middleware' => 'api',
    'namespace' => $this->namespace
], function ($router) {
    foreach (glob(app_path('Http/Routes/V1') . '/*.php') as $file) {
        $this->app->make('App\\Http\\Routes\\V1\\' . basename($file, '.php'))->map($router);
    }
});

// Route files define endpoints
// Example: UserRoute.php
$router->group([
    'prefix' => 'user',
    'middleware' => 'user'
], function ($router) {
    $router->get('/info', [UserController::class, 'info']);
    // ... more routes
});
```

### 6.5 Service Layer Pattern

```php
// Controller receives request
public function save(OrderSave $request)
{
    // Service handles business logic
    $order = OrderService::createFromRequest(
        $request->user,
        Plan::find($request->input('plan_id')),
        $request->input('period'),
        $request->input('coupon_code')
    );
    
    return $this->success($order->toArray());
}

// Service encapsulates logic
public static function createFromRequest(User $user, Plan $plan, string $period, ?string $couponCode): Order
{
    // Validation
    $planService = new PlanService($plan);
    $planService->validatePurchase($user, $period);
    
    // Hook call
    HookManager::call('order.create.before', [$user, $plan, $period, $couponCode]);
    
    // Database transaction
    return DB::transaction(function () use (...) {
        $order = new Order([...]);
        // ... logic
        $order->save();
        
        HookManager::call('order.create.after', $order);
        return $order;
    });
}
```

### 6.6 Model/Database Layer

```php
// Eloquent Model
class User extends Authenticatable
{
    protected $table = 'v2_user';
    protected $dateFormat = 'U';  // Unix timestamp
    protected $guarded = ['id'];
    
    protected $casts = [
        'created_at' => 'timestamp',
        'updated_at' => 'timestamp',
        'banned' => 'boolean',
        'is_admin' => 'boolean',
    ];
    
    // Relationships
    public function plan(): BelongsTo
    {
        return $this->belongsTo(Plan::class, 'plan_id', 'id');
    }
    
    public function orders(): HasMany
    {
        return $this->hasMany(Order::class, 'user_id', 'id');
    }
}
```

### 6.7 Caching Layer

```php
// Settings are cached
class Setting
{
    public function get(string $key): mixed
    {
        return Cache::remember(
            "setting:{$key}",
            3600,
            fn() => \App\Models\Setting::where('name', $key)->value('value')
        );
    }
    
    public function save(array $settings): void
    {
        foreach ($settings as $key => $value) {
            \App\Models\Setting::updateOrCreate(
                ['name' => $key],
                ['value' => $value]
            );
            Cache::forget("setting:{$key}");
        }
    }
}

// Server data is cached
Cache::put(CacheKey::get("SERVER_{$type}_LAST_CHECK_AT", $serverId), time());
Cache::get(CacheKey::get("SERVER_{$type}_ONLINE_USER", $serverId));
```

### 6.8 API Response Format

```php
// ApiResponse trait
trait ApiResponse
{
    protected function success($data = null, string $message = 'Success')
    {
        return response()->json([
            'data' => $data,
            'message' => $message
        ], 200);
    }
    
    protected function fail($error)
    {
        return response()->json([
            'message' => $error[1] ?? 'Error',
            'errors' => null
        ], $error[0] ?? 500);
    }
}
```


---

## 7. FULL API REFERENCE

### 7.1 Authentication APIs (Passport)

#### POST /api/v1/passport/auth/register
**Purpose**: User registration
**Authentication**: None
**Middleware**: None
**Controller**: `V1\Passport\AuthController@register`
**Request Fields**:
```json
{
    "email": "user@example.com",      // Required, valid email
    "password": "password123",        // Required, min 8 chars
    "invite_code": "ABC123",          // Optional
    "email_code": "123456"            // Required if email verification enabled
}
```
**Response**:
```json
{
    "data": {
        "token": "1|abc123...",
        "auth_data": "..."
    }
}
```
**Hooks Triggered**: `user.register.before`, `user.register.after`

#### POST /api/v1/passport/auth/login
**Purpose**: User login
**Authentication**: None
**Controller**: `V1\Passport\AuthController@login`
**Request Fields**:
```json
{
    "email": "user@example.com",
    "password": "password123"
}
```
**Response**:
```json
{
    "data": {
        "token": "1|abc123...",
        "auth_data": "...",
        "is_admin": false
    }
}
```
**Hooks Triggered**: `user.login.after`

#### POST /api/v1/passport/auth/forget
**Purpose**: Password reset
**Authentication**: None
**Controller**: `V1\Passport\AuthController@forget`
**Request Fields**:
```json
{
    "email": "user@example.com",
    "password": "newpassword123",
    "email_code": "123456"
}
```
**Hooks Triggered**: `user.password.reset.after`

#### POST /api/v1/passport/comm/sendEmailVerify
**Purpose**: Send email verification code
**Controller**: `V1\Passport\CommController@sendEmailVerify`
**Request Fields**:
```json
{
    "email": "user@example.com"
}
```

---

### 7.2 User Center APIs

#### GET /api/v1/user/info
**Purpose**: Get user information
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\UserController@info`
**Response**:
```json
{
    "data": {
        "email": "user@example.com",
        "balance": 10000,
        "transfer_enable": 107374182400,
        "u": 1073741824,
        "d": 5368709120,
        "expired_at": 1735689600,
        "plan_id": 1,
        "invite_user_id": null
    }
}
```
**Hooks Triggered**: None

#### POST /api/v1/user/update
**Purpose**: Update user profile
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\UserController@update`
**Request Fields**:
```json
{
    "remind_expire": true,
    "remind_traffic": true
}
```

#### POST /api/v1/user/changePassword
**Purpose**: Change password
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\UserController@changePassword`
**Request Fields**:
```json
{
    "old_password": "oldpass123",
    "new_password": "newpass123"
}
```

#### GET /api/v1/user/getSubscribe
**Purpose**: Get subscription info
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\UserController@getSubscribe`
**Response**:
```json
{
    "data": {
        "plan_id": 1,
        "token": "subscription_token",
        "expired_at": 1735689600,
        "u": 1073741824,
        "d": 5368709120,
        "transfer_enable": 107374182400,
        "subscribe_url": "https://example.com/s/token"
    }
}
```

#### GET /api/v1/user/resetSecurity
**Purpose**: Reset user security token
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\UserController@resetSecurity`

---

### 7.3 Subscription/Order APIs

#### POST /api/v1/user/order/save
**Purpose**: Create new order
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\OrderController@save`
**Request Fields**:
```json
{
    "plan_id": 1,
    "period": "month_price",
    "coupon_code": "DISCOUNT10"
}
```
**Response**:
```json
{
    "data": {
        "trade_no": "202411250001",
        "plan_id": 1,
        "total_amount": 9900,
        "status": 0
    }
}
```
**Hooks Triggered**: `order.create.before`, `order.create.after`

#### POST /api/v1/user/order/checkout
**Purpose**: Checkout order (get payment URL)
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\OrderController@checkout`
**Request Fields**:
```json
{
    "trade_no": "202411250001",
    "method": "EPay"
}
```
**Response**:
```json
{
    "data": {
        "type": 1,
        "data": "https://pay.example.com/submit?..."
    }
}
```

#### GET /api/v1/user/order/fetch
**Purpose**: Get user orders
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\OrderController@fetch`
**Query Parameters**: `status`, `page`, `page_size`

#### GET /api/v1/user/order/detail
**Purpose**: Get order details
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\OrderController@detail`
**Query Parameters**: `trade_no`

#### POST /api/v1/user/order/cancel
**Purpose**: Cancel pending order
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\OrderController@cancel`
**Hooks Triggered**: `order.cancel.before`, `order.cancel.after`

---

### 7.4 Server/Node APIs

#### GET /api/v1/user/server/fetch
**Purpose**: Get available servers
**Authentication**: Bearer Token
**Middleware**: `user`
**Controller**: `V1\User\ServerController@fetch`
**Response**:
```json
{
    "data": [
        {
            "id": 1,
            "name": "US Server",
            "type": "vmess",
            "host": "us.example.com",
            "port": 443,
            "tags": ["Premium", "US"]
        }
    ]
}
```

#### GET /api/v1/server/UniProxy/config
**Purpose**: Node gets its configuration
**Authentication**: Node Token (header)
**Middleware**: `server`
**Controller**: `V1\Server\UniProxyController@config`
**Headers**: `token: node_communication_key`

#### GET /api/v1/server/UniProxy/user
**Purpose**: Node gets active user list
**Authentication**: Node Token
**Middleware**: `server`
**Controller**: `V1\Server\UniProxyController@user`
**Response**:
```json
{
    "data": [
        {
            "id": 1,
            "uuid": "user-uuid-here",
            "speed_limit": 100,
            "device_limit": 3
        }
    ]
}
```

#### POST /api/v1/server/UniProxy/push
**Purpose**: Node reports traffic data
**Authentication**: Node Token
**Middleware**: `server`
**Controller**: `V1\Server\UniProxyController@push`
**Request Fields**:
```json
[
    {
        "uid": 1,
        "u": 1073741824,
        "d": 5368709120
    }
]
```

#### POST /api/v1/server/UniProxy/alive
**Purpose**: Node heartbeat + online count
**Authentication**: Node Token
**Middleware**: `server`
**Controller**: `V1\Server\UniProxyController@alive`
**Request Fields**:
```json
{
    "online_user": 150
}
```

---

### 7.5 Admin Panel APIs (V2)

#### GET /api/v2/{admin_path}/config/fetch
**Purpose**: Get all system configuration
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\ConfigController@fetch`

#### POST /api/v2/{admin_path}/config/save
**Purpose**: Save system configuration
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\ConfigController@save`

#### GET /api/v2/{admin_path}/user/fetch
**Purpose**: Get users list with filters
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\UserController@fetch`
**Query Parameters**: `filter`, `sort`, `page`, `page_size`

#### POST /api/v2/{admin_path}/user/update
**Purpose**: Update user data
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\UserController@update`

#### POST /api/v2/{admin_path}/user/ban
**Purpose**: Ban/Unban user
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\UserController@ban`

#### GET /api/v2/{admin_path}/plan/fetch
**Purpose**: Get subscription plans
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\PlanController@fetch`

#### POST /api/v2/{admin_path}/plan/save
**Purpose**: Create new plan
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\PlanController@save`

#### GET /api/v2/{admin_path}/server/manage/getNodes
**Purpose**: Get all server nodes
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\Server\ManageController@getNodes`

#### POST /api/v2/{admin_path}/server/manage/save
**Purpose**: Create/Update server
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\Server\ManageController@save`

#### GET /api/v2/{admin_path}/stat/getOverride
**Purpose**: Get dashboard statistics
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\StatController@getOverride`

#### GET /api/v2/{admin_path}/plugin/getPlugins
**Purpose**: Get all plugins
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\PluginController@index`

#### POST /api/v2/{admin_path}/plugin/install
**Purpose**: Install plugin
**Authentication**: Admin Bearer Token
**Middleware**: `admin`, `log`
**Controller**: `V2\Admin\PluginController@install`

---

### 7.6 Payment Callback APIs

#### ANY /api/v1/guest/payment/notify/{method}/{uuid}
**Purpose**: Payment gateway callback
**Authentication**: None (signature verification)
**Controller**: `V1\Guest\PaymentController@notify`
**Hooks Triggered**: 
- `payment.notify.before`
- `payment.notify.verified` (on success)
- `payment.notify.failed` (on failure)
- `payment.notify.success` (after order update)

---

### 7.7 Subscription Delivery

#### GET /{subscribe_path}/{token}
**Purpose**: Deliver subscription configuration
**Authentication**: Token in URL
**Middleware**: `client`
**Controller**: `V1\Client\ClientController@subscribe`
**Headers Accepted**: `User-Agent` (determines format)
**Response Formats**: Clash, ClashMeta, Surge, Shadowrocket, SingBox, QuantumultX, etc.
**Hooks Triggered**: `client.subscribe.before`, `client.subscribe.servers`

---

## 8. HOOKS, EVENTS, LISTENERS & EXTENSION POINTS

### 8.1 Complete Hook Reference

#### Action Hooks (Triggered Events)

| Hook Name | Location | Parameters | Purpose |
|-----------|----------|------------|---------|
| `user.register.before` | RegisterService | Request | Before user creation |
| `user.register.after` | RegisterService | User | After user created |
| `user.login.after` | LoginService | User | After successful login |
| `user.password.reset.after` | LoginService | User | After password reset |
| `user.telegram.bind.after` | Telegram Plugin | User | After Telegram binding |
| `order.create.before` | OrderService | [User, Plan, period, coupon] | Before order creation |
| `order.create.after` | OrderService | Order | After order created |
| `order.after_create` | OrderService | Order | Legacy: After order created |
| `order.open.before` | OrderService | Order | Before order opened |
| `order.open.after` | OrderService | Order | After order opened |
| `order.cancel.before` | OrderService | Order | Before order cancelled |
| `order.cancel.after` | OrderService | Order | After order cancelled |
| `payment.notify.before` | PaymentController | [method, uuid, request] | Before payment callback |
| `payment.notify.verified` | PaymentController | array | Payment verified |
| `payment.notify.failed` | PaymentController | [method, uuid, request] | Payment failed |
| `payment.notify.success` | PaymentController | Order | Payment successful |
| `traffic.reset.after` | TrafficResetService | User | After traffic reset |
| `ticket.create.after` | TicketController | Ticket | After ticket created |
| `ticket.reply.user.after` | TicketController | Ticket | After user reply |
| `ticket.reply.admin.after` | TicketService | [Ticket, Message] | After admin reply |
| `ticket.close.after` | TicketController | Ticket | After ticket closed |
| `client.subscribe.before` | ClientController | None | Before subscription delivery |
| `client.subscribe.unavailable` | ClientController | None | Subscription unavailable |
| `telegram.message.before` | TelegramController | Message | Before Telegram message |
| `telegram.message.after` | TelegramController | Message | After Telegram message |
| `telegram.message.unhandled` | TelegramController | Message | Unhandled Telegram message |
| `telegram.message.error` | TelegramController | [Message, Exception] | Telegram error |

#### Filter Hooks (Data Modification)

| Hook Name | Location | Parameters | Return | Purpose |
|-----------|----------|------------|--------|---------|
| `guest_comm_config` | CommController | config array | config array | Modify public config |
| `user.subscribe.response` | UserController | User | User | Modify user data |
| `user.knowledge.resource` | KnowledgeResource | data | data | Modify knowledge |
| `available_payment_methods` | PaymentService | methods array | methods array | Add payment methods |
| `client.subscribe.servers` | ClientController | [servers, user, request] | servers | Modify server list |
| `server.users.get` | ServerService | [users, node] | users | Modify user list for node |
| `traffic.process.before` | UserService | [server, protocol, data] | [server, protocol, data] | Before traffic process |
| `traffic.before_process` | UserService | [server, protocol, data] | [server, protocol, data] | Legacy |
| `protocol.servers.filtered` | AbstractProtocol | servers | servers | Filter servers |
| `subscribe.url` | Helper | url | url | Modify subscribe URL |
| `telegram.bot.commands` | TelegramService | commands | commands | Add bot commands |
| `telegram.message.handle` | TelegramController | [handled, message] | handled | Handle Telegram message |

### 8.2 How to Register Hooks (Plugin Development)

```php
// In Plugin boot() method
public function boot(): void
{
    // Register action hook listener
    $this->listen('order.create.after', function ($order) {
        // Do something after order is created
        Log::info('New order created', ['order_id' => $order->id]);
    }, 20);  // Priority: 20 (default)
    
    // Register filter hook
    $this->filter('guest_comm_config', function ($config) {
        // Modify config
        $config['my_plugin_enabled'] = true;
        return $config;
    }, 10);  // Priority: 10 (runs before default)
    
    // Intercept response
    $this->listen('user.register.before', function ($request) {
        if ($this->shouldBlock($request)) {
            $this->intercept(['message' => 'Registration blocked']);
        }
    });
}
```

### 8.3 Service Providers

| Provider | Purpose | Location |
|----------|---------|----------|
| `RouteServiceProvider` | Register routes | `app/Providers/RouteServiceProvider.php` |
| `EventServiceProvider` | Register events | `app/Providers/EventServiceProvider.php` |
| `PluginServiceProvider` | Plugin initialization | `app/Providers/PluginServiceProvider.php` |
| `SettingServiceProvider` | Settings loading | `app/Providers/SettingServiceProvider.php` |
| `HorizonServiceProvider` | Queue config | `app/Providers/HorizonServiceProvider.php` |
| `OctaneServiceProvider` | Octane lifecycle | `app/Providers/OctaneServiceProvider.php` |
| `ProtocolServiceProvider` | Protocol registration | `app/Providers/ProtocolServiceProvider.php` |
| `AuthServiceProvider` | Auth policies | `app/Providers/AuthServiceProvider.php` |

### 8.4 Middleware Extension Points

```php
// Add custom middleware in Kernel.php
protected $middlewareAliases = [
    'my_middleware' => \Plugin\MyPlugin\Middleware\MyMiddleware::class,
];

// Or in plugin routes
Route::middleware(['user', 'my_middleware'])->group(function () {
    // Routes here
});
```

### 8.5 Database Observers

```php
// User model has observer registered
User::observe(UserObserver::class);

// UserObserver handles:
// - creating: Before user created
// - created: After user created  
// - updating: Before user updated
// - updated: After user updated
```

### 8.6 Dependency Injection Container Bindings

```php
// Custom bindings in AppServiceProvider or PluginServiceProvider
$this->app->bind('my.service', function ($app) {
    return new MyService();
});

// Singleton binding
$this->app->singleton(PluginManager::class);
$this->app->singleton(Setting::class);
```


---

## 9. DATABASE ANALYSIS

### 9.1 Complete Database Schema

#### Table: v2_user
**Purpose**: Core user accounts

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| invite_user_id | int(11) | NULL | Referrer user ID |
| telegram_id | bigint(20) | NULL | Telegram ID |
| email | varchar(64) | - | Unique email |
| password | varchar(64) | - | Hashed password |
| password_algo | char(10) | NULL | Hash algorithm |
| password_salt | char(10) | NULL | Password salt |
| balance | int(11) | 0 | Account balance (cents) |
| discount | int(11) | NULL | Personal discount % |
| commission_type | tinyint(4) | 0 | 0:system, 1:period, 2:onetime |
| commission_rate | int(11) | NULL | Personal commission rate |
| commission_balance | int(11) | 0 | Pending commission |
| t | int(11) | 0 | Last traffic report time |
| u | bigint(20) | 0 | Upload bytes |
| d | bigint(20) | 0 | Download bytes |
| transfer_enable | bigint(20) | 0 | Total allowed bytes |
| banned | tinyint(1) | 0 | Ban status |
| is_admin | tinyint(1) | 0 | Admin flag |
| is_staff | tinyint(1) | 0 | Staff flag |
| last_login_at | int(11) | NULL | Last login timestamp |
| last_login_ip | int(11) | NULL | Last login IP |
| uuid | varchar(36) | - | Unique user UUID |
| group_id | int(11) | NULL | Server group access |
| plan_id | int(11) | NULL | Current plan |
| speed_limit | int(11) | NULL | Speed limit Mbps |
| device_limit | int(11) | NULL | Device limit |
| remind_expire | tinyint(4) | 1 | Expiry reminder |
| remind_traffic | tinyint(4) | 1 | Traffic reminder |
| token | char(32) | - | Subscription token |
| expired_at | bigint(20) | 0 | Expiry timestamp |
| next_reset_at | int(11) | NULL | Next traffic reset |
| last_reset_at | int(11) | NULL | Last traffic reset |
| reset_count | int(11) | 0 | Reset count |
| online_count | int(11) | NULL | Online devices |
| last_online_at | timestamp | NULL | Last online time |
| remarks | text | NULL | Admin notes |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

**Indexes**: `email` (unique), `u_d_expired_at_group_id_banned_transfer_enable`, `t`, `online_count`, `created_at`, `next_reset_at`

#### Table: v2_order
**Purpose**: Order transactions

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| user_id | int(11) | - | User ID |
| plan_id | int(11) | - | Plan ID |
| payment_id | int(11) | NULL | Payment method ID |
| period | varchar(255) | - | Subscription period |
| trade_no | varchar(36) | - | Order number |
| total_amount | int(11) | - | Total amount (cents) |
| handling_amount | int(11) | NULL | Handling fee |
| balance_amount | int(11) | NULL | Balance used |
| refund_amount | int(11) | NULL | Refund amount |
| surplus_amount | int(11) | NULL | Surplus from upgrade |
| discount_amount | int(11) | NULL | Discount applied |
| type | int(11) | - | 1:new, 2:renew, 3:upgrade, 4:reset |
| status | int(11) | 0 | 0:pending, 1:processing, 2:cancelled, 3:completed, 4:discounted |
| surplus_order_ids | json | NULL | IDs of surplus orders |
| coupon_id | int(11) | NULL | Coupon used |
| invite_user_id | int(11) | NULL | Inviter for commission |
| commission_status | int(11) | 0 | Commission status |
| commission_balance | int(11) | NULL | Commission amount |
| paid_at | int(11) | NULL | Payment timestamp |
| callback_no | varchar(255) | NULL | Gateway callback ID |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

#### Table: v2_plan
**Purpose**: Subscription plans

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| group_id | int(11) | - | Server group |
| transfer_enable | int(11) | - | Traffic GB |
| speed_limit | int(11) | NULL | Speed Mbps |
| device_limit | int(11) | NULL | Device limit |
| name | varchar(255) | - | Plan name |
| show | tinyint(1) | 0 | Public visibility |
| sort | int(11) | NULL | Sort order |
| renew | tinyint(1) | 1 | Allow renewal |
| content | text | NULL | Description |
| prices | json | NULL | Pricing structure |
| reset_traffic_method | tinyint(4) | NULL | Traffic reset method |
| capacity_limit | int(11) | NULL | Max users |
| sell | tinyint(1) | 1 | For sale |
| tags | json | NULL | Tags |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

#### Table: v2_server
**Purpose**: Proxy server nodes

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | bigint(20) | AUTO_INCREMENT | Primary key |
| type | varchar(255) | - | Protocol type |
| code | varchar(255) | NULL | Server code |
| parent_id | int(10) | NULL | Parent server ID |
| group_ids | json | NULL | Group IDs |
| route_ids | json | NULL | Route IDs |
| name | varchar(255) | - | Server name |
| rate | decimal(8,2) | - | Traffic rate |
| rate_time_enable | tinyint(1) | 0 | Dynamic rate |
| rate_time_ranges | json | NULL | Rate time ranges |
| tags | json | NULL | Tags |
| host | varchar(255) | - | Server host |
| port | varchar(255) | - | Client port |
| server_port | int(11) | - | Server port |
| protocol_settings | json | NULL | Protocol config |
| show | tinyint(1) | 0 | Visibility |
| sort | int(10) | NULL | Sort order |
| created_at | timestamp | NULL | Created time |
| updated_at | timestamp | NULL | Updated time |

#### Table: v2_payment
**Purpose**: Payment gateways

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| uuid | char(32) | - | Unique ID |
| payment | varchar(16) | - | Gateway type |
| name | varchar(255) | - | Display name |
| icon | varchar(255) | NULL | Icon URL |
| config | text | - | Gateway config |
| notify_domain | varchar(255) | NULL | Callback domain |
| handling_fee_fixed | int(11) | NULL | Fixed fee |
| handling_fee_percent | decimal(5,2) | NULL | Percent fee |
| enable | tinyint(1) | 0 | Enabled |
| sort | int(11) | NULL | Sort order |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

#### Table: v2_coupon
**Purpose**: Discount coupons

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| code | varchar(255) | - | Coupon code |
| name | varchar(255) | - | Display name |
| type | tinyint(4) | - | 1:percent, 2:fixed |
| value | int(11) | - | Discount value |
| show | tinyint(1) | 0 | Public visibility |
| limit_use | int(11) | NULL | Usage limit |
| limit_use_with_user | int(11) | NULL | Per user limit |
| limit_plan_ids | varchar(255) | NULL | Plan restrictions |
| limit_period | varchar(255) | NULL | Period restrictions |
| started_at | int(11) | - | Start timestamp |
| ended_at | int(11) | - | End timestamp |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

#### Table: v2_ticket
**Purpose**: Support tickets

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | int(11) | AUTO_INCREMENT | Primary key |
| user_id | int(11) | - | User ID |
| subject | varchar(255) | - | Ticket subject |
| level | int(11) | - | Priority level |
| status | int(11) | 0 | 0:open, 1:closed |
| reply_status | int(11) | 1 | 0:pending, 1:replied |
| created_at | int(11) | - | Created timestamp |
| updated_at | int(11) | - | Updated timestamp |

#### Table: v2_settings
**Purpose**: System configuration

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | bigint(20) | AUTO_INCREMENT | Primary key |
| group | varchar(255) | NULL | Setting group |
| type | varchar(255) | NULL | Setting type |
| name | varchar(255) | - | Setting key |
| value | mediumtext | NULL | Setting value |
| created_at | timestamp | NULL | Created time |
| updated_at | timestamp | NULL | Updated time |

#### Table: v2_plugins
**Purpose**: Installed plugins

| Column | Type | Default | Description |
|--------|------|---------|-------------|
| id | bigint(20) | AUTO_INCREMENT | Primary key |
| code | varchar(100) | - | Plugin code |
| name | varchar(255) | - | Plugin name |
| type | varchar(50) | 'feature' | feature/payment |
| version | varchar(50) | - | Version |
| is_enabled | tinyint(1) | 0 | Enabled status |
| config | json | NULL | Plugin config |
| installed_at | timestamp | NULL | Install time |
| created_at | timestamp | NULL | Created time |
| updated_at | timestamp | NULL | Updated time |

### 9.2 Entity Relationship Diagram (ASCII)

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   v2_user   │────<│  v2_order   │>────│   v2_plan   │
│             │     │             │     │             │
│ id (PK)     │     │ id (PK)     │     │ id (PK)     │
│ plan_id(FK) │─────│ user_id(FK) │     │ group_id    │
│ group_id    │     │ plan_id(FK) │─────│ name        │
│ invite_id   │     │ payment_id  │     │ prices      │
│ email       │     │ coupon_id   │     └─────────────┘
│ balance     │     │ trade_no    │            │
│ u, d        │     │ total_amount│            │
│ expired_at  │     │ status      │            │
└─────────────┘     └─────────────┘            │
      │                   │                    │
      │                   │                    ▼
      │             ┌─────────────┐    ┌─────────────┐
      │             │ v2_payment  │    │v2_server_grp│
      │             │             │    │             │
      │             │ id (PK)     │    │ id (PK)     │
      │             │ payment     │    │ name        │
      │             │ config      │    └─────────────┘
      │             └─────────────┘           │
      │                                       │
      ▼                                       ▼
┌─────────────┐                       ┌─────────────┐
│  v2_ticket  │                       │  v2_server  │
│             │                       │             │
│ id (PK)     │                       │ id (PK)     │
│ user_id(FK) │                       │ type        │
│ subject     │                       │ group_ids   │
│ status      │                       │ host        │
└─────────────┘                       │ port        │
      │                               │protocol_set │
      ▼                               └─────────────┘
┌─────────────┐
│v2_ticket_msg│
│             │
│ id (PK)     │
│ ticket_id   │
│ user_id     │
│ message     │
└─────────────┘
```


---

## 10. BUSINESS LOGIC FLOWS

### 10.1 Subscription Lifecycle

```
┌──────────────────────────────────────────────────────────────┐
│                   SUBSCRIPTION LIFECYCLE                      │
└──────────────────────────────────────────────────────────────┘

1. PURCHASE
   User selects plan → Creates order → Pays → Order completed
   │
   ├── OrderService::createFromRequest()
   │   ├── Validate plan availability
   │   ├── Apply coupon if provided
   │   ├── Apply user discount
   │   ├── Set order type (new/renew/upgrade)
   │   ├── Calculate commission
   │   └── Deduct balance if available
   │
   └── OrderService::open()
       ├── Update user plan_id, group_id
       ├── Set transfer_enable (traffic)
       ├── Set/extend expired_at
       ├── Reset traffic counters (if new)
       └── Apply speed_limit, device_limit

2. RENEWAL
   Same plan, extend expiration
   │
   └── expired_at = current_expired + period_months

3. UPGRADE
   Different plan, calculate surplus
   │
   ├── Calculate remaining value of current plan
   ├── Apply as discount to new order
   └── Mark old orders as STATUS_DISCOUNTED

4. TRAFFIC RESET
   Monthly traffic reset based on plan settings
   │
   ├── reset_traffic_method: 0=never, 1=first_day, 2=order_day
   ├── Reset u=0, d=0
   ├── Update next_reset_at
   └── Log in v2_traffic_reset_logs

5. EXPIRY
   User expires → Limited access
   │
   ├── Daily check via check:order command
   ├── Send reminder emails before expiry
   └── User cannot access servers when expired
```

### 10.2 User Lifecycle

```
1. REGISTRATION
   │
   ├── Validate email/invite code
   ├── Check registration limits
   ├── Hash password (bcrypt)
   ├── Generate UUID and token
   ├── Link invite_user_id if invited
   ├── Create API token (Sanctum)
   └── Trigger: user.register.after

2. LOGIN
   │
   ├── Validate credentials
   ├── Check banned status
   ├── Generate new API token
   ├── Update last_login_at/ip
   └── Trigger: user.login.after

3. EMAIL VERIFICATION
   │
   ├── Generate 6-digit code
   ├── Store in cache (5 min TTL)
   ├── Send via configured mail driver
   └── Verify on registration/password reset

4. DEVICE TRACKING
   │
   ├── Online count via node heartbeat
   ├── Cache: SERVER_{TYPE}_ONLINE_USER
   └── device_limit enforcement on nodes

5. NOTIFICATION TRIGGERS
   │
   ├── remind_expire: Before expiry
   ├── remind_traffic: Low traffic warning
   └── Ticket reply notifications
```

### 10.3 Order Lifecycle

```
STATUS FLOW:
┌──────────┐    ┌────────────┐    ┌────────────┐
│ PENDING  │───>│ PROCESSING │───>│ COMPLETED  │
│ (0)      │    │ (1)        │    │ (3)        │
└────┬─────┘    └────────────┘    └────────────┘
     │                                   
     ▼                             ┌────────────┐
┌──────────┐                       │ DISCOUNTED │
│ CANCELLED│                       │ (4)        │
│ (2)      │                       └────────────┘
└──────────┘

PROCESS:
1. Create order → status=0 (PENDING)
2. User pays via gateway
3. Gateway callback → status=1 (PROCESSING)
4. OrderHandleJob → status=3 (COMPLETED)
   OR on upgrade, old orders → status=4 (DISCOUNTED)
5. User cancels → status=2 (CANCELLED), refund balance
```

### 10.4 Node Lifecycle

```
1. NODE CREATION
   │
   ├── Admin creates server via API
   ├── Assign to group_ids
   ├── Configure protocol_settings
   └── Set rate multiplier

2. NODE HEARTBEAT
   │
   ├── Node calls /server/UniProxy/alive
   ├── Updates last_check_at cache
   ├── Reports online_user count
   └── Reports load status (CPU, memory)

3. TRAFFIC REPORTING
   │
   ├── Node calls /server/UniProxy/push
   ├── Data: [{uid, u, d}, ...]
   ├── TrafficFetchJob processes data
   ├── Updates user u/d counters
   └── StatUserJob records statistics

4. ONLINE/OFFLINE STATUS
   │
   ├── Check interval: 300 seconds (5 min)
   ├── STATUS_OFFLINE: No heartbeat
   ├── STATUS_ONLINE_NO_PUSH: Heartbeat, no traffic
   └── STATUS_ONLINE: Full operation
```

---

## 11. PAYMENT GATEWAY FRAMEWORK

### 11.1 Payment Plugin Interface

```php
interface PaymentInterface
{
    public function form(): array;
    public function pay($order): array;
    public function notify($params): array|bool;
}
```

### 11.2 Plugin Structure

```
plugins/YourPayment/
├── Plugin.php           # Main class
├── config.json          # Configuration
└── README.md           # Documentation
```

### 11.3 Implementing a Payment Plugin

```php
namespace Plugin\YourPayment;

use App\Services\Plugin\AbstractPlugin;
use App\Contracts\PaymentInterface;

class Plugin extends AbstractPlugin implements PaymentInterface
{
    public function boot(): void
    {
        // Register payment method
        $this->filter('available_payment_methods', function ($methods) {
            $methods['YourPayment'] = [
                'name' => $this->getConfig('display_name', 'My Payment'),
                'icon' => $this->getConfig('icon', '💳'),
                'plugin_code' => $this->getPluginCode(),
                'type' => 'plugin'
            ];
            return $methods;
        });
    }

    public function form(): array
    {
        return [
            'api_key' => [
                'label' => 'API Key',
                'type' => 'string',
                'required' => true
            ],
            'secret' => [
                'label' => 'Secret Key',
                'type' => 'string',
                'required' => true
            ]
        ];
    }

    public function pay($order): array
    {
        // Generate payment URL
        $params = [
            'amount' => $order['total_amount'] / 100,
            'order_id' => $order['trade_no'],
            'notify_url' => $order['notify_url'],
            'return_url' => $order['return_url']
        ];
        
        // Return redirect URL
        return [
            'type' => 1,  // 0: QR code, 1: redirect URL
            'data' => 'https://payment.example.com/pay?' . http_build_query($params)
        ];
    }

    public function notify($params): array|bool
    {
        // Verify signature
        if (!$this->verifySignature($params)) {
            return false;
        }
        
        // Return order info
        return [
            'trade_no' => $params['order_id'],
            'callback_no' => $params['transaction_id']
        ];
    }
}
```

### 11.4 Payment Flow

```
1. User selects payment method
2. PaymentService::pay() called
3. Plugin generates payment URL
4. User redirected to gateway
5. User completes payment
6. Gateway sends callback to /api/v1/guest/payment/notify/{method}/{uuid}
7. PaymentController::notify() processes
8. Plugin::notify() verifies signature
9. OrderService::paid() updates status
10. OrderHandleJob opens order
```

---

## 12. CRON JOBS / QUEUE JOBS

### 12.1 Scheduled Commands

| Command | Schedule | Purpose |
|---------|----------|---------|
| `xboard:statistics` | Daily 0:10 | Daily statistics |
| `check:order` | Every minute | Check pending orders |
| `check:commission` | Every minute | Process commissions |
| `check:ticket` | Every minute | Ticket notifications |
| `reset:traffic` | Every minute | Reset user traffic |
| `reset:log` | Daily | Clean old logs |
| `send:remindMail` | Daily 11:30 | Expiry reminders |
| `horizon:snapshot` | Every 5 min | Queue metrics |
| `cleanup:expired-online-status` | Every minute | Clean online cache |

### 12.2 Queue Jobs

| Job | Queue | Purpose |
|-----|-------|---------|
| `OrderHandleJob` | default | Process paid orders |
| `SendEmailJob` | default | Send emails |
| `SendTelegramJob` | default | Send Telegram messages |
| `StatServerJob` | default | Record server stats |
| `StatUserJob` | default | Record user stats |
| `TrafficFetchJob` | default | Process traffic data |
| `UpdateAliveDataJob` | default | Update node status |

### 12.3 Adding Custom Scheduled Tasks (Plugin)

```php
public function schedule(Schedule $schedule): void
{
    $schedule->call(function () {
        // Your task logic
    })->hourly();
    
    $schedule->command('your-plugin:task')->daily();
}
```

---

## 13. FRONTEND ANALYSIS

### 13.1 Admin Panel (Vue.js)

**Location**: `public/assets/admin/`
**Entry Point**: `/{admin_path}` (configurable)
**Framework**: Vue.js 3 with Ant Design

**Structure**:
```
public/assets/admin/
├── assets/
│   ├── index.js         # Main app bundle
│   └── vendor.js        # Vendor dependencies
├── locales/
│   ├── zh-CN.js         # Chinese
│   ├── en-US.js         # English
│   └── ko-KR.js         # Korean
└── index.html           # SPA entry
```

### 13.2 User Theme System

**Location**: `theme/{theme_name}/`
**Entry Point**: `/`

**Theme Structure**:
```
theme/Xboard/
├── dashboard.blade.php  # Main template
├── assets/              # Static assets
│   ├── css/
│   └── js/
└── config.json          # Theme configuration
```

### 13.3 Creating a New Theme

1. Create directory: `theme/MyTheme/`
2. Create `dashboard.blade.php`:
```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $title }}</title>
    <link rel="stylesheet" href="/theme/{{ $theme }}/assets/css/app.css">
</head>
<body>
    <div id="app"></div>
    <script>
        window.settings = {!! json_encode($theme_config) !!};
    </script>
    <script src="/theme/{{ $theme }}/assets/js/app.js"></script>
</body>
</html>
```

3. Create `config.json`:
```json
{
    "name": "My Theme",
    "version": "1.0.0",
    "configs": {
        "primary_color": {
            "type": "color",
            "default": "#1890ff",
            "label": "Primary Color"
        }
    }
}
```

### 13.4 API Consumption Pattern

```javascript
// User authentication
const response = await fetch('/api/v1/passport/auth/login', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({ email, password })
});
const { data } = await response.json();
const token = data.token;

// Authenticated requests
const userInfo = await fetch('/api/v1/user/info', {
    headers: {
        'Authorization': `Bearer ${token}`
    }
});
```

### 13.5 Internationalization (i18n)

**Admin Panel**: Pre-compiled locales in `locales/*.js`
**User Theme**: Depends on theme implementation
**Backend**: Laravel localization in `resources/lang/`


---

## 14. SECURITY ANALYSIS

### 14.1 Authentication Security

| Aspect | Implementation | Status |
|--------|---------------|--------|
| Password Hashing | Bcrypt (Laravel default) | ✅ Secure |
| API Authentication | Laravel Sanctum tokens | ✅ Secure |
| Token Storage | Database (personal_access_tokens) | ✅ Secure |
| Session Handling | Stateless API | ✅ Secure |
| Admin Path | Configurable secure_path | ✅ Good |

### 14.2 Input Validation

| Controller | Validation | Status |
|------------|------------|--------|
| AuthController | Form Request classes | ✅ Good |
| OrderController | Form Request classes | ✅ Good |
| AdminController | Form Request classes | ✅ Good |
| ServerController | Node token validation | ✅ Good |

### 14.3 Potential Vulnerabilities

| Issue | Severity | Location | Recommendation |
|-------|----------|----------|----------------|
| Rate limiting disabled | MEDIUM | Kernel.php | Enable throttle middleware (prevents brute force/DoS) |
| Debug mode in production | HIGH | .env | CRITICAL: Set APP_DEBUG=false (exposes sensitive data) |
| SQL injection | LOW | All controllers | Using Eloquent ORM (safe) |
| XSS in templates | LOW | Blade templates | Using {{ }} escaping |
| CSRF protection | INFO | Disabled for API | Acceptable for API-only |

### 14.4 Payment Security

| Aspect | Implementation |
|--------|---------------|
| Signature Verification | Plugin-specific |
| Callback Validation | Plugin notify() method |
| Amount Verification | Should verify in callback |
| SSL/TLS | Enforced via nginx |

### 14.5 Recommendations

1. **Enable Rate Limiting**: Uncomment throttle middleware in Kernel.php
2. **Secure Admin Path**: Use strong, unpredictable admin path
3. **Monitor Failed Logins**: Implement login attempt tracking
4. **Audit Logging**: Enable RequestLog middleware for all admin routes
5. **Plugin Sandboxing**: Review plugin code before installation

---

## 15. PERFORMANCE ANALYSIS

### 15.1 Database Optimization

| Table | Indexes | Status |
|-------|---------|--------|
| v2_user | email, multi-column composite | ✅ Good |
| v2_order | trade_no, user_id, updated_at | ✅ Good |
| v2_stat_user | composite indexes | ✅ Good |
| v2_server | sort, type_code | ✅ Good |

### 15.2 Caching Strategy

| Data | Cache Type | TTL |
|------|------------|-----|
| Settings | Redis | Permanent (invalidate on change) |
| Server Status | Redis | 300 seconds |
| User Sessions | Redis/Database | 120 minutes |

### 15.3 Heavy Operations

| Operation | Impact | Optimization |
|-----------|--------|--------------|
| User list fetch | Medium | Pagination, selective columns |
| Statistics calculation | High | Background jobs, caching |
| Traffic processing | High | Queue jobs, batch processing |
| Server sync | Medium | Cached user lists |

### 15.4 Recommendations

1. **Enable Query Caching**: Cache frequent queries
2. **Optimize N+1 Queries**: Use eager loading
3. **Index Optimization**: Add composite indexes for common filters
4. **Queue Heavy Tasks**: All email, stats to queue

---

## 16. SCALABILITY ANALYSIS

### 16.1 Horizontal Scaling

| Component | Scalable | Notes |
|-----------|----------|-------|
| Web Servers | ✅ Yes | Stateless, behind load balancer |
| Queue Workers | ✅ Yes | Multiple Horizon instances |
| Database | ⚠️ Limited | Requires read replicas |
| Redis | ✅ Yes | Redis Cluster support |

### 16.2 Bottlenecks

| Component | Bottleneck | Solution |
|-----------|------------|----------|
| Database | Write operations | Master-slave replication |
| Traffic Processing | High volume | Multiple queue workers |
| Settings Cache | Single source | Redis Cluster |

### 16.3 Multi-Tenant Considerations

Currently single-tenant. For multi-tenant:
- Add tenant_id to all tables
- Scope all queries by tenant
- Separate databases per tenant

---

## 17. PLUGIN DEVELOPMENT BLUEPRINT

### 17.1 Universal Plugin Structure

```
plugins/YourPlugin/
├── Plugin.php              # Main plugin class (required)
├── config.json             # Plugin configuration (required)
├── routes/
│   ├── api.php             # API routes
│   └── web.php             # Web routes
├── Controllers/
│   └── YourController.php
├── Commands/
│   └── YourCommand.php
├── database/
│   └── migrations/
│       └── 2024_01_01_create_tables.php
├── resources/
│   ├── views/
│   │   └── admin.blade.php
│   └── assets/
│       └── js/app.js
└── README.md
```

### 17.2 config.json Template

```json
{
    "name": "My Plugin",
    "code": "my_plugin",
    "type": "feature",
    "version": "1.0.0",
    "description": "Plugin description",
    "author": "Your Name",
    "require": {
        "xboard": ">=1.0.0"
    },
    "config": {
        "api_key": {
            "type": "string",
            "default": "",
            "label": "API Key",
            "description": "Your API key"
        },
        "enabled": {
            "type": "boolean",
            "default": true,
            "label": "Enable Plugin"
        },
        "options": {
            "type": "json",
            "default": [],
            "label": "Options"
        }
    }
}
```

### 17.3 Plugin.php Template

```php
<?php

namespace Plugin\MyPlugin;

use App\Services\Plugin\AbstractPlugin;

class Plugin extends AbstractPlugin
{
    public function boot(): void
    {
        // Register hooks
        $this->registerHooks();
        
        // Register filters
        $this->registerFilters();
    }
    
    protected function registerHooks(): void
    {
        $this->listen('order.create.after', function ($order) {
            // Handle order creation
        });
        
        $this->listen('user.register.after', function ($user) {
            // Handle user registration
        });
    }
    
    protected function registerFilters(): void
    {
        $this->filter('guest_comm_config', function ($config) {
            $config['my_plugin'] = [
                'enabled' => $this->getConfig('enabled', true),
            ];
            return $config;
        });
    }
    
    public function install(): void
    {
        // Run on plugin installation
    }
    
    public function cleanup(): void
    {
        // Run on plugin disable/uninstall
    }
    
    public function update(string $oldVersion, string $newVersion): void
    {
        // Handle version upgrades
    }
    
    public function schedule(\Illuminate\Console\Scheduling\Schedule $schedule): void
    {
        $schedule->call(function () {
            // Scheduled task
        })->hourly();
    }
}
```

### 17.4 Controller Template

```php
<?php

namespace Plugin\MyPlugin\Controllers;

use App\Http\Controllers\PluginController;
use Illuminate\Http\Request;

class MyController extends PluginController
{
    public function index(Request $request)
    {
        // Check plugin status
        if ($error = $this->beforePluginAction()) {
            return $error[1];
        }
        
        $data = [
            'setting' => $this->getConfig('api_key'),
        ];
        
        return $this->success($data);
    }
}
```

### 17.5 Routes Template (routes/api.php)

```php
<?php

use Illuminate\Support\Facades\Route;
use Plugin\MyPlugin\Controllers\MyController;

Route::group([
    'prefix' => 'api/v1/my-plugin',
    'middleware' => ['api']
], function () {
    Route::get('/', [MyController::class, 'index']);
    Route::post('/action', [MyController::class, 'action'])->middleware('user');
});
```

### 17.6 Migration Template

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('plugin_my_data', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('user_id');
            $table->string('data');
            $table->timestamps();
            
            $table->foreign('user_id')->references('id')->on('v2_user');
        });
    }
    
    public function down(): void
    {
        Schema::dropIfExists('plugin_my_data');
    }
};
```

### 17.7 Best Practices

1. **Namespace Convention**: `Plugin\{StudlyCase}\`
2. **Config Validation**: Always provide defaults
3. **Database Prefixing**: Use `plugin_` prefix for tables
4. **Error Handling**: Use try-catch, log errors
5. **Hook Priority**: Default 20, lower runs first
6. **Response Interception**: Use sparingly

---

## 18. FUTURE FEATURE MAP

### 18.1 Potential Features

| Feature | Backend Files | Frontend | DB Changes | Hooks Needed | Complexity |
|---------|--------------|----------|------------|--------------|------------|
| Multi-currency | OrderService, PaymentService | Order pages | v2_order.currency | order.create.before | Medium |
| Affiliate Tiers | UserService, CommissionLog | Admin panel | v2_user.affiliate_tier | order.commission | High |
| API Marketplace | New controller | New section | plugins marketplace table | plugin.* | High |
| Usage Analytics | StatController | Dashboard | v2_stat_detailed | None | Medium |
| Webhook System | New service | Admin config | v2_webhooks | All hooks | Medium |
| Custom Fields | UserService | User forms | v2_user_meta | user.* | Low |
| Reseller System | New module | Full panel | Multiple tables | Many | Very High |

### 18.2 Implementation Notes

For any feature, modify:
1. **Models**: Add new models/relationships
2. **Migrations**: Create database changes
3. **Services**: Add business logic
4. **Controllers**: Add API endpoints
5. **Routes**: Register new routes
6. **Frontend**: Update UI components

---

## 19. DEPLOYMENT REFERENCE

### 19.1 Required Packages (Ubuntu/Debian)

```bash
apt install nginx mysql-server redis-server supervisor
apt install php8.2-fpm php8.2-mysql php8.2-redis php8.2-curl
apt install php8.2-mbstring php8.2-xml php8.2-zip php8.2-bcmath
apt install php8.2-sodium php8.2-fileinfo php8.2-swoole
```

### 19.2 php.ini Settings

```ini
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
date.timezone = Asia/Shanghai
```

### 19.3 nginx Configuration

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/xboard/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 19.4 Supervisor Configuration

```ini
[program:xboard-octane]
command=php /var/www/xboard/artisan octane:start --host=127.0.0.1 --port=8000
directory=/var/www/xboard
autostart=true
autorestart=true
user=www-data

[program:xboard-horizon]
command=php /var/www/xboard/artisan horizon
directory=/var/www/xboard
autostart=true
autorestart=true
user=www-data
```

### 19.5 Crontab

```cron
* * * * * cd /var/www/xboard && php artisan schedule:run >> /dev/null 2>&1
```

### 19.6 Deployment Commands

```bash
# Install dependencies
composer install --optimize-autoloader --no-dev

# Generate key
php artisan key:generate

# Run migrations
php artisan migrate --force

# Clear caches
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Restart services
supervisorctl restart all
```

---

## 20. APPENDIX

### 20.1 Environment Variables Reference

| Variable | Default | Description |
|----------|---------|-------------|
| APP_NAME | Laravel | Application name |
| APP_ENV | production | Environment |
| APP_DEBUG | false | Debug mode |
| APP_KEY | - | Encryption key |
| APP_URL | - | Application URL |
| DB_CONNECTION | mysql | Database driver |
| DB_HOST | 127.0.0.1 | Database host |
| DB_PORT | 3306 | Database port |
| DB_DATABASE | xboard | Database name |
| DB_USERNAME | - | Database user |
| DB_PASSWORD | - | Database password |
| REDIS_HOST | 127.0.0.1 | Redis host |
| REDIS_PORT | 6379 | Redis port |
| MAIL_MAILER | smtp | Mail driver |
| QUEUE_CONNECTION | redis | Queue driver |

### 20.2 License

XBoard is released under the MIT License.

### 20.3 Third-Party Libraries

| Library | Version | License | Purpose |
|---------|---------|---------|---------|
| Laravel | 12.x | MIT | Framework |
| Sanctum | 4.x | MIT | API Auth |
| Horizon | 5.x | MIT | Queue UI |
| Guzzle | 7.x | MIT | HTTP Client |
| Stripe PHP | 7.x | MIT | Payments |

### 20.4 Known Limitations

1. Single database architecture (no sharding)
2. Limited multi-tenant support
3. Admin panel not customizable without rebuild
4. Some legacy MD5 password support

### 20.5 Support Resources

- GitHub Repository: https://github.com/cedar2025/Xboard
- Documentation: /docs/ directory
- Plugin Guide: /docs/en/development/plugin-development-guide.md

---

## DOCUMENT END

**Analysis Completed**: 2025-11-25
**Total Sections**: 20
**Analyzer**: GitHub Copilot Pro Agent

This document serves as the complete knowledge base for XBoard plugin development, system modernization, and feature extension. All code paths have been traced, all hooks documented, and all extension points identified.

For plugin development, focus on:
1. Section 8: Hooks & Extension Points
2. Section 17: Plugin Development Blueprint
3. Section 11: Payment Gateway Framework

For system maintenance, focus on:
1. Section 9: Database Analysis
2. Section 14: Security Analysis
3. Section 19: Deployment Reference
