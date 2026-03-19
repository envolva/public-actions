# CLAUDE.md — public-actions

## Projeto

Repositório público da organização **envolva** para experimentação e aprendizado com o [release-please](https://github.com/googleapis/release-please) do Google.

## Stack

- GitHub Actions (CI/CD)
- release-please v4 (versionamento e releases automatizados)
- Conventional Commits (padrão de mensagens de commit)

## Convenções

### Commits

Seguir **Conventional Commits** obrigatoriamente:

```
feat: nova funcionalidade        → bump minor
fix: correção de bug             → bump patch
feat!: breaking change           → bump major
chore: manutenção (sem release)
docs: documentação (sem release)
```

### Branches

- `main` — branch principal, protegida
- Feature branches com PRs para `main`
- Usar **squash merge** nos PRs para manter histórico linear

### Releases

- Gerenciadas automaticamente pelo release-please
- Release type: `simple` (usa `version.txt` + `CHANGELOG.md`)
- Versão inicial: `0.0.0` (pré-1.0 para experimentação)
- Tags no formato `vX.Y.Z`

## Arquivos Importantes

| Arquivo | Propósito |
|---------|-----------|
| `.github/workflows/release-please.yml` | Workflow do release-please |
| `release-please-config.json` | Configuração do release-please |
| `.release-please-manifest.json` | Rastreamento de versão atual |
| `docs/plano-release-please.md` | Plano de ação e aprendizado |

## Comandos Úteis

```bash
# Commit convencional simples
git commit -m "feat: descrição da feature"

# Breaking change
git commit -m "feat!: descrição da mudança que quebra compatibilidade"

# Forçar versão específica
git commit --allow-empty -m "chore: release 1.0.0" -m "Release-As: 1.0.0"
```
