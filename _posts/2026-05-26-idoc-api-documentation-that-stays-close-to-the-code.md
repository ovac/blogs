---
layout: post
current: post
cover: https://opengraph.githubassets.com/ovac-idoc-20260526/ovac/idoc
navigation: True
title: "iDoc: API documentation that stays close to the code"
date: 2026-05-26 03:00:00
tags: [tech, laravel]
class: post-template
subclass: 'post tag-tech tag-laravel'
author: ovac4u
---

Most teams do not set out to have bad API documentation.

It happens slowly.

A route changes. A request parameter is renamed. A JSON response gets one more nested field. The frontend team learns it from Slack. The mobile team learns it from a failed request. The external partner learns it from production. The documentation, if it exists, is now a memory of how the API used to work.

That is the problem I wanted `ovac/idoc` to solve.

[iDoc](https://github.com/ovac/idoc) is a Laravel package for generating interactive API documentation and an OpenAPI 3.0 spec from an existing Laravel application. The idea is simple: your application already knows a lot about your API. Your routes, controllers, PHPDoc, request metadata, and conventions should be enough to produce a useful API reference without asking a developer to maintain a second manual copy of the same truth.

The package started from a practical place. I wanted Laravel API docs that were not just technically generated, but actually comfortable to read and use. A generated Markdown page is useful. A generated OpenAPI file is useful. But developers live inside workflows. They need to read an endpoint, understand the payload, try a request, inspect the response, copy an example, and move on.

iDoc is my attempt to make that loop tight.

## The documentation is a product surface

For a long time, I treated API documentation as a byproduct of engineering. Build the API first, then write something down at the end.

That is backwards for serious systems.

The docs are part of the product surface. They are where an integrator forms their first model of your system. They are where a frontend developer decides whether the backend is predictable. They are where a future version of you tries to remember why an endpoint behaves a certain way.

That is why iDoc does more than dump route names.

The current public version of iDoc generates an OpenAPI document and renders it with a hybrid interface:

- ReDoc for fast, readable API reference navigation.
- Swagger UI in a side panel for "Try it" requests.
- A theme toggle that keeps the reader and tester in the same visual mode.
- Extra request headers for realistic authenticated testing.
- OpenAPI 3.0 output at a predictable public path.

That combination matters. Reading and testing should not be separate rituals. A good API reference should let you move from "what does this endpoint do?" to "what happens if I call it?" without leaving the page.

## Generated, but not careless

iDoc is generated from a Laravel codebase, but it is not meant to encourage careless documentation.

Generation gives you the skeleton. You still need discipline in how you write controllers, comments, request validation, route groups, and examples. The package gives the application a way to publish the truth it already carries. It does not remove the need to make that truth clean.

The core command is deliberately boring:

```bash
php artisan idoc:generate
```

Behind that command, iDoc inspects the routes selected by configuration, skips routes marked with `@hideFromAPIDocumentation`, groups the parsed output, and writes an OpenAPI JSON file. That is the correct kind of boring. Documentation generation should feel like running tests or compiling assets. It should not feel like a special event.

The config also lets you decide where the docs live:

```php
'path' => 'idoc',
'output' => '',
'tryit' => [
    'enabled' => env('IDOC_TRYIT_ENABLED', true),
],
```

That means a team can start with `/idoc`, publish `openapi.json`, and decide how much interactivity belongs in each environment.

## The AI assistant belongs in the docs, not beside them

One of the newer pieces I like in iDoc is the optional chat assistant.

The temptation with AI tools is to bolt them onto everything as decoration. That is not useful. In API documentation, though, an assistant has a real job: help a developer ask questions against the generated spec and the extra context the application owner chooses to provide.

iDoc's chat feature is provider-agnostic. Public configuration supports providers like DeepSeek, OpenAI, Google Gemini, Groq, Hugging Face Inference API, Together AI, and OpenAI-compatible local servers. The important part is not the provider list. The important part is that the assistant is grounded in the API documentation surface.

It can sit beside the OpenAPI reference and help answer questions like:

- Which endpoint should I call for this workflow?
- What payload shape does this route expect?
- Can you show an example request body?
- Which authorization header should I use?

The chat panel also includes a request tester workflow. That makes the assistant less of a chatbot and more of a navigation layer over the API.

For me, that is the right direction. API documentation should become an operating console for integration work.

## Wide compatibility is a maintenance promise

One detail I care about in iDoc is the compatibility range. The public Composer constraints support older Laravel 5.5-era applications and modern Laravel versions up through the current 13 series constraints in the repository.

That is not glamorous, but it matters.

Many useful Laravel applications are not rewritten every year. They are upgraded carefully, often under business pressure, while still serving real users. A documentation generator that only works for the newest stack leaves a lot of serious applications behind.

iDoc is designed for those real-world codebases:

```bash
composer require ovac/idoc
php artisan vendor:publish --tag=idoc-config
php artisan idoc:generate
```

For Lumen applications, it also exposes a service provider path so smaller API services can use the same documentation approach.

## What I learned building it

The biggest lesson is that API docs fail when they become ceremonial.

If documentation requires a developer to stop normal work, open another tool, and manually rewrite what the code already knows, it will eventually drift. The better path is to make documentation part of the application lifecycle.

That means:

- Generate the spec from the app.
- Render it in a way humans actually want to read.
- Let developers try requests from the same surface.
- Keep configuration in Laravel where the team already works.
- Let teams customize prompts, routes, middleware, themes, and assets without forking the package.

That is the shape of iDoc.

It is not just "docs for Laravel APIs." It is a small argument about how API teams should work: keep the documentation close to the code, keep the reference interactive, and make the integration path obvious.

If your API is important enough for another person to integrate with it, it is important enough to have documentation that behaves like part of the product.

Repository: [github.com/ovac/idoc](https://github.com/ovac/idoc)

Docs: [ovac4u.com/idoc](https://www.ovac4u.com/idoc)
