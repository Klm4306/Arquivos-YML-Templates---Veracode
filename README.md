### 🛠️ Flags importantes por ferramenta Veracode

As flags de diagnóstico e controle variam conforme a ferramenta utilizada. **Não assumir que `--debug` funciona em todos os comandos.**

#### 🔎 Pipeline Scan — `pipeline-scan.jar`

Para habilitar logs detalhados, utilize a propriedade Java:

```bash
java -Dpipeline.debug=true -jar pipeline-scan.jar \
  --veracode_api_id "$VeracodeID" \
  --veracode_api_key "$VeracodeKey" \
  --file "app.zip" \
  --fail_on_severity="Very High, High"
```

**Principais parâmetros:**

| Parâmetro                    | Uso                                               |
| ---------------------------- | ------------------------------------------------- |
| `-Dpipeline.debug=true`      | 🔍 Habilita logs de debug para troubleshooting    |
| `--file` / `-f`              | 📦 Define o arquivo empacotado que será analisado |
| `--fail_on_severity` / `-fs` | 🚦 Define as severidades que fazem o job falhar   |
| `--fail_on_cwe` / `-fc`      | 🚦 Define CWEs que fazem o job falhar             |
| `--request_policy` / `-rp`   | 📜 Baixa uma policy customizada                   |
| `--project_name` / `-p`      | 🏷️ Identifica o projeto/repositório              |
| `--project_url` / `-u`       | 🔗 URL do repositório                             |
| `--project_ref` / `-r`       | 🌿 Branch, tag ou commit analisado                |
| `--include` / `-i`           | 📋 Define módulos a serem incluídos no scan       |
| `--help` / `-h`              | ❓ Exibe os parâmetros disponíveis                 |
| `--version` / `-v`           | ℹ️ Exibe a versão do Pipeline Scanner             |

O Pipeline Scan também permite configurar o comportamento de falha por severidade ou CWE, sendo importante documentar essas opções para deixar claro o critério utilizado pelo pipeline.

---

#### 📦 Veracode CLI — `veracode package`

Para empacotamento pelo CLI:

```bash
./veracode package --source . --verbose
```

**Principais parâmetros:**

| Parâmetro          | Uso                                                                                     |
| ------------------ | --------------------------------------------------------------------------------------- |
| `--verbose`        | 🔍 Exibe informações detalhadas durante o empacotamento                                 |
| `--debug`          | ⚠️ Disponível por compatibilidade, mas **deprecated**; prefira `--verbose`              |
| `--source`         | 📁 Define o código-fonte a ser empacotado                                               |
| `--trust`          | 🔐 Permite executar o empacotamento considerando os componentes confiáveis configurados |
| `--skip-packagers` | ⏭️ Exclui determinados packagers do processo                                            |

**Recomendação:** em novos workflows, utilizar `--verbose` em vez de `--debug` no `veracode package`.

---

#### 🧪 Veracode SCA Agent — `srcclr`

Para diagnóstico do SCA Agent:

```bash
srcclr scan --debug
```

**Principais opções:**

| Parâmetro/variável | Uso                                                                      |
| ------------------ | ------------------------------------------------------------------------ |
| `--debug`          | 🔍 Ativa informações adicionais para troubleshooting                     |
| `test`             | 🧪 Testa o ambiente antes da execução do scan                            |
| `--npm`            | 📦 Teste específico para projetos npm                                    |
| `--maven`          | 📦 Teste específico para Maven                                           |
| `--gradle`         | 📦 Teste específico para Gradle                                          |
| `--pip`            | 📦 Teste específico para pip                                             |
| `DEBUG=1`          | 🔍 Ativa logs detalhados quando utilizando o script de CI                |
| `NOCACHE=1`        | ♻️ Evita o uso do cache do agente durante a instalação pelo script de CI |

Exemplo utilizando o instalador de CI:

```bash
curl -sSL https://sca-downloads.veracode.com/ci.sh | DEBUG=1 sh
```

O `DEBUG=1` é particularmente útil quando o SCA é executado pelo script de CI, enquanto `srcclr scan --debug` é utilizado diretamente com o agente.

---

#### 🔐 Veracode Java API Wrapper — `vosp-api-wrapper-java.jar`

No Java API Wrapper, o parâmetro de debug é diferente:

```bash
java -jar vosp-api-wrapper-java.jar \
  -vid "$VeracodeID" \
  -vkey "$VeracodeKey" \
  -action uploadandscan \
  -debug
```

**Principais parâmetros:**

| Parâmetro        | Uso                                                                        |
| ---------------- | -------------------------------------------------------------------------- |
| `-debug`         | 🔍 Exibe informações detalhadas de diagnóstico                             |
| `-action`        | ⚙️ Define a operação da API                                                |
| `-vid`           | 🔑 API ID                                                                  |
| `-vkey`          | 🔑 API Key                                                                 |
| `-credprofile`   | 🔐 Utiliza um profile do arquivo de credenciais                            |
| `-logfilepath`   | 📝 Define o arquivo para gravação dos logs                                 |
| `-maxretrycount` | 🔄 Define o número de tentativas adicionais em determinadas falhas/timeout |
| `-inputfilepath` | 📄 Lê parâmetros de execução a partir de um CSV                            |
| `-help`          | ❓ Exibe parâmetros e ações disponíveis                                     |

**Recomendação:** prefira `-credprofile` ou arquivo de credenciais em vez de colocar `-vid` e `-vkey` diretamente no comando quando possível. O wrapper também permite `-logfilepath` para persistir informações de execução.

---

#### 🖥️ Veracode CLI — `veracode static scan`

Para SAST utilizando o Veracode CLI:

```bash
./veracode static scan <source> --verbose
```

Utilize as flags específicas do comando `static scan` para configurar o comportamento do scan e seus critérios de falha. A documentação do CLI deve ser consultada para a versão instalada antes de adicionar flags ao workflow.

---

### ⚠️ Regra prática para troubleshooting

| Ferramenta                          | Flag de diagnóstico     |
| ----------------------------------- | ----------------------- |
| Pipeline Scan (`pipeline-scan.jar`) | `-Dpipeline.debug=true` |
| Veracode CLI — `package`            | `--verbose`             |
| Veracode CLI — `scan`               | `--verbose`             |
| SCA Agent (`srcclr`)                | `--debug`               |
| SCA CI Script                       | `DEBUG=1`               |
| Java API Wrapper                    | `-debug`                |

🔒 **Segurança:** nunca publique logs de debug contendo API ID, API Key, tokens ou outros secrets. Utilize os mecanismos de `Secrets` do GitHub Actions/GitLab/Azure DevOps e verifique os logs antes de compartilhá-los.

💡 **Troubleshooting:** ao investigar uma falha, identifique primeiro qual ferramenta está executando o scan e utilize **a flag correspondente acima**. Não adicione `--debug` genericamente ao workflow, pois a sintaxe varia entre os componentes.
