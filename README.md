## 📖 Entendendo os Parâmetros de Execução da Veracode

Esta seção complementa a documentação das flags, apresentando os conceitos por trás dos parâmetros e seu papel na execução das ferramentas de segurança.

### 🔍 Diagnóstico e detalhamento da execução

| Conceito     | Descrição                                                                                                                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Debug**    | Utilizado para investigar falhas técnicas, fornecendo informações adicionais sobre o comportamento interno da ferramenta. É especialmente útil quando uma etapa apresenta erros inesperados. |
| **Verbose**  | Aumenta o nível de detalhamento das mensagens de execução, permitindo acompanhar melhor as etapas realizadas pela ferramenta.                                                                |
| **Log File** | Arquivo utilizado para registrar informações da execução, permitindo consultar erros e eventos mesmo após o término do processo.                                                             |
| **Retry**    | Mecanismo de repetição de operações após determinadas falhas temporárias, como timeouts ou problemas de comunicação.                                                                         |

### ⚙️ Controle do processo de análise

| Conceito              | Descrição                                                                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Action**            | Determina a operação que será executada pela ferramenta, definindo o objetivo da chamada à API.                                            |
| **Source**            | Representa a origem do código-fonte que será processado ou analisado.                                                                      |
| **File**              | Identifica um arquivo específico que será fornecido como entrada para determinada operação.                                                |
| **Project Name**      | Permite identificar a aplicação ou o projeto associado à execução, facilitando sua organização e rastreabilidade.                          |
| **Project Reference** | Relaciona a análise a uma versão específica do código, permitindo distinguir execuções realizadas em branches, tags ou commits diferentes. |
| **Policy**            | Representa um conjunto de regras utilizado como referência para avaliar os resultados da análise.                                          |

### 🚦 Critérios de aprovação e falha

Os parâmetros de controle permitem definir como os resultados de segurança afetam o andamento do pipeline.

* **Severidade:** classifica a gravidade das vulnerabilidades identificadas e pode ser utilizada para determinar se a execução deve falhar.
* **CWE (Common Weakness Enumeration):** identifica categorias de fraquezas de software, permitindo estabelecer critérios de bloqueio com base no tipo de problema encontrado.
* **Critério de falha:** regra que determina se o resultado da análise permite a continuidade do pipeline ou provoca sua interrupção.
* **Exit Code:** código retornado pelo processo ao finalizar. Em pipelines de CI/CD, normalmente é utilizado para determinar se uma etapa foi concluída com sucesso ou falhou.

### 🔐 Autenticação e configuração

| Conceito               | Descrição                                                                                                                       |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **API ID**             | Identifica a credencial utilizada para acessar os serviços da Veracode.                                                         |
| **API Key**            | Componente secreto da credencial, utilizado em conjunto com o API ID para autenticação.                                         |
| **Credential Profile** | Permite reutilizar credenciais previamente configuradas, evitando informar os dados de autenticação em cada comando.            |
| **Input File**         | Arquivo externo utilizado para fornecer parâmetros de execução, facilitando a configuração de operações com múltiplas entradas. |

### 📦 Empacotamento e dependências

| Conceito         | Descrição                                                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Packager**     | Componente responsável por preparar ou empacotar o projeto de acordo com sua linguagem ou tecnologia.                                             |
| **Cache**        | Armazena dados ou artefatos para reutilização, podendo reduzir o tempo de execução de operações posteriores.                                      |
| **Trust**        | Relaciona-se às configurações de confiança reconhecidas pela ferramenta, conforme o mecanismo implementado.                                       |
| **Dependências** | Bibliotecas e componentes externos utilizados pela aplicação, que podem ser avaliados por ferramentas de análise de composição de software (SCA). |

### 💡 Aplicação prática no troubleshooting

A escolha do parâmetro deve considerar o tipo de problema observado:

| Situação                                                        | Abordagem recomendada                                                                               |
| --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| A ferramenta encerra inesperadamente.                           | Habilitar o diagnóstico para investigar a causa da falha.                                           |
| Não está claro em qual etapa ocorreu o erro.                    | Aumentar o detalhamento dos logs de execução.                                                       |
| A autenticação falha.                                           | Verificar as credenciais, o perfil utilizado e as permissões associadas.                            |
| O pipeline falha após encontrar vulnerabilidades.               | Revisar os critérios de severidade, CWE e o código de saída do processo.                            |
| A execução falha devido a problemas temporários de comunicação. | Verificar os mecanismos de repetição e os limites de timeout disponíveis.                           |
| O empacotamento não inclui os componentes esperados.            | Revisar a origem do código, as configurações dos empacotadores e as opções de inclusão ou exclusão. |
| O SCA apresenta resultados incompletos.                         | Investigar a descoberta de dependências, os arquivos de configuração e a execução do agente.        |

> **Nota:** os conceitos apresentados descrevem comportamentos gerais de ferramentas de linha de comando e CI/CD. A disponibilidade e o funcionamento exato de cada recurso dependem da ferramenta e de sua versão.
