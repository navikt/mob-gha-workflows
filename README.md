# mob-gha-workflows

Repoet inneholder gjenbrukbare GitHub Actions workflows. Det er en forhåndsdefinert workflow som kan gjenbrukes på
tvers av flere repositorier. Dette lar oss sentralisere og standardisere CI/CD-logikk,
slik at vi kan oppdatere, vedlikeholde og skalere denne logikken på ett sted.

## Bruk

### Anbefalt: SHA-pinning med versjon i kommentar

Pin til commit SHA for forutsigbarhet og sikkerhet. Dependabot holder SHA-en oppdatert automatisk.

```yaml
jobs:
  build-and-test:
    uses: navikt/mob-gha-workflows/.github/workflows/gradle-build-test-app.yaml@dac7a1ac47e76110d90af301b2152057c215c867 # ratchet:navikt/mob-gha-workflows/.github/workflows/gradle-build-test-app.yaml@v1.0.0

  deploy:
    uses: navikt/mob-gha-workflows/.github/workflows/nais-deploy.yaml@dac7a1ac47e76110d90af301b2152057c215c867 # ratchet:navikt/mob-gha-workflows/.github/workflows/nais-deploy.yaml@v1.0.0
```

> **Viktig:** Bruk alltid commit SHA (ikke tag SHA). Finn riktig SHA slik:
> ```bash
> git rev-list -n 1 v1.0.0
> ```

### Alternativer

| Referanse | Eksempel | Oppførsel |
|-----------|----------|-----------|
| SHA + versjon (anbefalt) | `@dac7a1ac...` | Låst — dependabot oppdaterer automatisk |
| Major-tag | `@v1` | Får alle `v1.x.x`-oppdateringer automatisk |
| Eksakt versjon | `@v1.0.0` | Låst — må oppdateres manuelt |
| Branch | `@main` | Alltid siste commit, ingen versjonskontroll |

### Dependabot-oppsett

For at dependabot skal holde SHA-er oppdatert, legg til i `.github/dependabot.yaml`:

```yaml
- package-ecosystem: "github-actions"
  directory: "/"
  schedule:
    interval: "weekly"
    day: "tuesday"
    time: "09:00"
    timezone: "Europe/Oslo"
  groups:
    github:
      patterns:
        - "*"
```

## Tilgjengelige workflows

| Workflow | Beskrivelse |
|----------|-------------|
| `gradle-build-test-app.yaml` | Bygg og test Gradle-applikasjon |
| `gradle-build-push-image.yaml` | Bygg og push Docker image (Gradle) |
| `gradle-dependency-submission.yaml` | Submit dependency graph |
| `node-build-push-image.yaml` | Bygg og push Docker image (Node) |
| `node-build-pr.yaml` | Bygg og test Node PR |
| `nais-deploy.yaml` | Deploy til NAIS |
| `unleash-deploy.yaml` | Deploy Unleash |
| `squawk-migration-lint.yaml` | Lint SQL-migrasjoner |
| `codeql-scan.yaml` | CodeQL sikkerhetsskanning |
| `digestabot-update.yaml` | Oppdater base image digests |
| `digestabot-automerge.yaml` | Automerge digestabot-PRer |
| `dependabot-automerge.yaml` | Automerge dependabot-PRer |

## Utvikling og endringer

For å teste endringer før de merges inn i main:

1. Lag en branch i dette repoet.
2. Referer til branchen i konsument-repoet: `@branch-navn` istedenfor `@v1`.

## Releases

Nye releases lages ved å pushe en tag:

```bash
git tag -a v1.0.0 -m "Beskrivelse av endringen"
git push origin v1.0.0
```

Release-workflowen oppretter en GitHub Release med release notes og oppdaterer floating major tag (`v1`).

Lag ny release kun når gjenbrukbare workflows endres. Interne CI-workflows (zizmor, dependency-scan) krever ingen ny release.

### Versjonering

Vi følger [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH` (f.eks. `v1.2.3`).

```
v  1  .  2  .  3
   │     │     └── PATCH — bakoverkompatibel bugfix
   │     └──────── MINOR — ny funksjonalitet, bakoverkompatibel
   └────────────── MAJOR — breaking change, krever oppdatering hos konsumenter
```

**Eksempler:**

| Endring | Fra → Til | Årsak |
|---------|-----------|-------|
| Fikse en feil i `nais-deploy.yaml` | `v1.0.0` → `v1.0.1` | Patch: bugfix |
| Legge til ny optional input | `v1.0.1` → `v1.1.0` | Minor: ny funksjonalitet |
| Legge til ny workflow | `v1.1.0` → `v1.2.0` | Minor: ny funksjonalitet |
| Fjerne en input | `v1.2.0` → `v2.0.0` | Major: breaking change |
| Endre navn på en workflow-fil | `v1.2.0` → `v2.0.0` | Major: breaking change |
| Endre required permissions | `v1.2.0` → `v2.0.0` | Major: breaking change |

**Major-tag (`v1`) oppdateres automatisk** og peker alltid på siste `v1.x.x`-release. Konsumenter som bruker `@v1` får oppdateringer uten å gjøre noe.

| Endring | Konsumenter på `@v1` | Konsumenter på `@v1.0.0` / SHA |
|---------|----------------------|--------------------------------|
| `v1.0.1` patch | Får automatisk ✅ | Må oppdatere (dependabot lager PR) |
| `v1.1.0` minor | Får automatisk ✅ | Må oppdatere (dependabot lager PR) |
| `v2.0.0` major | Får **ikke** — må bytte til `@v2` ⚠️ | Må oppdatere manuelt |

## Henvendelser

Spørsmål knyttet til koden eller prosjektet kan stilles som issues her på Github.
Interne henvendelser kan sendes via Slack i kanalen [#utbetaling](https://nav-it.slack.com/archives/CKZADNFBP)
