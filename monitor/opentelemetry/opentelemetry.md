The most effective and production-ready approach to implementing OpenTelemetry (OTel) in a Laravel application is using PHP Auto-Instrumentation.

Instead of forcing you to write manual tracing code around every query and controller, auto-instrumentation hooks directly into the PHP runtime engine. It automatically intercepts HTTP requests, Eloquent database transactions, Redis commands, and queue jobs with minimal performance overhead.  
OneUptime
+ 1

Step 1: Install the OpenTelemetry PHP Extension
Because PHP is a shared-nothing architecture, the auto-instrumentation framework requires a native C extension to inject hooks into the runtime lifecycle.

Install the extension via PECL:

Bash
sudo apt-get install gcc make autoconf # Required build tools
pecl install opentelemetry
Enable it in your php.ini:
Add the following line to your active php.ini file. Make sure you add it to the configurations for both your web server (e.g., PHP-FPM) and your CLI (for Artisan commands and workers).  
SigNoz

Ini, TOML
extension=opentelemetry.so
Verify the installation:

Bash
php -m | grep opentelemetry
Step 2: Install Composer Dependencies
Run the following command in your Laravel project root to pull in the official OpenTelemetry SDK, the OTLP exporter, and the specialized Laravel instrumentation hook library:

Bash
composer require open-telemetry/sdk \
                 open-telemetry/exporter-otlp \
                 open-telemetry/opentelemetry-auto-laravel
How it works: The opentelemetry-auto-laravel package automatically bootstraps itself via Composer's autoloader. It listens to core Laravel framework events and generates spans effortlessly.  
OneUptime
+ 1

Step 3: Configure via Environment Variables (.env)
OpenTelemetry is designed to be configured entirely through standard environment variables. This keeps your configuration decoupled from your codebase. Add the following to your .env file:  
OneUptime

Code snippet
# Enable OTel auto-loading hooks
OTEL_PHP_AUTOLOAD_ENABLED=true

# App Identification
OTEL_SERVICE_NAME=my-laravel-app
OTEL_RESOURCE_ATTRIBUTES=deployment.environment=production

# Exporter Endpoint (Point this to your OTel Collector, SigNoz, Jaeger, or Grafana Agent)
# If using HTTP/Protobuf (Recommended default):
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf

# Alternate gRPC setup if needed:
# OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
# OTEL_EXPORTER_OTLP_PROTOCOL=grpc
Step 4: Performance Optimizations for Production
Collecting traces can create overhead if left unmanaged. To maintain a highly performant application, optimize your setup with these best practices:

Implement Trace Sampling: In high-traffic production environments, sending 100% of your traces is unnecessary and expensive. Set up ratio-based sampling to only capture a fraction of successful traffic (e.g., 10%):

Code snippet
OTEL_TRACES_SAMPLER=parentbased_traceidratio
OTEL_TRACES_SAMPLER_ARG=0.1
Handle Long-Running Processes (Octane, Horizon, & Workers):
If you are running Laravel Octane or daemonized queues (php artisan queue:work), state can bleed between processes. The opentelemetry-auto-laravel package natively targets this by clearing out and ending the span context at the end of every request or job lifecycle, avoiding memory leaks.

Step 5: (Optional) Adding Custom Spans for Business Logic
While auto-instrumentation will map your HTTP layers and Eloquent queries, you might want deep performance metrics on a specific heavy calculation or third-party API integration. You can create manual spans like this:

PHP
use OpenTelemetry\API\Globals;

// Get the globally configured tracer
$tracer = Globals::tracerProvider()->getTracer('laravel-custom');

// Build and start a new span
$span = $tracer->spanBuilder('process_heavy_financial_report')->startSpan();

try {
    // Your heavy calculation or external service call here...
    
} catch (\Exception $e) {
    $span->recordException($e);
    throw $e;
} finally {
    // Always close the span to record duration accurately
    $span->end();
}
Once this is running, your application will stream traces to your observability dashboard, showing you exact flame graphs outlining where database bottlenecks, slow HTTP requests, or faulty queue handlers live.  
base14

Which observability backend or APM tool (such as Jaeger, SigNoz, Datadog, or Grafana Cloud) are you planning to send your telemetry data to, so we can ensure your network and header configurations match perfectly?
