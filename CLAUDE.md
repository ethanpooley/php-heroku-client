# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A PHP client library for the Heroku Platform API. Uses PSR-18 (HTTP Client) and PSR-7 (Request/Response) standards. Supports PHP 7.3+ and 8.x.

## Commands

```bash
composer install              # Install dependencies
composer test                 # Run tests with coverage (sets XDEBUG_MODE=coverage automatically)
vendor/bin/phpunit --filter testMethodName  # Run a single test
```

## Architecture

Single-class library: `HerokuClient\Client` (`src/Client.php`) wraps a PSR-18 HTTP client to interact with the Heroku API.

**Request flow:** Public HTTP methods (`get`, `post`, `put`, `patch`, `delete`, `head`) → `execute()` → `buildRequest()` → PSR-18 `sendRequest()` → `processResponse()` → returns `stdClass`.

**Configuration:** Constructor accepts an associative array that sets protected properties directly (apiKey, baseUrl, curlOptions, httpClient). Falls back to `HEROKU_API_KEY` env var for the API key.

**HTTP client:** Defaults to `php-http/curl-client` with GuzzleHttp PSR-7 factories. A custom PSR-18 `HttpClient` can be injected via the `httpClient` config key.

**Exceptions** (all in `src/Exception/`):
- `BadHttpStatusException` (`RuntimeException`) — HTTP 4xx/5xx responses
- `JsonDecodingException` / `JsonEncodingException` (`RuntimeException`) — JSON errors
- `MissingApiKeyException` (`LogicException`) — no API key provided

**State inspection:** `getLastHttpRequest()` / `getLastHttpResponse()` expose the most recent PSR-7 objects. The stored request has the Authorization header redacted.

## Coding Standards

- PSR-2 coding standard
- All properties are `protected` (no public properties)
- Tests use `php-http/mock-client` for HTTP isolation
- 100% test coverage target
