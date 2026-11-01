# Signals

Where to look before reading any source file. Rules are technology agnostic. Examples are the only place technology names appear; match on the pattern, not the list. Read a signal file fully. Infer from names first. Grep second. Limits are in `scan-budget.md`.

## Signal files

| Category | Tells you | Examples |
|---|---|---|
| Dependency manifest | language, framework, libraries, unit boundary | `package.json`, `pyproject.toml`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`, `build.gradle(.kts)`, `*.csproj`, `Gemfile`, `composer.json`, `mix.exs`, `pubspec.yaml` |
| Internal dependencies | related repositories | dependencies whose scope or group matches the repository's organisation (an `@org/` scope, a group id, a module path prefix), git or private-registry sources, path dependencies outside the repository; `repository` and `homepage` fields in manifests give the link |
| Lockfile | confirms the ecosystem. Name only, never read | `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `poetry.lock`, `uv.lock`, `go.sum`, `Cargo.lock`, `Gemfile.lock`, `composer.lock` |
| Build and workspace config | build tool, monorepo units | `tsconfig.json`, `vite.config.*`, `webpack.config.*`, `Makefile`, `CMakeLists.txt`, `settings.gradle(.kts)`, `nx.json`, `turbo.json`, `pnpm-workspace.yaml`, `lerna.json`, a `workspaces` field |
| Runtime pins | language and tool versions | `.nvmrc`, `.node-version`, `.tool-versions`, `.python-version`, `.ruby-version`, `.java-version`, `runtime.txt` |
| Container | deployables, local services | `Dockerfile*`, `docker-compose*.yml`, `compose.yaml` |
| Orchestration and IaC | deploy targets, topology | `k8s/`, `kubernetes/`, `Chart.yaml`, `*.tf`, `Pulumi.yaml`, `serverless.yml`, `cdk.json`, `fly.toml`, `render.yaml`, `vercel.json`, `netlify.toml`, `app.yaml` |
| CI/CD | pipelines, verification commands | `.github/workflows/*`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml`, `bitbucket-pipelines.yml`, `azure-pipelines.yml` |
| Environment and config | integrations, data stores | `.env.example`, `.env.sample`, `config/`, `application*.yml`, `application*.properties`, `appsettings*.json`, `settings.py` |
| Entry points | composition roots | `main.*`, `index.*`, `app.*`, `server.*`, `cmd/*/main.go`, `Program.cs`, `*Application.java`, `manage.py`, `wsgi.py`, `asgi.py`, `bin/` |
| API surface | features, operations | `routes/`, `controllers/`, `handlers/`, `api/`, `resolvers/`, `urls.py`, `openapi*`, `swagger*`, `*.graphql`, `*.proto` |
| Data models | domain entities | `models/`, `entities/`, `domain/`, `schema.prisma`, `schema.sql`, `migrations/` (names only), `*.entity.*` |
| Messaging and jobs | async operations | `events/`, `consumers/`, `producers/`, `jobs/`, `workers/`, `queues/`, `subscribers/`, `cron/` |
| UI surface | features, actors | `pages/`, `views/`, `screens/`, route folders, navigation or menu components, translation bundles in `locales/` or `i18n/` (grep labels, never read fully) |
| Tests | feature names. Names only | `test/`, `tests/`, `__tests__/`, `spec/`, `e2e/`, `*.spec.*`, `*.test.*`, `*_test.*` |
| Docs | everything | `README*`, `docs/`, `doc/`, `CONTRIBUTING*`, `ARCHITECTURE*`, `adr/`, `docs/adr/`, `docs/decisions/`, `CHANGELOG*`, `mkdocs.yml`, `docusaurus.config.*` |
| Agent context files | architecture, conventions. Treat as docs | `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.cursor/rules/`, `.github/copilot-instructions.md` |

## Never read

`node_modules/`, `vendor/`, `.venv/`, `venv/`, `target/`, `dist/`, `build/`, `out/`, `coverage/`, `.next/`, `.nuxt/`, `generated/`, `__generated__/`, `__snapshots__/`, `fixtures/`, `*.min.*`, `*.map`, lockfiles, binaries, images, data dumps. Add what the repository's `.gitignore` excludes.

## Grep hints

Derive patterns from the frameworks the manifests revealed. You know their declaration idioms for routes, handlers, entities and events. Record the pattern you used as evidence. `files_with_matches` or `count` first, then `content` with `head_limit` 30 and no context lines.

Framework-independent conventions, always worth a try:

| Looking for | Pattern |
|---|---|
| Endpoints | HTTP verb near a path literal: ``(get\|post\|put\|patch\|delete)\W{0,5}["'`]/`` (case-insensitive); path literals: ``["'`]/[a-z0-9/_:{}.-]+["'`]`` |
| Declaration sites | files that import the framework's routing, ORM or messaging module. Grep the import. |
| Roles by name | `(Controller\|Handler\|Resolver\|Service\|Repository\|Entity\|Model\|Schema\|Event\|Consumer\|Listener\|Job\|Worker)\b` in file and type names |
| Migrations | file names `\d{8,14}[_-]\w+` or `V\d+__\w+` |
| External systems | environment names `_(URL\|HOST\|KEY\|SECRET\|TOKEN\|DSN)$`; directories `clients/\|integrations/\|adapters/\|gateways/` |
| Feature flags | `feature[_-]?flag\|isEnabled\(\|flags?\.` |
| UI navigation | translation keys `(nav\|menu\|sidebar)\.`; file or component names containing `nav\|menu\|sidebar` |
| Actors | `(role\|permission\|scope\|tenant)s?\b`; role words in names and labels |
