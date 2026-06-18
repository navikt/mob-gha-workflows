# mob-gha-workflows

Repoet inneholder gjenbrukbare GitHub Actions workflows. Det er en forhåndsdefinert workflow som kan gjenbrukes på
tvers av flere repositorier. Dette lar oss sentralisere og standardisere CI/CD-logikk,
slik at vi kan oppdatere, vedlikeholde og skalere denne logikken på ett sted.

## Bruk

Referer til workflows med major version tag:

```yaml
jobs:
  build-and-test:
    uses: navikt/mob-gha-workflows/.github/workflows/gradle-build-test-app.yaml@v1

  deploy:
    uses: navikt/mob-gha-workflows/.github/workflows/nais-deploy.yaml@v1
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

| Endring | Versjon |
|---------|---------|
| Ny workflow | Minor (`v1.1.0`) |
| Ny optional input | Minor (`v1.2.0`) |
| Bugfix | Patch (`v1.2.1`) |
| Fjernet/renamed input | **Major** (`v2.0.0`) |
| Endret required permissions | **Major** (`v2.0.0`) |

## Henvendelser

Spørsmål knyttet til koden eller prosjektet kan stilles som issues her på Github.
Interne henvendelser kan sendes via Slack i kanalen [#utbetaling](https://nav-it.slack.com/archives/CKZADNFBP)
