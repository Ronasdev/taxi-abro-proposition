# Document Technique - TAXI ABRO
## Stack Technologique Laravel + JavaScript

---

## 🏗️ Architecture Technique

### Stack Principal
- **Backend** : Laravel 10.x (PHP 8.2+)
- **Frontend** : Blade + Alpine.js + Livewire
- **CSS Framework** : TailwindCSS 3.x
- **Base de données** : PostgreSQL 15+ (recommandé) ou MySQL 8.0+
- **Cache** : Redis 7.x
- **Queue** : Redis + Horizon
- **Storage** : AWS S3 ou équivalent

### Architecture MVC Étendue
```
app/
├── Http/
│   ├── Controllers/
│   │   ├── Web/           # Controllers web classiques
│   │   ├── Api/           # API REST
│   │   └── Admin/         # Interface admin
│   ├── Middleware/
│   ├── Requests/          # Form requests avec validation
│   └── Resources/         # API Resources
├── Models/
├── Services/              # Logique métier
├── Repositories/          # Couche d'accès aux données
├── Jobs/                  # Tâches asynchrones
├── Events/                # Événements système
├── Listeners/             # Écouteurs d'événements
├── Notifications/         # Notifications email/SMS
└── Policies/              # Autorisations
```

## 🗄️ Modèle de Base de Données

### Tables Principales

#### Users (Utilisateurs multi-rôles)
```sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE,
    email_verified_at TIMESTAMP,
    phone_verified_at TIMESTAMP,
    password VARCHAR(255),
    avatar_path VARCHAR(500),
    date_of_birth DATE,
    preferred_language VARCHAR(5) DEFAULT 'fr',
    timezone VARCHAR(50) DEFAULT 'America/Toronto',
    two_factor_secret TEXT,
    two_factor_recovery_codes TEXT,
    remember_token VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);
```

#### Roles & Permissions (Spatie)
```sql
-- Utilisation du package spatie/laravel-permission
-- Tables générées automatiquement :
-- roles, permissions, model_has_roles, model_has_permissions, role_has_permissions
```

#### Bookings (Réservations)
```sql
CREATE TABLE bookings (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    user_id BIGINT REFERENCES users(id),
    driver_id BIGINT REFERENCES users(id),
    vehicle_id BIGINT REFERENCES vehicles(id),
    
    -- Informations trajet
    pickup_address TEXT NOT NULL,
    pickup_latitude DECIMAL(10, 8),
    pickup_longitude DECIMAL(11, 8),
    destination_address TEXT NOT NULL,
    destination_latitude DECIMAL(10, 8),
    destination_longitude DECIMAL(11, 8),
    
    -- Timing
    scheduled_at TIMESTAMP NOT NULL,
    pickup_time TIMESTAMP,
    dropoff_time TIMESTAMP,
    
    -- Détails
    passenger_count INTEGER DEFAULT 1,
    service_type VARCHAR(50) NOT NULL, -- 'standard', 'vip', 'airport', 'cross_border'
    special_instructions TEXT,
    flight_ticket_path VARCHAR(500), -- Upload billet
    
    -- Pricing
    base_price DECIMAL(10, 2),
    taxes DECIMAL(10, 2),
    total_price DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'CAD',
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending', -- pending, confirmed, in_progress, completed, cancelled
    payment_status VARCHAR(20) DEFAULT 'pending', -- pending, paid, failed, refunded
    
    -- Metadata
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);
```

#### Vehicles (Véhicules)
```sql
CREATE TABLE vehicles (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    license_plate VARCHAR(20) UNIQUE NOT NULL,
    make VARCHAR(50) NOT NULL,
    model VARCHAR(50) NOT NULL,
    year INTEGER NOT NULL,
    color VARCHAR(30),
    capacity INTEGER DEFAULT 4,
    vehicle_type VARCHAR(30), -- 'sedan', 'suv', 'van', 'luxury'
    
    -- Documentation
    registration_expires_at DATE,
    insurance_expires_at DATE,
    inspection_expires_at DATE,
    
    -- Maintenance
    current_mileage INTEGER DEFAULT 0,
    last_maintenance_at DATE,
    next_maintenance_due INTEGER, -- mileage
    
    -- Status
    status VARCHAR(20) DEFAULT 'active', -- active, maintenance, retired
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Payments (Paiements)
```sql
CREATE TABLE payments (
    id BIGSERIAL PRIMARY KEY,
    uuid UUID UNIQUE DEFAULT gen_random_uuid(),
    booking_id BIGINT REFERENCES bookings(id),
    user_id BIGINT REFERENCES users(id),
    
    -- Payment details
    amount DECIMAL(10, 2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'CAD',
    payment_method VARCHAR(30), -- 'stripe', 'paypal', 'cash'
    
    -- External references
    stripe_payment_intent_id VARCHAR(255),
    paypal_order_id VARCHAR(255),
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending', -- pending, completed, failed, refunded
    processed_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Index et Optimisations
```sql
-- Index pour les recherches fréquentes
CREATE INDEX idx_bookings_user_id ON bookings(user_id);
CREATE INDEX idx_bookings_driver_id ON bookings(driver_id);
CREATE INDEX idx_bookings_scheduled_at ON bookings(scheduled_at);
CREATE INDEX idx_bookings_status ON bookings(status);
CREATE INDEX idx_bookings_location ON bookings USING GIST (
    ll_to_earth(pickup_latitude, pickup_longitude)
);

-- Index composites
CREATE INDEX idx_bookings_user_status ON bookings(user_id, status);
CREATE INDEX idx_bookings_date_status ON bookings(DATE(scheduled_at), status);
```

## 🔧 Configuration Laravel

### Packages Recommandés

#### Authentification & Autorisation
```bash
composer require laravel/sanctum
composer require spatie/laravel-permission
composer require pragmarx/google2fa-laravel
```

#### Interface Utilisateur
```bash
composer require livewire/livewire
composer require filament/filament  # Pour l'admin (optionnel)
```

#### Paiements
```bash
composer require stripe/stripe-php
composer require srmklive/paypal
```

#### Géolocalisation & Maps
```bash
composer require geocoder-php/google-maps-provider
composer require geocoder-php/geocoder
```

#### Communication
```bash
composer require laravel/vonage-notification-channel  # SMS
composer require pusher/pusher-php-server            # WebSockets
```

#### Utilitaires
```bash
composer require spatie/laravel-medialibrary        # Gestion fichiers
composer require spatie/laravel-activitylog         # Logs d'activité
composer require barryvdh/laravel-dompdf           # PDF
composer require maatwebsite/excel                  # Excel export
```

### Configuration Environnement (.env)
```env
# Application
APP_NAME="TAXI ABRO"
APP_ENV=production
APP_KEY=base64:...
APP_DEBUG=false
APP_URL=https://taxiabro.ca

# Database
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=taxiabro
DB_USERNAME=taxiabro_user
DB_PASSWORD=secure_password

# Redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

# Mail
MAIL_MAILER=smtp
MAIL_HOST=smtp.sendgrid.net
MAIL_PORT=587
MAIL_USERNAME=apikey
MAIL_PASSWORD=your_sendgrid_api_key
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@taxiabro.ca
MAIL_FROM_NAME="TAXI ABRO"

# SMS (Vonage/Nexmo)
VONAGE_KEY=your_vonage_key
VONAGE_SECRET=your_vonage_secret
VONAGE_SMS_FROM="TAXI ABRO"

# Payments
STRIPE_KEY=pk_live_...
STRIPE_SECRET=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

PAYPAL_MODE=live
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret

# Maps & Geocoding
GOOGLE_MAPS_API_KEY=your_google_maps_key

# File Storage
FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_DEFAULT_REGION=ca-central-1
AWS_BUCKET=taxiabro-files

# Pusher (WebSockets)
PUSHER_APP_ID=your_pusher_app_id
PUSHER_APP_KEY=your_pusher_key
PUSHER_APP_SECRET=your_pusher_secret
PUSHER_APP_CLUSTER=us2
```

## 🎨 Frontend avec TailwindCSS

### Configuration TailwindCSS
```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
    "./resources/**/*.vue",
    "./app/Http/Livewire/**/*.php",
  ],
  theme: {
    extend: {
      colors: {
        'taxi-blue': {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          900: '#1e3a8a',
        },
        'taxi-yellow': {
          400: '#fbbf24',
          500: '#f59e0b',
          600: '#d97706',
        }
      },
      fontFamily: {
        'sans': ['Inter', 'system-ui', 'sans-serif'],
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
  ],
}
```

### Structure CSS
```scss
// resources/css/app.css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  html {
    font-family: Inter, system-ui, sans-serif;
  }
}

@layer components {
  .btn-primary {
    @apply bg-taxi-blue-600 hover:bg-taxi-blue-700 text-white font-medium py-2 px-4 rounded-lg transition-colors duration-200;
  }
  
  .btn-secondary {
    @apply bg-gray-200 hover:bg-gray-300 text-gray-800 font-medium py-2 px-4 rounded-lg transition-colors duration-200;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
  
  .form-input {
    @apply block w-full rounded-md border-gray-300 shadow-sm focus:border-taxi-blue-500 focus:ring-taxi-blue-500;
  }
}

@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
}
```

### Alpine.js Integration
```javascript
// resources/js/app.js
import Alpine from 'alpinejs'
import mask from '@alpinejs/mask'
import intersect from '@alpinejs/intersect'

Alpine.plugin(mask)
Alpine.plugin(intersect)

// Composants Alpine globaux
Alpine.data('bookingForm', () => ({
    step: 1,
    pickup: '',
    destination: '',
    date: '',
    time: '',
    passengers: 1,
    
    nextStep() {
        if (this.validateCurrentStep()) {
            this.step++
        }
    },
    
    prevStep() {
        this.step--
    },
    
    validateCurrentStep() {
        // Logique de validation
        return true
    }
}))

Alpine.data('mapComponent', () => ({
    map: null,
    markers: [],
    
    init() {
        this.initMap()
    },
    
    initMap() {
        this.map = new google.maps.Map(this.$refs.map, {
            center: { lat: 45.7597, lng: -73.4555 }, // L'Assomption
            zoom: 10
        })
    }
}))

Alpine.start()
```

## 🔐 Authentification & Sécurité

### Middleware Personnalisés
```php
// app/Http/Middleware/RoleMiddleware.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class RoleMiddleware
{
    public function handle(Request $request, Closure $next, ...$roles)
    {
        if (!auth()->check()) {
            return redirect()->route('login');
        }

        $user = auth()->user();
        
        if (!$user->hasAnyRole($roles)) {
            abort(403, 'Accès non autorisé');
        }

        return $next($request);
    }
}
```

### Policies
```php
// app/Policies/BookingPolicy.php
<?php

namespace App\Policies;

use App\Models\Booking;
use App\Models\User;

class BookingPolicy
{
    public function view(User $user, Booking $booking): bool
    {
        return $user->id === $booking->user_id 
            || $user->id === $booking->driver_id
            || $user->hasRole(['admin', 'dispatcher']);
    }

    public function update(User $user, Booking $booking): bool
    {
        return $user->hasRole(['admin', 'dispatcher'])
            || ($booking->status === 'pending' && $user->id === $booking->user_id);
    }

    public function cancel(User $user, Booking $booking): bool
    {
        return $user->id === $booking->user_id 
            || $user->hasRole(['admin', 'dispatcher']);
    }
}
```

## 📱 API REST

### Structure API
```php
// routes/api.php
Route::prefix('v1')->group(function () {
    // Public routes
    Route::post('/auth/login', [AuthController::class, 'login']);
    Route::post('/auth/register', [AuthController::class, 'register']);
    
    // Protected routes
    Route::middleware('auth:sanctum')->group(function () {
        Route::apiResource('bookings', BookingController::class);
        Route::apiResource('vehicles', VehicleController::class);
        
        // Driver specific routes
        Route::middleware('role:driver')->prefix('driver')->group(function () {
            Route::get('/bookings', [DriverController::class, 'bookings']);
            Route::post('/bookings/{booking}/accept', [DriverController::class, 'acceptBooking']);
            Route::post('/bookings/{booking}/start', [DriverController::class, 'startTrip']);
            Route::post('/bookings/{booking}/complete', [DriverController::class, 'completeTrip']);
        });
        
        // Admin routes
        Route::middleware('role:admin')->prefix('admin')->group(function () {
            Route::get('/dashboard', [AdminController::class, 'dashboard']);
            Route::get('/analytics', [AdminController::class, 'analytics']);
        });
    });
});
```

### API Resources
```php
// app/Http/Resources/BookingResource.php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class BookingResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->uuid,
            'pickup_address' => $this->pickup_address,
            'destination_address' => $this->destination_address,
            'scheduled_at' => $this->scheduled_at->toISOString(),
            'passenger_count' => $this->passenger_count,
            'service_type' => $this->service_type,
            'total_price' => $this->total_price,
            'status' => $this->status,
            'driver' => new UserResource($this->whenLoaded('driver')),
            'vehicle' => new VehicleResource($this->whenLoaded('vehicle')),
            'created_at' => $this->created_at->toISOString(),
        ];
    }
}
```

## 🔄 Jobs & Queues

### Jobs Asynchrones
```php
// app/Jobs/SendBookingConfirmation.php
<?php

namespace App\Jobs;

use App\Models\Booking;
use App\Notifications\BookingConfirmed;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class SendBookingConfirmation implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(
        public Booking $booking
    ) {}

    public function handle(): void
    {
        $this->booking->user->notify(new BookingConfirmed($this->booking));
        
        // SMS notification
        $this->booking->user->notify(
            (new BookingConfirmed($this->booking))->via(['vonage'])
        );
    }
}
```

### Configuration Horizon
```php
// config/horizon.php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'connection' => 'redis',
            'queue' => ['default', 'emails', 'notifications'],
            'balance' => 'auto',
            'processes' => 10,
            'tries' => 3,
            'timeout' => 60,
        ],
    ],
],
```

## 📊 Analytics & Reporting

### Service Analytics
```php
// app/Services/AnalyticsService.php
<?php

namespace App\Services;

use App\Models\Booking;
use Carbon\Carbon;
use Illuminate\Support\Facades\DB;

class AnalyticsService
{
    public function getDashboardMetrics(Carbon $startDate, Carbon $endDate): array
    {
        return [
            'total_bookings' => $this->getTotalBookings($startDate, $endDate),
            'total_revenue' => $this->getTotalRevenue($startDate, $endDate),
            'average_trip_value' => $this->getAverageTripValue($startDate, $endDate),
            'completion_rate' => $this->getCompletionRate($startDate, $endDate),
            'popular_routes' => $this->getPopularRoutes($startDate, $endDate),
            'hourly_distribution' => $this->getHourlyDistribution($startDate, $endDate),
        ];
    }

    private function getTotalBookings(Carbon $startDate, Carbon $endDate): int
    {
        return Booking::whereBetween('created_at', [$startDate, $endDate])
            ->count();
    }

    private function getTotalRevenue(Carbon $startDate, Carbon $endDate): float
    {
        return Booking::whereBetween('created_at', [$startDate, $endDate])
            ->where('payment_status', 'paid')
            ->sum('total_price');
    }

    private function getPopularRoutes(Carbon $startDate, Carbon $endDate): array
    {
        return Booking::select(
                'pickup_address',
                'destination_address',
                DB::raw('COUNT(*) as trip_count')
            )
            ->whereBetween('created_at', [$startDate, $endDate])
            ->groupBy('pickup_address', 'destination_address')
            ->orderBy('trip_count', 'desc')
            ->limit(10)
            ->get()
            ->toArray();
    }
}
```

## 🚀 Déploiement & DevOps

### Docker Configuration
```dockerfile
# Dockerfile
FROM php:8.2-fpm

# Install dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libpq-dev

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_pgsql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www

# Copy application
COPY . .

# Install dependencies
RUN composer install --optimize-autoloader --no-dev

# Set permissions
RUN chown -R www-data:www-data /var/www
RUN chmod -R 755 /var/www/storage

EXPOSE 9000
CMD ["php-fpm"]
```

### Docker Compose
```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    container_name: taxiabro-app
    restart: unless-stopped
    working_dir: /var/www
    volumes:
      - ./:/var/www
    networks:
      - taxiabro

  nginx:
    image: nginx:alpine
    container_name: taxiabro-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./:/var/www
      - ./docker/nginx:/etc/nginx/conf.d
    networks:
      - taxiabro

  postgres:
    image: postgres:15
    container_name: taxiabro-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: taxiabro
      POSTGRES_USER: taxiabro_user
      POSTGRES_PASSWORD: secure_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - taxiabro

  redis:
    image: redis:7-alpine
    container_name: taxiabro-redis
    restart: unless-stopped
    networks:
      - taxiabro

volumes:
  postgres_data:

networks:
  taxiabro:
    driver: bridge
```

### Scripts de Déploiement
```bash
#!/bin/bash
# deploy.sh

echo "🚀 Déploiement TAXI ABRO"

# Pull latest code
git pull origin main

# Install/update dependencies
composer install --optimize-autoloader --no-dev

# Run migrations
php artisan migrate --force

# Clear caches
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Restart services
php artisan queue:restart
php artisan horizon:terminate

# Build assets
npm ci
npm run build

echo "✅ Déploiement terminé"
```

## 🧪 Tests

### Configuration PHPUnit
```php
// tests/Feature/BookingTest.php
<?php

namespace Tests\Feature;

use App\Models\User;
use App\Models\Booking;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class BookingTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_create_booking(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->post('/bookings', [
            'pickup_address' => '123 Main St, Montreal',
            'destination_address' => '456 Oak Ave, Toronto',
            'scheduled_at' => now()->addDay(),
            'passenger_count' => 2,
            'service_type' => 'standard',
        ]);

        $response->assertStatus(201);
        $this->assertDatabaseHas('bookings', [
            'user_id' => $user->id,
            'pickup_address' => '123 Main St, Montreal',
        ]);
    }

    public function test_admin_can_view_all_bookings(): void
    {
        $admin = User::factory()->create();
        $admin->assignRole('admin');

        Booking::factory()->count(5)->create();

        $response = $this->actingAs($admin)->get('/admin/bookings');

        $response->assertStatus(200);
        $response->assertJsonCount(5, 'data');
    }
}
```

## 📈 Monitoring & Performance

### Configuration Telescope (Développement)
```php
// config/telescope.php
'watchers' => [
    Watchers\CacheWatcher::class => env('TELESCOPE_CACHE_WATCHER', true),
    Watchers\CommandWatcher::class => env('TELESCOPE_COMMAND_WATCHER', true),
    Watchers\DumpWatcher::class => env('TELESCOPE_DUMP_WATCHER', true),
    Watchers\EventWatcher::class => env('TELESCOPE_EVENT_WATCHER', true),
    Watchers\ExceptionWatcher::class => env('TELESCOPE_EXCEPTION_WATCHER', true),
    Watchers\JobWatcher::class => env('TELESCOPE_JOB_WATCHER', true),
    Watchers\LogWatcher::class => env('TELESCOPE_LOG_WATCHER', true),
    Watchers\MailWatcher::class => env('TELESCOPE_MAIL_WATCHER', true),
    Watchers\ModelWatcher::class => [
        'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
        'hydrations' => true,
    ],
    Watchers\NotificationWatcher::class => env('TELESCOPE_NOTIFICATION_WATCHER', true),
    Watchers\QueryWatcher::class => [
        'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
        'slow' => 100,
    ],
    Watchers\RedisWatcher::class => env('TELESCOPE_REDIS_WATCHER', true),
    Watchers\RequestWatcher::class => [
        'enabled' => env('TELESCOPE_REQUEST_WATCHER', true),
        'size_limit' => env('TELESCOPE_RESPONSE_SIZE_LIMIT', 64),
    ],
    Watchers\ScheduleWatcher::class => env('TELESCOPE_SCHEDULE_WATCHER', true),
    Watchers\ViewWatcher::class => env('TELESCOPE_VIEW_WATCHER', true),
],
```

---

*Ce document technique fournit une base solide pour le développement de la plateforme TAXI ABRO avec Laravel. Il peut être adapté selon les besoins spécifiques de chaque version du cahier des charges.*
