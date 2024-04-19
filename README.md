# PHP LOGGING

## POINT UTAMA

### 1. Logging Channel

1. Default channel milik `Laravel`.

-   Single, Mengirim data _log_ ke single file.

    ```PHP
    'single' => [
            'driver' => 'single',
            'path' => storage_path('logs/laravel.log'),
            'level' => env('LOG_LEVEL', 'debug'),
        ],
    ```

-   Daily, mengirim data _log_ ke single file, namun setiap hari akan di _rotate_ filenya.

    ```PHP
     'daily' => [
            'driver' => 'daily',
            'path' => storage_path('logs/laravel.log'),
            'level' => env('LOG_LEVEL', 'debug'),
            'days' => 14,
        ],
    ```

-   Slack, mengirim data _log_ ke `slack chat`.

    ```PHP
    'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => env('LOG_LEVEL', 'critical'),
        ],
    ```

-   Sylog, mengirim data _log_ ke `sylog`.

    ```PHP
     'syslog' => [
            'driver' => 'syslog',
            'level' => env('LOG_LEVEL', 'debug'),
        ],
    ```

-   Null, tidak mengirim data _log_ kemanapun.

    ```PHP
    'null' => [
            'driver' => 'monolog',
            'handler' => NullHandler::class,
        ],
    ```

-   Stack, mengirim data _log_ ke beberapa channel sekaligus, default nya hanya mengirim ke channel.

    ```PHP
    'stack' => [
            'driver' => 'stack',
            'channels' => ['single'],
            'ignore_exceptions' => false,
        ],
    ```

-   Secara default, `laravel` akan menggunakan channel `slack`.

---

### 2. Log Facade

-   Kita bisa menggunakan `Log facade` untuk melakukan logging di `laravel`.

-   Kode `log facade`

    ```php
    class LoggingTest extends TestCase
    {
        public function testLogging()
        {
            Log::info("Hello Info");
            Log::warning("Hello Warning");
            Log::error("Hello Error");
            Log::critical("Hello Critical");

            self::assertTrue(true);
        }
    }
    ```

-   Hasil dari log bisa di lihat di `storage/logs/laravel.log`.

---

### 3. Multiple Log Channel

-   Untuk membuat _log_ ke `slack`, kita perlu membuat URL terlebih dahulu. Di directory `config/logging.php`

    ```PHP
     'stack' => [
            'driver' => 'stack',
            'channels' => ['single', 'slack', 'stderr'], //menambahkan channel
            'ignore_exceptions' => false,
        ],

         'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => env('LOG_LEVEL_SLACK', 'error'), // menambahkan level error
        ],
    ```

    ```PHP
    LOG_CHANNEL=stack
    LOG_DEPRECATIONS_CHANNEL=null
    LOG_LEVEL=debug
    LOG_LEVEL_SLACK=error
    LOG_SLACK_WEBHOOK_URL=https://hooks.slack.com/services/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
    ```

---

### 4. Context

-   `Log facade` memiliki parameter kedua setelah message yang bisa diisi dengan data `context`. Mirip seperti PHP logging.

    ```PHP
     public function testContext()
    {
        Log::info("Hello Info", ["user" => "agung"]);
        Log::info("Hello Info", ["user" => "agung"]);
        Log::info("Hello Info", ["user" => "agung"]);

        self::assertTrue(true);
    }
    ```

-   Atau kita bisa menggunakan _function_ `withContext()`, untuk secara otomatis ke kode selanjutnya.

-   Kode `withContext`

    ```PHP

    public function testWithContext()
    {
        Log::withContext(["user" => "vior"]);

        Log::info("Hello Info");
        Log::info("Hello Info");
        Log::info("Hello Info");

        self::assertTrue(true);
    }
    ```

---

### 5. Selected Channel

-   Kode selected channel

    ```PHP
    public function testChannel()
    {
        $slackLogger = Log::channel("slack");
        $slackLogger->error("Hello Slack"); // send to slack channel

        Log::info("Hello Laravel"); // send to default channel
        self::assertTrue(true);
    }
    ```

---

### 6. Handler

-   Kode Handler

    ```PHP
      public function testFileHandler()
    {
        $fileLogger = Log::channel("file");
        $fileLogger->info("Hello File Handler");
        $fileLogger->warning("Hello File Handler");
        $fileLogger->error("Hello File Handler");
        $fileLogger->critical("Hello File Handler");

        self::assertTrue(true);
    }
    ```

---

### 7. Formatter

-   Di `laravel` kita bisa menggunakan `config formatter` dengan berisi _class_ `formatter`.

-   Kode konfigurasi

    ```PHP
    'file' => [
            'driver' => 'monolog',
            'level' => env('LOG_LEVEL', 'debug'),
            'handler' => StreamHandler::class,
            'formatter' => \Monolog\Formatter\JsonFormatter::class,
            'with' => [
                'stream' => storage_path("logs/application.log"),
            ],
        ],
    ```

---

## PERTANYAAN & CATATAN TAMBAHAN

-  Logging dalam Laravel adalah aspek penting dalam pengembangan aplikasi yang memungkinkan Anda untuk merekam informasi yang berharga seperti pesan kesalahan, aktivitas pengguna, dan peristiwa penting lainnya. Dengan menggunakan fasilitas logging yang disediakan oleh Laravel, seperti Monolog, Anda dapat dengan mudah mengonfigurasi tingkat log, channel, dan format pesan sesuai kebutuhan aplikasi Anda, sehingga memudahkan pemantauan, pemecahan masalah, dan meningkatkan keamanan serta kinerja aplikasi secara keseluruhan.

---

### KESIMPULAN

-
