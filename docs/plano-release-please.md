# Plano de Ação — Integração release-please

## O que é o release-please?

Ferramenta do Google que automatiza:
- Geração de **CHANGELOG.md**
- Criação de **GitHub Releases**
- Bump de **versão** (semver)

Ele funciona parseando mensagens de commit no padrão **Conventional Commits** e criando automaticamente um PR de release que acumula todas as mudanças.

## Como funciona o fluxo

```
Developer faz merge na main com commits convencionais
        ↓
release-please action roda (trigger: push to main)
        ↓
Se há commits "releasáveis" (feat/fix):
  → Cria ou atualiza um Release PR
        ↓
Release PR fica aberto acumulando mudanças
        ↓
Quando você faz merge do Release PR:
  → Cria tag (ex: v1.2.3)
  → Cria GitHub Release com notas
  → Atualiza CHANGELOG.md e version.txt
```

## Etapas de Implementação

### Fase 1 — Setup inicial (configuração base)

- [x] Criar `CLAUDE.md` com convenções do projeto
- [x] Criar este plano de ação
- [ ] Criar `release-please-config.json`
- [ ] Criar `.release-please-manifest.json` (versão `0.0.0`)
- [ ] Criar `.github/workflows/release-please.yml`
- [ ] Criar `.gitignore`
- [ ] Criar `README.md` básico
- [ ] Commit inicial e push para `main`

### Fase 2 — Primeiro release (testando o fluxo)

- [ ] Fazer commit com `feat: setup inicial do projeto`
- [ ] Verificar se o release-please cria o Release PR automaticamente
- [ ] Inspecionar o conteúdo do Release PR (CHANGELOG proposto, version bump)
- [ ] Fazer merge do Release PR
- [ ] Verificar se a tag `v0.1.0` foi criada
- [ ] Verificar se o GitHub Release foi criado com as notas corretas

### Fase 3 — Explorando tipos de commit

- [ ] Testar `fix:` → deve gerar bump patch (0.1.0 → 0.1.1)
- [ ] Testar `feat:` → deve gerar bump minor (0.1.1 → 0.2.0)
- [ ] Testar `feat!:` (breaking change) → com `bump-minor-pre-major` ativo, deve gerar minor enquanto < 1.0
- [ ] Testar `chore:` → NÃO deve gerar release
- [ ] Testar múltiplos commits acumulados em um único Release PR

### Fase 4 — Funcionalidades avançadas

- [ ] Testar `Release-As: X.Y.Z` para forçar versão específica
- [ ] Experimentar `changelog-sections` customizadas
- [ ] Testar `extra-files` para atualizar versão em outros arquivos
- [ ] Explorar comportamento com `draft: true` (releases como rascunho)
- [ ] Testar edição do Release PR com `BEGIN_COMMIT_OVERRIDE` / `END_COMMIT_OVERRIDE`

## Configuração Planejada

### release-please-config.json

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "release-type": "simple",
  "bump-minor-pre-major": true,
  "bump-patch-for-minor-pre-major": true,
  "include-component-in-tag": false,
  "changelog-sections": [
    { "type": "feat", "section": "🚀 Funcionalidades" },
    { "type": "fix", "section": "🐛 Correções" },
    { "type": "perf", "section": "⚡ Performance" },
    { "type": "refactor", "section": "♻️ Refatoração" },
    { "type": "docs", "section": "📚 Documentação" },
    { "type": "chore", "section": "🔧 Manutenção", "hidden": true }
  ],
  "packages": {
    ".": {}
  }
}
```

### .release-please-manifest.json

```json
{
  ".": "0.0.0"
}
```

### Workflow (.github/workflows/release-please.yml)

```yaml
on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

name: release-please

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        id: release
        with:
          config-file: release-please-config.json
          manifest-file: .release-please-manifest.json
```

## Referências

- [release-please](https://github.com/googleapis/release-please) — biblioteca core
- [release-please-action](https://github.com/googleapis/release-please-action) — GitHub Action
- [Conventional Commits](https://www.conventionalcommits.org/) — especificação de commits
- [SemVer](https://semver.org/) — versionamento semântico

## Notas Importantes

1. **Token**: Usando `GITHUB_TOKEN` padrão inicialmente. Se precisar que outros workflows disparem em eventos de release, será necessário usar um PAT (Personal Access Token).

2. **Squash merge**: Recomendado para PRs, pois mantém o histórico linear e dá controle preciso sobre as entradas do changelog.

3. **Pré-1.0**: Com `bump-minor-pre-major: true`, breaking changes geram bump minor (não major) enquanto a versão estiver abaixo de 1.0.0. Ideal para fase de experimentação.

4. **Commits não-convencionais**: Commits que não seguem o padrão são simplesmente ignorados pelo release-please.
