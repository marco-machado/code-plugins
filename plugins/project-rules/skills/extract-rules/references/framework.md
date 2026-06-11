# Extracting Framework Conventions

## Identify the frameworks first

From the package manifests in the project context, identify the primary framework per area (web frontend, backend, mobile, CLI). Conventions to extract are framework-relative: what matters is how this project uses the framework, especially where it deviates from or constrains the framework's defaults.

## What to examine

Sample the framework's characteristic file types (components, routes, handlers, views, controllers, services) across 10 to 15 files:

- **File roles and placement**: which directories hold which kinds of code; what a route, component, service, or model file is expected to contain; whether the project follows the framework's canonical layout or a custom one.
- **Composition patterns**: e.g. React server vs client components and where `"use client"` appears; hooks vs HOCs vs render props; Vue options vs composition API; Django CBVs vs function views; Express middleware chains vs route-level handlers.
- **Data fetching and state**: the one blessed way data enters the UI or handler layer (loaders, React Query, server actions, services layer, repositories) and which alternatives the project avoids even though the framework allows them.
- **Dependency injection and wiring**: constructor injection vs decorators vs module singletons; where instances are created and registered.
- **Configuration access**: how env vars and settings reach code (central config module vs direct `process.env`/`os.environ` access).
- **Validation and serialization**: the library and the layer where it happens (schemas at the boundary vs inline checks).
- **Project-specific wrappers**: in-house abstractions over framework primitives (a custom `apiClient`, `BaseController`, shared layout components) that new code is expected to use instead of the raw framework API.

## Convention vs accident

- A pattern used in every recent feature but absent from old code is the current convention; say so.
- The presence of two data-fetching approaches in similar proportions is an inconsistency to report, not a rule.
- Wrappers used by most call sites are conventions; wrappers with one consumer are not.

## What to report

Rules such as: where new code of each kind goes, the blessed data-fetching and state pattern, required wrappers over framework primitives, configuration access rules, and framework features the project deliberately avoids. Scope each rule to the area it applies to (frontend, backend, package name in a monorepo).
