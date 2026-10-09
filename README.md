# 🛠️ Parâmetros e Flags de Diagnóstico — Veracode

Este documento apresenta os principais parâmetros e flags utilizados nas ferramentas da Veracode, explicando suas funções e como contribuem para a configuração de análises de segurança e o diagnóstico de problemas em pipelines de CI/CD.

## 🔎 1. Pipeline Scan — `pipeline-scan.jar`

Ferramenta utilizada para realizar análises de segurança em aplicações, permitindo configurar critérios de falha e identificar vulnerabilidades.

| Parâmetro                    | Descrição                                                                                   |
| ---------------------------- | ------------------------------------------------------------------------------------------- |
| `-Dpipeline.debug=true`      | Habilita logs de diagnóstico para auxiliar na investigação de erros durante a execução.     |
| `--file` / `-f`              | Especifica o arquivo da aplicação que será analisado.                                       |
| `--fail_on_severity` / `-fs` | Define os níveis de severidade que provocarão a falha do pipeline.                          |
| `--fail_on_cwe` / `-fc`      | Define os identificadores CWE que provocarão a falha do pipeline.                           |
| `--request_policy` / `-rp`   | Solicita uma policy personalizada para orientar os critérios da análise.                    |
| `--project_name` / `-p`      | Define o nome do projeto associado à análise.                                               |
| `--project_url` / `-u`       | Informa a URL do repositório do projeto.                                                    |
| `--project_ref` / `-r`       | Identifica a referência do código analisado, como branch, tag ou commit.                    |
| `--include` / `-i`           | Especifica os módulos que devem ser incluídos na análise, conforme o suporte da ferramenta. |
| `--help` / `-h`              | Exibe os parâmetros e as instruções de utilização disponíveis.                              |
| `--version` / `-v`           | Exibe a versão do Pipeline Scanner.                                                         |

**Exemplo de utilização:**

```bash
java -Dpipeline.debug=true -jar pipeline-scan.jar \
  --veracode_api_id "$VeracodeID" \
  --veracode_api_key "$VeracodeKey" \
  --file "app.zip" \
  --fail_on_severity="Very High, High"
```

## 📦 2. Veracode CLI — `veracode package`

Utilizado para empacotar o código-fonte e preparar os artefatos necessários às etapas seguintes do processo de segurança.

| Parâmetro          | Descrição                                                                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--verbose`        | Exibe informações detalhadas sobre as etapas de empacotamento.                                                                                     |
| `--debug`          | Parâmetro de diagnóstico legado, indicado como deprecated no material de referência. Prefira `--verbose` quando recomendado pela versão instalada. |
| `--source`         | Especifica o diretório ou caminho do código-fonte utilizado no empacotamento.                                                                      |
| `--trust`          | Controla o tratamento de componentes confiáveis, conforme as opções suportadas pelo CLI.                                                           |
| `--skip-packagers` | Permite ignorar empacotadores específicos durante o processo.                                                                                      |

**Exemplo de utilização:**

```bash
./veracode package --source . --verbose
```

## 🧪 3. Veracode SCA Agent — `srcclr`

Ferramenta voltada à análise de composição de software (SCA), utilizada para identificar vulnerabilidades e riscos associados às dependências de uma aplicação.

| Parâmetro/Variável | Descrição                                                                                         |
| ------------------ | ------------------------------------------------------------------------------------------------- |
| `--debug`          | Exibe informações adicionais para auxiliar na investigação de erros durante a execução do agente. |
| `test`             | Executa testes de ambiente para verificar os requisitos necessários à utilização do agente.       |
| `--npm`            | Especifica o contexto de teste relacionado ao npm.                                                |
| `--maven`          | Especifica o contexto de teste relacionado ao Maven.                                              |
| `--gradle`         | Especifica o contexto de teste relacionado ao Gradle.                                             |
| `--pip`            | Especifica o contexto de teste relacionado ao pip.                                                |
| `DEBUG=1`          | Habilita mensagens adicionais de diagnóstico no script de CI compatível.                          |
| `NOCACHE=1`        | Evita o uso do cache durante a instalação pelo script de CI compatível.                           |

**Exemplo — execução com diagnóstico:**

```bash
srcclr scan --debug
```

**Exemplo — instalação pelo script de CI com logs adicionais:**

```bash
curl -sSL https://sca-downloads.veracode.com/ci.sh | DEBUG=1 sh
```

> **Nota:** os parâmetros de teste e os argumentos aceitos podem variar conforme a versão do agente e o comando utilizado.

## 🔐 4. Veracode Java API Wrapper — `vosp-api-wrapper-java.jar`

Permite executar operações da API da Veracode, incluindo o envio de aplicações e o início de análises por meio de comandos Java.

| Parâmetro        | Descrição                                                                          |
| ---------------- | ---------------------------------------------------------------------------------- |
| `-debug`         | Habilita informações adicionais de diagnóstico durante a execução.                 |
| `-action`        | Define a operação que será executada, como `uploadandscan`.                        |
| `-vid`           | Informa o identificador da API utilizado na autenticação.                          |
| `-vkey`          | Informa a chave secreta da API utilizada na autenticação.                          |
| `-credprofile`   | Seleciona um perfil de credenciais previamente configurado.                        |
| `-logfilepath`   | Define o caminho do arquivo em que os logs serão armazenados.                      |
| `-maxretrycount` | Define o número de novas tentativas para operações compatíveis com esse mecanismo. |
| `-inputfilepath` | Especifica o arquivo CSV que contém parâmetros de execução.                        |
| `-help`          | Exibe as opções e instruções disponíveis no wrapper.                               |

**Exemplo de utilização:**

```bash
java -jar vosp-api-wrapper-java.jar \
  -credprofile "default" \
  -action uploadandscan \
  -debug \
  -logfilepath "veracode.log"
```

> **Segurança:** prefira utilizar perfis de credenciais ou mecanismos seguros de armazenamento de secrets em vez de informar credenciais diretamente no comando.

## 🖥️ 5. Veracode CLI — `veracode static scan`

Utilizado para executar análises estáticas de segurança (SAST) por meio do Veracode CLI.

| Parâmetro   | Descrição                                                                                                       |
| ----------- | --------------------------------------------------------------------------------------------------------------- |
| `--verbose` | Solicita informações adicionais sobre a execução da análise.                                                    |
| `<source>`  | Representa o caminho ou a origem do código que será analisado, conforme a sintaxe aceita pela versão instalada. |

**Exemplo de utilização:**

```bash
./veracode static scan <source> --verbose
```

> Consulte a documentação da versão instalada para confirmar a sintaxe e os argumentos aceitos pelo comando `static scan`.

## 📋 6. Resumo das flags de diagnóstico

| Ferramenta                 | Parâmetro               | Finalidade                                     |
| -------------------------- | ----------------------- | ---------------------------------------------- |
| Pipeline Scan              | `-Dpipeline.debug=true` | Habilitar logs de diagnóstico.                 |
| Veracode CLI — Package     | `--verbose`             | Detalhar o processo de empacotamento.          |
| Veracode CLI — Static Scan | `--verbose`             | Detalhar a execução da análise estática.       |
| Veracode SCA Agent         | `--debug`               | Investigar erros durante a execução do agente. |
| SCA CI Script              | `DEBUG=1`               | Habilitar logs adicionais no script de CI.     |
| Java API Wrapper           | `-debug`                | Exibir informações de diagnóstico.             |

## 🔍 7. Diferença entre `debug` e `verbose`

* **Debug:** prioriza informações úteis para identificar a origem de erros e problemas técnicos.
* **Verbose:** amplia o detalhamento das mensagens exibidas durante a execução de um processo.
* **Importante:** a disponibilidade e o comportamento dessas opções dependem da ferramenta e da versão utilizada. Não presuma que uma flag aceita em um componente funcionará em outro.

## 🔒 8. Boas práticas de segurança

* Armazene API IDs, API Keys e tokens em mecanismos seguros de secrets do CI/CD.
* Evite inserir credenciais diretamente em comandos, scripts ou arquivos versionados.
* Revise os logs gerados antes de compartilhá-los, verificando se contêm informações sensíveis.
* Utilize flags de diagnóstico somente quando necessário, especialmente em ambientes de produção.
* Consulte a documentação oficial antes de adicionar parâmetros ao pipeline.

## 📚 9. Referências oficiais

* [Documentação da Veracode](https://docs.veracode.com/)
* [Veracode CLI](https://docs.veracode.com/r/Veracode_CLI)
* [Pipeline Scan](https://docs.veracode.com/r/Pipeline_Scan)
* [Veracode Software Composition Analysis](https://docs.veracode.com/r/Veracode_Software_Composition_Analysis)

---

**Objetivo:** padronizar a utilização dos parâmetros da Veracode, facilitar o troubleshooting e documentar as opções disponíveis para configuração das ferramentas em pipelines de CI/CD.
