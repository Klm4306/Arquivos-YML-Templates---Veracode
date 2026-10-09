# ⚠️ Pontos de Atenção — Pipeline Veracode (CI/CD)

> Leia antes de mexer no workflow. Pequenos detalhes de localização e versão quebram o pipeline silenciosamente.

---

## 📁 Localização do arquivo de workflow

| Plataforma | Caminho obrigatório | Flexível? |
|---|---|---|
| 🐙 **GitHub Actions** | `.github/workflows/*.yml` | ❌ Não — fora daqui o arquivo é **ignorado** |
| 🦊 **GitLab CI/CD** | `.gitlab-ci.yml` (raiz) | ✅ Sim, via *CI/CD Settings* ou `include:` |
| 🔷 **Azure DevOps** | Definido na UI do pipeline | ✅ Sim, qualquer caminho |

> 🔴 **Atenção GitHub:** se o `.yml` estiver na raiz do repositório (fora de `.github/workflows/`), o Actions **não executa** e **não aparece** na aba *Actions*. Não há erro visível — o workflow simplesmente não roda.

---

## 🚫 Actions descontinuadas

| Action | Status | Ação recomendada |
|---|---|---|
| `actions/upload-artifact@v1` / `v2` | ❌ Bloqueado automaticamente | Migrar para `@v4` |
| `actions/download-artifact@v1` / `v2` | ❌ Bloqueado automaticamente | Migrar para `@v4` |
| `veracode/veracode-uploadandscan-action@0.2.10` | ⚠️ Depende internamente de action antiga | Substituir por chamada direta ao **Java Wrapper** |

> 💡 O erro pode vir de **dentro de uma action de terceiros**, mesmo que seu YAML use `@v4` corretamente. Sempre verifique as dependências internas da action.

---

## 🔑 Segredos (Secrets) necessários

```
VeracodeID          → API ID da Veracode
VeracodeKey         → API Key da Veracode
SCA                 → Token para Source Clear (SCA)
VeracodePolicyName  → Nome da política usada no Pipeline Scan
```

> 🔴 Sem esses secrets configurados em **Settings → Secrets and variables → Actions**, os jobs falham na autenticação.

---

## 📦 Empacotamento (zip)

- ✅ Inclua apenas extensões relevantes para o scan (`.py`, `.js`, `.java`, etc.)
- ✅ Exclua `node_modules`, `.git`, `dist` e arquivos de teste
- ⚠️ A exclusão de `.git` é só **boa prática de empacotamento** — não afeta o funcionamento do Actions

---

## 🧩 Boas práticas gerais

- 🔄 Sempre busque a **versão mais recente** de wrappers/jars dinamicamente (via `maven-metadata.xml`), em vez de fixar uma versão manualmente
- 🕒 Defina `timeout-minutes` em todos os jobs para evitar execuções travadas
- 🔒 Use `concurrency` em jobs de scan para evitar execuções simultâneas conflitantes
- 🧪 Rode `Veracode_SCA` e `Veracode_SAST` com `continue-on-error: true` se não quiser bloquear o build por vulnerabilidades — avalie o risco antes de usar essa flag

---

## ✅ Checklist rápido antes de commitar

- [ ] Arquivo está em `.github/workflows/`?
- [ ] Todas as actions estão em versões atuais (`v4`+)?
- [ ] Secrets configurados no repositório?
- [ ] Nome do job/branch bate com o trigger (`on: push branches`)?
- [ ] Testado via `workflow_dispatch` antes de depender do push automático?

---

## 🛠️ Flags importantes para diagnóstico e execução

🔍 --debug
Use --debug nas execuções do Veracode para habilitar logs mais detalhados. A flag é especialmente útil para investigar problemas de autenticação, upload, comunicação com a plataforma, configuração dos parâmetros e comportamento do wrapper.

Exemplo:

java -jar VeracodeJavaAPI.jar ... --debug

⚠️ Atenção: os logs de debug podem expor informações adicionais da execução. Não publique logs completos contendo credenciais, tokens ou secrets no repositório ou em issues públicas.

---

## 💡 Recomendação de troubleshooting:
Ao investigar uma falha no pipeline, primeiro execute novamente com --debug e preserve os logs da execução. Compare principalmente as etapas de autenticação → empacotamento → upload → scan → resultado/policy, pois isso ajuda a identificar em qual etapa o problema ocorreu.
