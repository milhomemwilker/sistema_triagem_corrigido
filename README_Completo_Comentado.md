# Sistema de Triagem — documentação completa e código comentado

Esta documentação explica o trabalho realizado na correção do Sistema de Triagem PS Escola: investigação dos problemas, alterações nos arquivos, decisões de implementação, testes e preparação para o GitHub. Foram corrigidos 12 problemas inicialmente identificados e uma falha adicional encontrada durante o teste em navegador.

Os comentários dos exemplos são didáticos e foram acrescentados nesta documentação. Os trechos integrais de JavaScript preservam o comportamento dos arquivos corrigidos; esta recriação do README não altera a versão publicada.

## Como estudar este documento

1. Leia o histórico do trabalho e o mapa dos arquivos.
2. Consulte os 13 problemas por prioridade.
3. Compare as remoções e adições nos patches.
4. Estude os arquivos completos comentados no final.
5. Consulte os testes e as limitações para entender o que foi verificado.

## O que foi feito e por quê

| Etapa | Trabalho realizado | Finalidade e resultado |
| --- | --- | --- |
| 1 | Inspeção do ZIP, HTML, JavaScript e instruções de execução | Identificar os arquivos e confirmar que se trata de aplicação estática com persistência no navegador |
| 2 | Execução em ambiente de prévia e disponibilização privada | Abrir cadastro, triagem e fila sem instalar dependências do aplicativo |
| 3 | Análise das funções de cadastro, triagem, fila e armazenamento | Identificar erros de lógica, validação, seleção de pacientes e renderização |
| 4 | Reprodução de falhas em funções isoladas | Confirmar prioridade incorreta, exclusão por índice, conversão da pressão e temperatura, formulário vazio e erro de JSON |
| 5 | Correção dos 12 problemas iniciais | Alterar os cinco arquivos JavaScript envolvidos, preservando as telas e regras educacionais existentes |
| 6 | Teste pelo navegador com dados fictícios | Descobrir uma falha adicional de compatibilidade na geração de IDs e corrigi-la |
| 7 | Testes de regressão | Aprovar 14 verificações automatizadas e concluir cadastro, sugestão, registro e exibição na fila pelo navegador |
| 8 | Atualização da versão privada | Publicar a versão corrigida no mesmo endereço, mantendo o acesso privado |
| 9 | Documentação no template | Registrar comportamento original, esperado, área afetada e solução aplicada nos relatórios Word |
| 10 | Preparação para GitHub | Organizar HTML, assets, testes, package.json, README, CHANGELOG e arquivos de configuração em ZIP |
| 11 | Geração e conferência dos patches | Aplicar o patch consolidado em cópia original, comparar os sete arquivos resultantes e executar novamente os 14 testes |
| 12 | Ampliação deste README | Explicar funções, decisões, testes, aplicação e reversão dos patches, sem acrescentar funcionalidades ao sistema |

## Mapa dos arquivos e das responsabilidades

| Arquivo | Responsabilidade | Alteração realizada |
| --- | --- | --- |
| `index.html` | Formulários, telas e importação dos scripts | Preservado nesta correção |
| `assets/styles.css` | Apresentação e layout | Preservado nesta correção |
| `assets/state.js` | Estado compartilhado e persistência | Tratamento de erros, snapshot único e funções auxiliares de ID, HTML e busca |
| `assets/cadastro.js` | Cadastro, pesquisa e remoção | Validação de idade, exclusão por ID, busca normalizada e renderização protegida |
| `assets/triagem.js` | Seleção, validação e classificação | Limpeza na troca, números decimais, validação, bloqueio de duplicidade e identificação completa |
| `assets/fila.js` | Exibição e chamada | Ordenação por prioridade e cancelamento em caso de falha ao salvar |
| `assets/app.js` | Integração, navegação e indicadores | Callback centralizado para seleção e mensagens persistentes de erro |
| `tests/regressao.cjs` | Verificações automatizadas | Adicionado, com 14 testes e simulações de DOM/armazenamento |
| `package.json` | Metadados e comando npm test | Adicionado sem dependências externas |
| `.gitignore` | Exclusão de itens locais do Git | Ignora dependências, arquivos de ambiente, logs e configurações locais de editor |
| `.gitattributes` | Normalização de arquivos de texto | Define finais de linha LF para os formatos usados |
| `.nojekyll` | Compatibilidade com publicação estática | Arquivo vazio para dispensar processamento Jekyll |
| `CHANGELOG.md` | Histórico das correções | Lista BUG01 a BUG13 |

## Decisões que precisam ser entendidas

- **Identidade versus posição:** filtros mudam posições de lista; IDs identificam o paciente independentemente da posição.
- **Validação antes do cálculo:** texto vazio não deve virar zero, e texto parcialmente numérico não deve ser aceito como medida válida.
- **Preservação de dados:** erro de leitura bloqueia gravações e conserva o conteúdo original. Não foi implementado assistente automático de recuperação.
- **Gravação conjunta:** pacientes e triagens são serializados em uma chave única. Isso evita gravar uma lista e falhar antes de gravar a outra; não cria banco de dados ou transações entre usuários.
- **Desfazer em memória:** se save() retorna false, o controlador restaura a lista anterior e não informa sucesso.
- **HTML protegido:** a abordagem aplicada foi codificar as interpolações com escapeHTML(), mantendo os templates existentes. Não foi feita uma reescrita completa com createElement()/textContent.
- **Regras clínicas preservadas:** não foram criados novos critérios clínicos. O foco foi corrigir a interpretação dos dados e o comportamento previsto pelas regras do código.
- **Mensagens legíveis:** foi removido o apagamento automático após 2,8 segundos. As mensagens são substituídas por novas ações ou limpas nos pontos definidos no código; não há garantia de que qualquer clique limpe todas as mensagens.
- **Compatibilidade:** funções auxiliares são carregadas por state.js antes dos controladores. Todos os patches precisam estar aplicados antes de abrir a aplicação.


## Registro detalhado dos problemas e dos patches

Documento técnico da versão corrigida, com 13 problemas, causas, soluções e evidências de verificação. Os IDs BUG01–BUG13 são os mesmos do relatório anterior; a ordem abaixo segue a prioridade de correção.

## Conteúdo do pacote

- `README.md`: este documento, com os patches completos incorporados.
- `patches/correcao-completa.patch`: patch consolidado para os arquivos originais.
- `patches/0001-*.patch` a `patches/0007-*.patch`: patches por arquivo, incluindo os testes e o comando npm test.

Os patches foram gerados a partir dos arquivos originais enviados e dos arquivos da entrega corrigida. Não incluem dados de pacientes, credenciais ou configuração do endereço privado publicado.

## Prioridades e identificação

| Ordem | ID | Severidade | Problema |
| --- | --- | --- | --- |
| 1 | BUG01 | Crítica | Fila ignora a classificação de risco |
| 2 | BUG02 | Crítica | Exclusão remove paciente diferente após busca |
| 3 | BUG06 | Alta | Troca de paciente mantém dados da triagem anterior |
| 4 | BUG03 | Alta | Pressão arterial não reconhece o formato exibido |
| 5 | BUG04 | Alta | Temperatura perde a parte decimal |
| 6 | BUG07 | Alta | Dados digitados são interpretados como HTML |
| 7 | BUG08 | Alta | Idade e sinais vitais aceitam conteúdo inválido |
| 8 | BUG05 | Alta | Sugestão vermelha com formulário vazio |
| 9 | BUG09 | Alta | Dados corrompidos impedem a inicialização |
| 10 | BUG13 | Alta | Cadastro falha sem crypto.randomUUID |
| 11 | BUG10 | Média | Paciente pode aparecer várias vezes na fila |
| 12 | BUG12 | Média | Seleção de triagem identifica apenas pelo primeiro nome |
| 13 | BUG11 | Média | Busca ignora sobrenome e diferencia maiúsculas |

## Problemas e soluções

Em cada item, “comportamento original” descreve a falha anterior; a solução descreve a alteração implementada.

### 1. BUG01 — Fila ignora a classificação de risco

**Severidade:** Crítica. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Fila de atendimento — fila.js, callNext e render.

**Comportamento original:** Cadastre uma triagem verde e depois uma vermelha. Ao chamar o próximo, a verde é retirada primeiro, pois callNext usa shift(). Resultado reproduzido em teste de função.

**Comportamento esperado:** Chamar pela ordem de prioridade definida no sistema; em empate, respeitar a ordem de entrada.

**Solução aplicada:** Implementada orderedTriages(), utilizada na exibição e na chamada, com prioridade vermelho, amarelo, verde e branco. Novos registros recebem createdAt completo; registros antigos sem data mantêm a ordem relativa nos empates.

**Verificação:** Teste automatizado de prioridade e desempate aprovado; paciente vermelho escolhido antes do verde.

### 2. BUG02 — Exclusão remove paciente diferente após busca

**Severidade:** Crítica. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Cadastro — cadastro.js, render e handleListClick.

**Comportamento original:** Cadastre Ana e Bruno. Busque Bruno e clique em Remover. O índice 0 da lista filtrada é aplicado à lista completa e exclui Ana. Erro reproduzido na função de exclusão.

**Comportamento esperado:** Remover somente o paciente escolhido, inclusive com pesquisa ativa.

**Solução aplicada:** O botão Remover carrega o ID do paciente. A exclusão procura esse identificador na lista completa e remove somente as triagens associadas; em falha de gravação, o estado anterior é restaurado.

**Verificação:** Teste automatizado de exclusão por ID com filtro aprovado; Ana preservada e Bruno removido.

### 3. BUG06 — Troca de paciente mantém dados da triagem anterior

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem — triagem.js, selectPatient; cadastro.js, seleção pelo botão Triar.

**Comportamento original:** Preencha sinais vitais, sintomas e classificação para um paciente e selecione outro. selectPatient atualiza apenas o ID e o resumo; os demais campos permanecem preenchidos. Constatação por leitura do código.

**Comportamento esperado:** Evitar que dados clínicos de um paciente sejam atribuídos a outro durante a troca.

**Solução aplicada:** Troca centralizada em selectPatient(), chamada tanto pelo seletor quanto pelo botão Triar. A mudança de ID limpa formulário, classificação, sugestão e mensagem.

**Verificação:** Teste automatizado: ao mudar o paciente, sintomas anteriores são apagados e a sugestão é limpa.

### 4. BUG03 — Pressão arterial não reconhece o formato exibido

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem — triagem.js, parseBloodPressure e calculateSuggestion.

**Comportamento original:** Informe 180/110 com temperatura 36.5, oxigenação 98, frequência 80 e cefaleia. A sugestão é verde: o código divide por hífen, mas o campo orienta usar barra. Reproduzido em teste.

**Comportamento esperado:** Interpretar corretamente o formato sistólica/diastólica e aplicar as regras já definidas no código.

**Solução aplicada:** parseBloodPressure() valida números separados por barra e aceita espaços. readVitals() rejeita formato inválido, valores não positivos e sistólica menor ou igual à diastólica.

**Verificação:** Teste automatizado: 180/110 reconhecido e classificado em vermelho pelas regras existentes.

### 5. BUG04 — Temperatura perde a parte decimal

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem — triagem.js, calculateSuggestion.

**Comportamento original:** Com os outros sinais normais, 38.5 é convertido em 38 e produz amarelo; 37.8 vira 37 e produz verde. Ambos os resultados foram reproduzidos em testes das funções.

**Comportamento esperado:** Preservar as casas decimais e comparar o valor com os limites presentes no código.

**Solução aplicada:** Conversão decimal estrita aceita ponto ou vírgula e preserva a parte fracionária. Entradas com texto adicional são rejeitadas.

**Verificação:** Testes automatizados: 38.5 e 38,5 retornam vermelho; 37.8 retorna amarelo, pelas regras existentes.

### 6. BUG07 — Dados digitados são interpretados como HTML

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** cadastro.js, render; triagem.js, renderPatientSummary e renderVitalPreview; fila.js, render.

**Comportamento original:** Nome, endereço, sintomas e sinais vitais são concatenados em innerHTML sem escape. Conteúdo com marcação pode alterar a interface e permitir XSS. Identificado na revisão; não foi executada carga maliciosa.

**Comportamento esperado:** Exibir entradas do usuário como texto, sem interpretar HTML ou executar eventos.

**Solução aplicada:** escapeHTML() codifica &, <, >, aspas simples e duplas nas interpolações dos blocos HTML de cadastro, resumo, sinais e fila. Os atributos utilizados permanecem entre aspas. Esta proteção é específica para os contextos HTML empregados; não é sanitização universal de URLs, scripts ou CSS.

**Verificação:** Testes automatizados de codificação de caracteres e exibição literal de marcação aprovados; não foi feita auditoria completa de segurança.

### 7. BUG08 — Idade e sinais vitais aceitam conteúdo inválido

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Cadastro e triagem — index.html, cadastro.js e triagem.js.

**Comportamento original:** Os campos são de texto e a gravação verifica principalmente se estão preenchidos. Não há validação numérica suficiente para rejeitar idade negativa, texto em sinais vitais ou oxigenação acima de 100. Evidência por revisão.

**Comportamento esperado:** Rejeitar entradas inválidas antes de salvar ou sugerir classificação, com mensagem específica.

**Solução aplicada:** Idade deve ser inteira entre 0 e 150; pressão deve ter formato válido; temperatura deve ser numérica e positiva; oxigenação deve ficar entre 0 e 100; frequência cardíaca é opcional, mas deve ser inteira e positiva quando informada; sintomas não podem conter somente espaços. Não foram definidos limites clínicos adicionais.

**Verificação:** Testes automatizados de idade negativa, texto e oxigenação fora da faixa aprovados.

### 8. BUG05 — Sugestão vermelha com formulário vazio

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem — triagem.js, suggestRisk e calculateSuggestion.

**Comportamento original:** Clique em Sugerir classificação sem preencher sinais vitais. Number converte a oxigenação vazia em zero e a função retorna vermelho. Resultado reproduzido em teste.

**Comportamento esperado:** Informar que faltam dados e não selecionar uma classificação sem entradas válidas.

**Solução aplicada:** readVitals() valida sinais e sintomas antes da sugestão. Em falha, é exibida mensagem, o cálculo é interrompido e a classificação selecionada é limpa.

**Verificação:** Teste automatizado: oxigenação vazia impede o cálculo e retorna null.

### 9. BUG09 — Dados corrompidos impedem a inicialização

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Persistência — state.js, constructor e save.

**Comportamento original:** Se o localStorage contém JSON inválido, o construtor AppState lança SyntaxError e a aplicação não inicializa. Exceção reproduzida em teste com conteúdo inválido.

**Comportamento esperado:** Abrir a aplicação com uma mensagem recuperável e preservar os dados disponíveis para recuperação.

**Solução aplicada:** Leitura e análise de JSON protegidas por try/catch e validação da estrutura essencial. Dados inválidos são preservados e novas gravações bloqueadas. Pacientes e triagens são gravados juntos em snapshot único. Falha ao salvar retorna false e os controladores desfazem a alteração em memória. A recuperação dos dados originais é manual.

**Verificação:** Testes automatizados de JSON corrompido, bloqueio de gravação e cancelamento de chamada quando save falha aprovados.

### 10. BUG13 — Cadastro falha sem crypto.randomUUID

**Severidade:** Alta. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** state.js, cadastro.js e triagem.js.

**Comportamento original:** No teste em HTTP local, o cadastro lançou TypeError: crypto.randomUUID is not a function.

**Comportamento esperado:** Gerar IDs mesmo quando randomUUID não estiver disponível, desde que o navegador ofereça getRandomValues.

**Solução aplicada:** createRecordId() utiliza crypto.randomUUID() quando disponível. Como alternativa, gera bytes com crypto.getRandomValues(), define versão/variante e monta um UUID v4. Cadastro e triagem passaram a usar essa função.

**Verificação:** Após a correção, fluxo no navegador concluído: cadastro fictício, sugestão com 38,5, registro e exibição na fila.

### 11. BUG10 — Paciente pode aparecer várias vezes na fila

**Severidade:** Média. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem e fila — triagem.js, handleSubmit e renderPatientOptions.

**Comportamento original:** handleSubmit adiciona uma nova triagem por push sem verificar se o paciente já aguarda atendimento. A seleção continua incluindo todos os pacientes cadastrados. Constatação por revisão.

**Comportamento esperado:** Evitar duplicidade de entradas ativas para o mesmo atendimento, distinguindo reavaliação de uma nova entrada.

**Solução aplicada:** Antes da inclusão, some() verifica se já existe uma triagem ativa com o mesmo patientId. Duplicidade é bloqueada com mensagem. Não foi implementado fluxo de reavaliação.

**Verificação:** Teste automatizado: segunda triagem ativa para o mesmo paciente não aumenta a fila.

### 12. BUG12 — Seleção de triagem identifica apenas pelo primeiro nome

**Severidade:** Média. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Triagem — triagem.js, renderPatientOptions.

**Comportamento original:** renderPatientOptions gera cada opção apenas com patient.firstName. Dois pacientes chamados Ana aparecem com o mesmo texto na seleção. Constatação por leitura do código.

**Comportamento esperado:** Distinguir pacientes homônimos antes do preenchimento da triagem.

**Solução aplicada:** As opções mostram nome completo e ID único, mantendo o ID como valor da seleção.

**Verificação:** Teste automatizado e conferência visual da opção com nome completo e ID.

### 13. BUG11 — Busca ignora sobrenome e diferencia maiúsculas

**Severidade:** Média. **Status:** corrigido e verificado no escopo descrito.

**Área afetada:** Recepção — cadastro.js, render.

**Comportamento original:** A busca usa apenas patient.firstName.includes(search). Para Ana Silva, pesquisar Silva não encontra o cadastro; pesquisar ana também pode não encontrar Ana. Evidência direta na função.

**Comportamento esperado:** Localizar pelo nome completo, sem diferenciar letras maiúsculas e minúsculas.

**Solução aplicada:** Pesquisa usa nome completo com normalização NFD, remoção de acentos, conversão para minúsculas e remoção de espaços nas extremidades.

**Verificação:** Teste automatizado: SILVA encontra Ana Sílva.

## Aplicar o patch consolidado

O patch exige a versão original enviada, com `index.html` na raiz e JavaScript em `assets/`. Se você já utiliza o ZIP corrigido, não reaplique os patches.

1. Preserve uma cópia do projeto original ou faça um commit antes da alteração.
2. Extraia este pacote e copie sua pasta `patches` para a raiz do projeto original.
3. Na raiz do projeto, execute:

```bash
git apply --check patches/correcao-completa.patch
git apply patches/correcao-completa.patch
```

O primeiro comando apenas verifica a compatibilidade. O segundo aplica as mudanças. Não são necessários commits existentes para `git apply` operar, mas o Git precisa estar instalado.

Se aparecer conflito, pare e compare a versão local com a original: não force a aplicação nem execute os patches duas vezes. Os patches adicionam `tests/regressao.cjs` e `package.json`; arquivos existentes com esses nomes precisam ser comparados antes.

### Alternativa com patches por arquivo

Use esta alternativa somente se não aplicou o consolidado. Todos os patches devem ser aplicados antes de executar o sistema, pois há dependências entre os arquivos.

```bash
git apply patches/0001-state.patch
git apply patches/0002-cadastro.patch
git apply patches/0003-triagem.patch
git apply patches/0004-fila.patch
git apply patches/0005-app.patch
git apply patches/0006-regressao.patch
git apply patches/0007-package.patch
```

### Reverter o patch

Somente se os arquivos ainda estiverem compatíveis com a versão recém-corrigida e não houver alterações posteriores:

```bash
git apply --reverse --check patches/correcao-completa.patch
git apply --reverse patches/correcao-completa.patch
```

A reversão também remove os arquivos de teste e package.json criados pelo patch. Ela não reverte dados do navegador. Faça uma cópia antes de reverter.

## Testar a versão corrigida

Com Node.js 18 ou superior, na raiz do projeto:

```bash
npm test
```

Alternativa sem npm:

```bash
node tests/regressao.cjs
```

Não há dependências para instalar. Resultado esperado: **14 testes aprovados**. Os testes usam funções reais com simulação de DOM e armazenamento; não cobrem todos os comportamentos possíveis de navegador.

Para abrir a aplicação, mantenha `index.html` ao lado da pasta `assets` e abra-o no navegador. Também é possível servir a pasta com um servidor estático local.

## Persistência e compatibilidade

- Novas gravações usam `ps_escola_pacientes_snapshot_v2`, contendo pacientes e triagens juntos.
- Sem snapshot, a versão tenta ler as chaves antigas `ps_escola_pacientes` e `ps_escola_triagens`.
- As chaves antigas permanecem preservadas, mas deixam de ser atualizadas após a adoção do snapshot. Uma versão antiga do sistema não lê o snapshot novo.
- A recuperação de dados inválidos é manual. A implementação bloqueia gravações para não sobrescrever o conteúdo original; não oferece assistente de recuperação.
- O armazenamento é local ao navegador e à origem do site; não há sincronização entre dispositivos ou abas.

## Limites da correção

As regras existentes de sugestão de classificação foram preservadas. Os testes verificam seu funcionamento no código, sem validar um protocolo clínico. Utilize dados fictícios no contexto educacional.

Continuam fora desta entrega: backend, prontuário/histórico, autenticação própria, perfis, reavaliação, sincronização entre usuários e auditoria completa de segurança. A chamada remove a triagem da fila ativa. Esses itens não foram apresentados como funcionalidades implementadas.

## Patches completos por arquivo

Linhas iniciadas com `-` pertencem à versão original; linhas com `+` são adições da versão corrigida. Linhas sem esses sinais dão contexto. Os arquivos `.patch` contêm as mesmas diferenças abaixo, sem as cercas Markdown.

### 0001-state.patch

```diff
--- a/assets/state.js
+++ b/assets/state.js
@@ -1,25 +1,47 @@
+function createRecordId() {
+  if (typeof crypto.randomUUID === "function") return crypto.randomUUID();
+  const bytes = crypto.getRandomValues(new Uint8Array(16));
+  bytes[6] = (bytes[6] & 15) | 64; bytes[8] = (bytes[8] & 63) | 128;
+  const hex = Array.from(bytes, b => b.toString(16).padStart(2, "0")).join("");
+  return `${hex.slice(0,8)}-${hex.slice(8,12)}-${hex.slice(12,16)}-${hex.slice(16,20)}-${hex.slice(20)}`;
+}
+// Escape all user-controlled values before HTML interpolation.
+function escapeHTML(value) {
+  return String(value ?? "").replace(/[&<>"']/g, c => ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
+}
+function normalizeSearch(value) { return String(value).normalize("NFD").replace(/[\u0300-\u036f]/g, "").toLowerCase().trim(); }
 class AppState {
   constructor(patientStorageKey, triageStorageKey) {
     this.patientStorageKey = patientStorageKey;
     this.triageStorageKey = triageStorageKey;
-    this.patients = JSON.parse(localStorage.getItem(patientStorageKey) || "[]");
-    this.triages = JSON.parse(localStorage.getItem(triageStorageKey) || "[]");
+    this.snapshotKey = patientStorageKey + "_snapshot_v2";
     this.activeScreen = "cadastro";
     this.selectedPatientId = "";
+    this.patients = []; this.triages = []; this.storageError = ""; this.storageBlocked = false;
+    try {
+      const snapshot = localStorage.getItem(this.snapshotKey);
+      const data = snapshot ? JSON.parse(snapshot) : {
+        patients: JSON.parse(localStorage.getItem(patientStorageKey) || "[]"),
+        triages: JSON.parse(localStorage.getItem(triageStorageKey) || "[]")
+      };
+      if (!Array.isArray(data.patients) || !Array.isArray(data.triages) ||
+          !data.patients.every(x => x && typeof x.id === "string" && typeof x.firstName === "string" && typeof x.lastName === "string") ||
+          !data.triages.every(x => x && typeof x.id === "string" && typeof x.patientId === "string" && ["branco","verde","amarelo","vermelho"].includes(x.classification))) throw Error("Formato inválido");
+      this.patients = data.patients; this.triages = data.triages;
+    } catch (error) {
+      this.storageBlocked = true;
+      this.storageError = "Não foi possível carregar os dados salvos. Os originais foram preservados. Recupere o armazenamento antes de cadastrar ou alterar registros.";
+    }
   }
-
   save() {
-    localStorage.setItem(this.patientStorageKey, JSON.stringify(this.patients));
-    localStorage.setItem(this.triageStorageKey, JSON.stringify(this.triages));
+    if (this.storageBlocked) { window.alert(this.storageError); return false; }
+    try {
+      // One atomic write; original legacy keys remain available for recovery.
+      localStorage.setItem(this.snapshotKey, JSON.stringify({patients:this.patients,triages:this.triages}));
+      return true;
+    } catch (error) { window.alert("Não foi possível salvar. A alteração foi cancelada; verifique o armazenamento do navegador."); return false; }
   }
-
-  getPatientName(patient) {
-    return `${patient.firstName} ${patient.lastName}`;
-  }
-
-  getSelectedPatient() {
-    return this.patients.find((patient) => patient.id === this.selectedPatientId);
-  }
+  getPatientName(patient) { return `${patient.firstName} ${patient.lastName}`; }
+  getSelectedPatient() { return this.patients.find(patient => patient.id === this.selectedPatientId); }
 }
-
 window.AppState = AppState;
```

### 0002-cadastro.patch

```diff
--- a/assets/cadastro.js
+++ b/assets/cadastro.js
@@ -16,7 +16,7 @@
     event.preventDefault();
     const data = new FormData(this.dom.patientForm);
     const patient = {
-      id: crypto.randomUUID(),
+      id: createRecordId(),
       firstName: data.get("firstName").trim(),
       lastName: data.get("lastName").trim(),
       age: data.get("age"),
@@ -32,9 +32,13 @@
       this.callbacks.showMessage(this.dom.patientMessage, "Preencha os campos obrigatórios.");
       return;
     }
+    if (!/^\d+$/.test(String(patient.age).trim()) || !Number.isInteger(Number(patient.age)) || Number(patient.age) < 0 || Number(patient.age) > 150) {
+      this.callbacks.showMessage(this.dom.patientMessage, "Informe uma idade inteira entre 0 e 150."); return;
+    }
     this.state.patients.push(patient);
+    if (!this.state.save()) { this.state.patients.pop(); return; }
+    this.callbacks.selectPatient(patient.id);
     this.state.selectedPatientId = patient.id;
-    this.state.save();
     this.reset();
     this.callbacks.showMessage(this.dom.patientMessage, "Paciente cadastrado.");
     this.callbacks.renderAll();
@@ -44,17 +48,19 @@
     const selectButton = event.target.closest("[data-select-patient]");
     const deleteButton = event.target.closest("[data-delete-patient]");
     if (selectButton) {
-      this.state.selectedPatientId = selectButton.dataset.selectPatient;
+      this.callbacks.selectPatient(selectButton.dataset.selectPatient);
       this.callbacks.setScreen("triagem");
       this.callbacks.renderAll();
     }
     if (deleteButton) {
-      const index = Number(deleteButton.dataset.deletePatient);
-      const removed = this.state.patients.splice(index, 1)[0];
-      if (removed) {
-        this.state.triages = this.state.triages.filter((triage) => triage.patientId !== removed.id);
-      }
-      this.state.save();
+      const id = deleteButton.dataset.deletePatient;
+      if (!this.state.patients.some(patient => patient.id === id)) return;
+      const previousPatients = this.state.patients;
+      const previousTriages = this.state.triages;
+      this.state.patients = previousPatients.filter(patient => patient.id !== id);
+      this.state.triages = previousTriages.filter(triage => triage.patientId !== id);
+      if (!this.state.save()) { this.state.patients = previousPatients; this.state.triages = previousTriages; return; }
+      if (this.state.selectedPatientId === id) this.callbacks.selectPatient("");
       this.callbacks.renderAll();
     }
   }
@@ -64,8 +70,8 @@
   }
 
   render() {
-    const search = this.dom.patientSearch.value;
-    const patients = this.state.patients.filter((patient) => patient.firstName.includes(search));
+    const search = normalizeSearch(this.dom.patientSearch.value);
+    const patients = this.state.patients.filter((patient) => normalizeSearch(this.state.getPatientName(patient)).includes(search));
     if (patients.length === 0) {
       this.dom.patientList.innerHTML = `<div class="empty-state">Nenhum paciente na recepção.</div>`;
       return;
@@ -74,16 +80,16 @@
       <article class="patient-card">
         <header>
           <div>
-            <strong>${this.state.getPatientName(patient)}</strong>
-            <small>${patient.age} anos · ${patient.gender}</small>
+            <strong>${escapeHTML(this.state.getPatientName(patient))}</strong>
+            <small>${escapeHTML(patient.age)} anos · ${escapeHTML(patient.gender)}</small>
           </div>
-          <span class="tag">${patient.insurancePlan}</span>
+          <span class="tag">${escapeHTML(patient.insurancePlan)}</span>
         </header>
-        <div class="meta-line">${patient.address}</div>
-        <div class="meta-line">${patient.insuranceName} · ${patient.insuranceCard}</div>
+        <div class="meta-line">${escapeHTML(patient.address)}</div>
+        <div class="meta-line">${escapeHTML(patient.insuranceName)} · ${escapeHTML(patient.insuranceCard)}</div>
         <div class="patient-actions">
-          <button class="small-button" data-select-patient="${patient.id}" type="button">Triar</button>
-          <button class="small-button danger-button" data-delete-patient="${index}" type="button">Remover</button>
+          <button class="small-button" data-select-patient="${escapeHTML(patient.id)}" type="button">Triar</button>
+          <button class="small-button danger-button" data-delete-patient="${escapeHTML(patient.id)}" type="button">Remover</button>
         </div>
       </article>
     `).join("");
```

### 0003-triagem.patch

```diff
--- a/assets/triagem.js
+++ b/assets/triagem.js
@@ -16,29 +16,43 @@
     this.dom.clearTriageForm.addEventListener("click", () => this.reset());
   }
 
-  selectPatient() {
-    this.state.selectedPatientId = this.dom.triagePatient.value;
-    this.renderPatientSummary();
+  selectPatient(id = this.dom.triagePatient.value) {
+    if (id !== this.state.selectedPatientId) {
+      this.dom.triageForm.reset();
+      this.dom.riskSuggestion.textContent = "Sem sugestão";
+      this.dom.triageMessage.textContent = "";
+    }
+    this.state.selectedPatientId = this.state.patients.some(p => p.id === id) ? id : "";
+    this.renderPatientOptions(); this.renderPatientSummary(); this.renderVitalPreview();
   }
-
   parseBloodPressure(value) {
-    const parts = value.split("-");
-    return {
-      systolic: Number(parts[0]),
-      diastolic: Number(parts[1])
-    };
+    const parts = String(value).trim().match(/^(\d{1,3})\s*\/\s*(\d{1,3})$/);
+    if (!parts) return {systolic:NaN, diastolic:NaN};
+    return {systolic:Number(parts[1]), diastolic:Number(parts[2])};
   }
-
+  readVitals() {
+    const read = id => document.getElementById(id).value.trim();
+    const decimal = value => /^(?:\d+(?:[.,]\d+)?|[.,]\d+)$/.test(value) ? Number(value.replace(",", ".")) : NaN;
+    const pressure = this.parseBloodPressure(read("bloodPressure"));
+    const temperature = decimal(read("temperature")), oxygen = decimal(read("oxygen"));
+    const heartRate = read("heartRate") ? decimal(read("heartRate")) : null;
+    const symptoms = read("symptoms");
+    let error = "";
+    if (!Number.isFinite(pressure.systolic) || !Number.isFinite(pressure.diastolic) || pressure.systolic <= 0 || pressure.diastolic <= 0 || pressure.systolic <= pressure.diastolic) error = "Informe pressão válida no formato 120/80, com sistólica maior que diastólica.";
+    else if (!Number.isFinite(temperature) || temperature <= 0) error = "Informe temperatura numérica positiva, com ponto ou vírgula.";
+    else if (!Number.isFinite(oxygen) || oxygen < 0 || oxygen > 100) error = "Informe oxigenação entre 0 e 100%.";
+    else if (heartRate !== null && (!Number.isInteger(heartRate) || heartRate <= 0)) error = "Informe frequência cardíaca inteira positiva ou deixe em branco.";
+    else if (!symptoms) error = "Informe sintomas e observações.";
+    return {pressure,temperature,oxygen,heartRate,symptoms,error};
+  }
   normalizeClassification(value) {
     return value || "branco";
   }
 
   calculateSuggestion() {
-    const pressure = this.parseBloodPressure(document.getElementById("bloodPressure").value);
-    const temperature = parseInt(document.getElementById("temperature").value, 10);
-    const oxygen = Number(document.getElementById("oxygen").value);
-    const symptoms = document.getElementById("symptoms").value.toLowerCase();
-    const heartRate = Number(document.getElementById("heartRate").value);
+    const {pressure, temperature, oxygen, symptoms: rawSymptoms, heartRate, error} = this.readVitals();
+    if (error) return null;
+    const symptoms = rawSymptoms.toLowerCase();
     let risk = "verde";
     if (oxygen < 90 || pressure.systolic >= 180 || temperature >= 38.5 || symptoms.includes("dor no peito") || symptoms.includes("convuls")) {
       risk = "vermelho";
@@ -51,6 +65,13 @@
   }
 
   suggestRisk() {
+    const vitals = this.readVitals();
+    if (vitals.error) {
+      this.callbacks.showMessage(this.dom.triageMessage, vitals.error);
+      this.dom.riskSuggestion.textContent = "Dados inválidos";
+      document.querySelectorAll('[name="classification"]').forEach(input => input.checked = false);
+      return;
+    }
     const risk = this.calculateSuggestion();
     const riskInput = document.querySelector(`[name="classification"][value="${risk}"]`);
     if (riskInput) {
@@ -62,13 +83,20 @@
   handleSubmit(event) {
     event.preventDefault();
     const data = new FormData(this.dom.triageForm);
-    const classification = this.normalizeClassification(data.get("classification"));
+    const classification = data.get("classification");
+    const vitals = this.readVitals();
+    if (vitals.error) { this.callbacks.showMessage(this.dom.triageMessage, vitals.error); return; }
+    if (!Object.hasOwn(this.priorityLabels, classification)) { this.callbacks.showMessage(this.dom.triageMessage, "Selecione uma classificação."); return; }
+    if (!this.state.getSelectedPatient()) { this.callbacks.showMessage(this.dom.triageMessage, "Selecione um paciente válido."); return; }
+    if (this.state.triages.some(item => item.patientId === this.state.selectedPatientId)) {
+      this.callbacks.showMessage(this.dom.triageMessage, "Este paciente já está na fila. Não é possível duplicar a triagem."); return;
+    }
     if (!this.state.selectedPatientId || !data.get("bloodPressure") || !data.get("temperature") || !data.get("oxygen") || !data.get("symptoms")) {
       this.callbacks.showMessage(this.dom.triageMessage, "Preencha todos os dados da triagem.");
       return;
     }
     this.state.triages.push({
-      id: crypto.randomUUID(),
+      id: createRecordId(),
       patientId: this.state.selectedPatientId,
       bloodPressure: data.get("bloodPressure"),
       temperature: data.get("temperature"),
@@ -76,12 +104,13 @@
       heartRate: data.get("heartRate"),
       symptoms: data.get("symptoms").trim(),
       classification,
+      createdAt: new Date().toISOString(),
       time: new Date().toLocaleTimeString("pt-BR", {
         hour: "2-digit",
         minute: "2-digit"
       })
     });
-    this.state.save();
+    if (!this.state.save()) { this.state.triages.pop(); return; }
     this.reset();
     this.callbacks.showMessage(this.dom.triageMessage, "Triagem registrada.");
     this.callbacks.renderAll();
@@ -102,7 +131,7 @@
       return;
     }
     this.dom.triagePatient.innerHTML = `<option value="">Selecionar paciente</option>` + this.state.patients.map((patient) => `
-      <option value="${patient.id}">${patient.firstName}</option>
+      <option value="${escapeHTML(patient.id)}">${escapeHTML(this.state.getPatientName(patient))} — ${escapeHTML(patient.id)}</option>
     `).join("");
     this.dom.triagePatient.value = this.state.selectedPatientId;
   }
@@ -114,11 +143,11 @@
       return;
     }
     this.dom.patientSummary.innerHTML = `
-      <div class="summary-row"><span>Nome</span><strong>${this.state.getPatientName(patient)}</strong></div>
-      <div class="summary-row"><span>Idade</span><strong>${patient.age}</strong></div>
-      <div class="summary-row"><span>Gênero</span><strong>${patient.gender}</strong></div>
-      <div class="summary-row"><span>Convênio</span><strong>${patient.insuranceName}</strong></div>
-      <div class="summary-row"><span>Plano</span><strong>${patient.insurancePlan}</strong></div>
+      <div class="summary-row"><span>Nome</span><strong>${escapeHTML(this.state.getPatientName(patient))}</strong></div>
+      <div class="summary-row"><span>Idade</span><strong>${escapeHTML(patient.age)}</strong></div>
+      <div class="summary-row"><span>Gênero</span><strong>${escapeHTML(patient.gender)}</strong></div>
+      <div class="summary-row"><span>Convênio</span><strong>${escapeHTML(patient.insuranceName)}</strong></div>
+      <div class="summary-row"><span>Plano</span><strong>${escapeHTML(patient.insurancePlan)}</strong></div>
     `;
   }
 
@@ -129,9 +158,9 @@
     const oxygenNumber = Number(oxygen);
     const oxygenClass = oxygenNumber < 90 ? "alert" : "";
     this.dom.vitalPreview.innerHTML = `
-      <div class="vital-row"><span>Pressão</span><strong>${pressure}</strong></div>
-      <div class="vital-row"><span>Temperatura</span><strong>${temperature} °C</strong></div>
-      <div class="vital-row"><span>Oxigenação</span><strong class="${oxygenClass}">${oxygen}%</strong></div>
+      <div class="vital-row"><span>Pressão</span><strong>${escapeHTML(pressure)}</strong></div>
+      <div class="vital-row"><span>Temperatura</span><strong>${escapeHTML(temperature)} °C</strong></div>
+      <div class="vital-row"><span>Oxigenação</span><strong class="${escapeHTML(oxygenClass)}">${escapeHTML(oxygen)}%</strong></div>
     `;
   }
 }
```

### 0004-fila.patch

```diff
--- a/assets/fila.js
+++ b/assets/fila.js
@@ -11,14 +11,25 @@
     this.dom.callNext.addEventListener("click", () => this.callNext());
   }
 
+  orderedTriages() {
+    const priorities = {vermelho:0, amarelo:1, verde:2, branco:3};
+    return this.state.triages.map((item,index) => ({item,index})).sort((a,b) => {
+      const priority = (priorities[a.item.classification] ?? 4) - (priorities[b.item.classification] ?? 4);
+      if (priority) return priority;
+      const ta = Date.parse(a.item.createdAt), tb = Date.parse(b.item.createdAt);
+      return Number.isFinite(ta) && Number.isFinite(tb) ? ta-tb || a.index-b.index : a.index-b.index;
+    }).map(entry => entry.item);
+  }
   callNext() {
     if (this.state.triages.length === 0) {
       return;
     }
-    const called = this.state.triages.shift();
+    const called = this.orderedTriages()[0];
+    const previous = this.state.triages;
+    this.state.triages = previous.filter(item => item.id !== called.id);
+    if (!this.state.save()) { this.state.triages = previous; return; }
     const patient = this.state.patients.find((item) => item.id === called.patientId);
     window.alert(`${patient ? this.state.getPatientName(patient) : "Paciente"} encaminhado para atendimento.`);
-    this.state.save();
     this.callbacks.renderAll();
   }
 
@@ -27,26 +38,26 @@
       this.dom.queueBoard.innerHTML = `<div class="empty-state">Nenhum paciente aguardando atendimento.</div>`;
       return;
     }
-    this.dom.queueBoard.innerHTML = this.state.triages.map((triage) => {
+    this.dom.queueBoard.innerHTML = this.orderedTriages().map((triage) => {
       const patient = this.state.patients.find((item) => item.id === triage.patientId);
       const patientName = patient ? this.state.getPatientName(patient) : "Paciente não encontrado";
       return `
         <article class="queue-card">
-          <div class="risk-bar" style="background:${this.priorityColor[triage.classification]}"></div>
+          <div class="risk-bar" style="background:${escapeHTML(this.priorityColor[triage.classification])}"></div>
           <div class="queue-body">
             <header>
               <div>
-                <strong>${patientName}</strong>
-                <small>Entrada ${triage.time}</small>
+                <strong>${escapeHTML(patientName)}</strong>
+                <small>Entrada ${escapeHTML(triage.time)}</small>
               </div>
-              <span class="tag ${triage.classification}">${this.priorityLabels[triage.classification]}</span>
+              <span class="tag ${escapeHTML(triage.classification)}">${escapeHTML(this.priorityLabels[triage.classification])}</span>
             </header>
             <div class="tag-row">
-              <span class="tag">PA ${triage.bloodPressure}</span>
-              <span class="tag">Temp ${triage.temperature} °C</span>
-              <span class="tag">SpO2 ${triage.oxygen}%</span>
+              <span class="tag">PA ${escapeHTML(triage.bloodPressure)}</span>
+              <span class="tag">Temp ${escapeHTML(triage.temperature)} °C</span>
+              <span class="tag">SpO2 ${escapeHTML(triage.oxygen)}%</span>
             </div>
-            <p class="meta-line">${triage.symptoms}</p>
+            <p class="meta-line">${escapeHTML(triage.symptoms)}</p>
           </div>
         </article>
       `;
```

### 0005-app.patch

```diff
--- a/assets/app.js
+++ b/assets/app.js
@@ -80,6 +80,7 @@
     this.registration = new PatientRegistration(this.state, this.dom, {
       showMessage: (target, message) => this.showMessage(target, message),
       renderAll: () => this.renderAll(),
+      selectPatient: (id) => this.triage.selectPatient(id),
       setScreen: (screen) => this.navigation.setScreen(screen)
     });
     this.triage = new TriageController(this.state, this.dom, {
@@ -125,13 +126,12 @@
     this.triage.init();
     this.queue.init();
     this.renderAll();
+    if (this.state.storageError) this.showMessage(this.dom.patientMessage, this.state.storageError);
   }
 
   showMessage(target, message) {
     target.textContent = message;
-    window.setTimeout(() => {
-      target.textContent = "";
-    }, 2800);
+    // Keep validation messages visible until the next action.
   }
 
   renderAll() {
```

### 0006-regressao.patch

```diff
--- /dev/null
+++ b/tests/regressao.cjs
@@ -0,0 +1,26 @@
+const fs=require('fs'),vm=require('vm'),assert=require('assert');
+const root=require('path').join(__dirname,'..','assets/');
+const fields=Object.fromEntries(Object.entries({bloodPressure:'120/80',temperature:'36.5',oxygen:'98',heartRate:'80',symptoms:'cefaleia'}).map(([k,value])=>[k,{value}]));
+const c={window:{alert(){}},localStorage:{getItem(){return null},setItem(){}},document:{getElementById:id=>fields[id]},crypto:require('crypto').webcrypto,FormData:class{constructor(form){this.form=form}get(k){return this.form[k]??null}},console};vm.createContext(c);
+for(const n of ['state','cadastro','triagem','fila'])vm.runInContext(fs.readFileSync(root+n+'.js','utf8'),c);
+let passed=0;function test(name,fn){fn();passed++;console.log('PASS',name)}
+const T=c.window.TriageController,t=new T({}, {}, {}, {});
+test('BUG03 pressão com barra',()=>{fields.bloodPressure.value='180/110';assert.equal(t.calculateSuggestion(),'vermelho');fields.bloodPressure.value='120/80'});
+test('BUG04 decimais ponto e vírgula',()=>{for(const v of ['38.5','38,5']){fields.temperature.value=v;assert.equal(t.calculateSuggestion(),'vermelho')}fields.temperature.value='37.8';assert.equal(t.calculateSuggestion(),'amarelo');fields.temperature.value='36.5'});
+test('BUG05 formulário vazio não classifica',()=>{const old=fields.oxygen.value;fields.oxygen.value='';assert.equal(t.calculateSuggestion(),null);fields.oxygen.value=old});
+test('BUG08 validação numérica',()=>{for(const v of ['101','-1','abc','98xyz']){fields.oxygen.value=v;assert.ok(t.readVitals().error)}fields.oxygen.value='98';fields.temperature.value='abc';assert.ok(t.readVitals().error);fields.temperature.value='36.5'});
+const state=new c.window.AppState('p','t');state.patients=[{id:'a',firstName:'Ana',lastName:'Silva'},{id:'b',firstName:'Bruno',lastName:'Silva'}];let rendered=0;
+const q=new c.window.QueueController(state,{queueBoard:{},callNext:{}},{verde:'Verde',vermelho:'Vermelho'},{verde:'green',vermelho:'red'},{renderAll(){rendered++}});
+test('BUG01 prioridade e desempate',()=>{state.triages=[{id:'g',patientId:'a',classification:'verde'},{id:'r1',patientId:'b',classification:'vermelho',createdAt:'2026-10-24T10:00:00Z'},{id:'r2',patientId:'b',classification:'vermelho',createdAt:'2026-10-24T11:00:00Z'}];assert.equal(q.orderedTriages()[0].id,'r1');q.callNext();assert.equal(state.triages.length,2);assert.equal(q.orderedTriages()[0].id,'r2')});
+const dom={patientList:{},patientSearch:{value:'bruno'}};const reg=new c.window.PatientRegistration(state,dom,{renderAll(){},selectPatient(){}});
+test('BUG02 remoção por ID com filtro',()=>{reg.render();assert.ok(dom.patientList.innerHTML.includes('data-delete-patient="b"'));reg.handleListClick({target:{closest:s=>s==='[data-delete-patient]'?{dataset:{deletePatient:'b'}}:null}});assert.equal(state.patients[0].id,'a');assert.equal(state.patients.length,1)});
+test('BUG11 nome completo caixa e acento',()=>{state.patients[0].lastName='Sílva';dom.patientSearch.value='SILVA';reg.render();assert.ok(dom.patientList.innerHTML.includes('Ana'))});
+test('BUG07 codificação de conteúdo e atributos',()=>{assert.equal(vm.runInContext('escapeHTML(`<img src=x onerror="alert(1)">`)',c),'&lt;img src=x onerror=&quot;alert(1)&quot;&gt;');state.patients[0].address='<b>texto</b>';reg.render();assert.ok(dom.patientList.innerHTML.includes('&lt;b&gt;texto&lt;/b&gt;'));assert.ok(!dom.patientList.innerHTML.includes('<b>texto'))});
+const td={triageForm:{reset(){Object.values(fields).forEach(x=>x.value='')}},triagePatient:{value:''},riskSuggestion:{},triageMessage:{},patientSummary:{},vitalPreview:{}};const tr=new T(state,td,{showMessage:(target,msg)=>target.textContent=msg,renderAll(){}},{verde:'Verde'});
+test('BUG06 troca limpa dados e classificação',()=>{state.selectedPatientId='old';fields.symptoms.value='Paciente anterior';tr.selectPatient('a');assert.equal(fields.symptoms.value,'');assert.equal(state.selectedPatientId,'a');assert.equal(td.riskSuggestion.textContent,'Sem sugestão')});
+test('BUG12 identificação completa',()=>{tr.renderPatientOptions();assert.ok(td.triagePatient.innerHTML.includes('Ana Sílva — a'))});
+test('BUG10 triagem duplicada bloqueada',()=>{for(const [id,v] of Object.entries({bloodPressure:'120/80',temperature:'36.5',oxygen:'98',heartRate:'80',symptoms:'cefaleia'}))fields[id].value=v;state.triages=[{id:'one',patientId:'a',classification:'verde'}];td.triageForm.classification='verde';tr.handleSubmit({preventDefault(){}});assert.equal(state.triages.length,1);assert.ok(td.triageMessage.textContent.includes('já está na fila'))});
+test('BUG08 idade inválida bloqueada',()=>{let message='';const data={firstName:'Ana',lastName:'Silva',age:'-1',gender:'Feminino',address:'Rua',insuranceName:'SUS',insuranceCard:'1'};const r=new c.window.PatientRegistration(state,{patientForm:data,patientMessage:{}},{showMessage:(_,msg)=>message=msg});r.handleSubmit({preventDefault(){}});assert.ok(message.includes('idade inteira'))});
+test('BUG09 JSON corrompido preservado e gravação bloqueada',()=>{c.localStorage.getItem=()=>'{bad';const broken=new c.window.AppState('p','t');assert.equal(broken.storageBlocked,true);assert.equal(broken.save(),false);c.localStorage.getItem=()=>null});
+test('falha de persistência cancela chamada',()=>{const original=state.triages;state.save=()=>false;q.callNext();assert.equal(state.triages,original)});
+console.log(`${passed} testes aprovados`);
```

### 0007-package.patch

```diff
--- /dev/null
+++ b/package.json
@@ -0,0 +1,12 @@
+{
+  "name": "sistema-triagem",
+  "version": "1.1.1",
+  "private": true,
+  "description": "Sistema educacional de cadastro, triagem e fila com correções de bugs",
+  "scripts": {
+    "test": "node tests/regressao.cjs"
+  },
+  "engines": {
+    "node": ">=18"
+  }
+}
```

## Código completo corrigido com comentários didáticos

Os blocos a seguir incluem os cinco arquivos alterados. Os comentários explicam responsabilidades e decisões sem mudar o código executável. Os arquivos HTML e CSS foram preservados; a suíte de testes é explicada na seção seguinte.

### Código comentado de assets/state.js

```javascript
// Gera um identificador único. Usa randomUUID quando disponível e alternativa criptográfica quando ausente.
function createRecordId() {
  if (typeof crypto.randomUUID === "function") return crypto.randomUUID();
  // Cria 16 bytes aleatórios sem recorrer a Math.random().
  const bytes = crypto.getRandomValues(new Uint8Array(16));
  // Ajusta os bits da versão 4 e da variante do UUID.
  bytes[6] = (bytes[6] & 15) | 64; bytes[8] = (bytes[8] & 63) | 128;
  const hex = Array.from(bytes, b => b.toString(16).padStart(2, "0")).join("");
  return `${hex.slice(0,8)}-${hex.slice(8,12)}-${hex.slice(12,16)}-${hex.slice(16,20)}-${hex.slice(20)}`;
}
// Codifica valores antes de interpolar em HTML.
// Codifica caracteres especiais em textos e atributos HTML entre aspas. Não usar como proteção universal para outros contextos.
function escapeHTML(value) {
  return String(value ?? "").replace(/[&<>"']/g, c => ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
}
// Normaliza acentos, caixa e espaços para comparar pesquisas e nomes completos.
function normalizeSearch(value) { return String(value).normalize("NFD").replace(/[\u0300-\u036f]/g, "").toLowerCase().trim(); }
class AppState {
  // Inicializa listas e seleção; carrega snapshot ou formato antigo e bloqueia gravações quando os dados não podem ser interpretados.
  constructor(patientStorageKey, triageStorageKey) {
    this.patientStorageKey = patientStorageKey;
    this.triageStorageKey = triageStorageKey;
    this.snapshotKey = patientStorageKey + "_snapshot_v2";
    this.activeScreen = "cadastro";
    this.selectedPatientId = "";
    this.patients = []; this.triages = []; this.storageError = ""; this.storageBlocked = false;
    try {
      // Lê primeiro o formato corrigido; as chaves antigas são usadas somente se ele não existir.
      const snapshot = localStorage.getItem(this.snapshotKey);
      const data = snapshot ? JSON.parse(snapshot) : {
        patients: JSON.parse(localStorage.getItem(patientStorageKey) || "[]"),
        triages: JSON.parse(localStorage.getItem(triageStorageKey) || "[]")
      };
      // Além do JSON válido, exige listas e campos essenciais no formato esperado.
      if (!Array.isArray(data.patients) || !Array.isArray(data.triages) ||
          !data.patients.every(x => x && typeof x.id === "string" && typeof x.firstName === "string" && typeof x.lastName === "string") ||
          !data.triages.every(x => x && typeof x.id === "string" && typeof x.patientId === "string" && ["branco","verde","amarelo","vermelho"].includes(x.classification))) throw Error("Formato inválido");
      this.patients = data.patients; this.triages = data.triages;
    } catch (error) {
      // Evita sobrescrever o conteúdo original após falha de leitura.
      this.storageBlocked = true;
      this.storageError = "Não foi possível carregar os dados salvos. Os originais foram preservados. Recupere o armazenamento antes de cadastrar ou alterar registros.";
    }
  }
  // Retorna true somente após salvar as duas listas juntas. Retorna false se bloqueado ou se o armazenamento falhar.
  save() {
    if (this.storageBlocked) { window.alert(this.storageError); return false; }
    try {
      // Gravação única; chaves legadas permanecem disponíveis para recuperação.
      // Grava um único JSON com as duas listas; as chaves antigas continuam preservadas.
      localStorage.setItem(this.snapshotKey, JSON.stringify({patients:this.patients,triages:this.triages}));
      return true;
    } catch (error) { window.alert("Não foi possível salvar. A alteração foi cancelada; verifique o armazenamento do navegador."); return false; }
  }
  // Monta o nome completo a partir de nome e sobrenome.
  getPatientName(patient) { return `${patient.firstName} ${patient.lastName}`; }
  // Busca o objeto do paciente pelo ID selecionado; retorna undefined quando não existe.
  getSelectedPatient() { return this.patients.find(patient => patient.id === this.selectedPatientId); }
}
window.AppState = AppState;
```

### Código comentado de assets/cadastro.js

```javascript
class PatientRegistration {
  // Recebe o estado compartilhado, os elementos da interface e as funções de comunicação com a aplicação.
  constructor(state, dom, callbacks) {
    this.state = state;
    this.dom = dom;
    this.callbacks = callbacks;
  }

  // Conecta envio, limpeza, pesquisa e cliques nos cartões aos métodos do controlador.
  init() {
    this.dom.patientForm.addEventListener("submit", (event) => this.handleSubmit(event));
    this.dom.clearPatientForm.addEventListener("click", () => this.reset());
    this.dom.patientSearch.addEventListener("input", () => this.render());
    this.dom.patientList.addEventListener("click", (event) => this.handleListClick(event));
  }

  // Lê o formulário, valida campos e idade, cria o registro e salva. Só limpa o formulário após sucesso.
  handleSubmit(event) {
    event.preventDefault();
    const data = new FormData(this.dom.patientForm);
    const patient = {
      id: createRecordId(),
      firstName: data.get("firstName").trim(),
      lastName: data.get("lastName").trim(),
      age: data.get("age"),
      gender: data.get("gender"),
      address: data.get("address").trim(),
      insuranceName: data.get("insuranceName").trim(),
      insuranceCard: data.get("insuranceCard").trim(),
      insuranceValidUntil: data.get("insuranceValidUntil"),
      insurancePlan: data.get("insurancePlan"),
      // Armazena data e hora completas, adequadas ao desempate e independentes da apresentação local.
      createdAt: new Date().toISOString()
    };
    if (!patient.firstName || !patient.lastName || !patient.age || !patient.gender || !patient.address || !patient.insuranceName || !patient.insuranceCard) {
      this.callbacks.showMessage(this.dom.patientMessage, "Preencha os campos obrigatórios.");
      return;
    }
    if (!/^\d+$/.test(String(patient.age).trim()) || !Number.isInteger(Number(patient.age)) || Number(patient.age) < 0 || Number(patient.age) > 150) {
      this.callbacks.showMessage(this.dom.patientMessage, "Informe uma idade inteira entre 0 e 150."); return;
    }
    this.state.patients.push(patient);
    if (!this.state.save()) { this.state.patients.pop(); return; }
    this.callbacks.selectPatient(patient.id);
    this.state.selectedPatientId = patient.id;
    this.reset();
    this.callbacks.showMessage(this.dom.patientMessage, "Paciente cadastrado.");
    this.callbacks.renderAll();
  }

  // Distingue Triar de Remover usando delegação de eventos. Seleção e exclusão utilizam IDs únicos.
  handleListClick(event) {
    const selectButton = event.target.closest("[data-select-patient]");
    const deleteButton = event.target.closest("[data-delete-patient]");
    if (selectButton) {
      this.callbacks.selectPatient(selectButton.dataset.selectPatient);
      this.callbacks.setScreen("triagem");
      this.callbacks.renderAll();
    }
    if (deleteButton) {
      // O botão contém o ID do paciente, não o índice do resultado filtrado.
      const id = deleteButton.dataset.deletePatient;
      if (!this.state.patients.some(patient => patient.id === id)) return;
      // Guarda as listas anteriores para restaurá-las se a persistência falhar.
      const previousPatients = this.state.patients;
      const previousTriages = this.state.triages;
      this.state.patients = previousPatients.filter(patient => patient.id !== id);
      this.state.triages = previousTriages.filter(triage => triage.patientId !== id);
      if (!this.state.save()) { this.state.patients = previousPatients; this.state.triages = previousTriages; return; }
      if (this.state.selectedPatientId === id) this.callbacks.selectPatient("");
      this.callbacks.renderAll();
    }
  }

  // Restaura os valores iniciais do formulário de cadastro; não apaga pacientes salvos.
  reset() {
    this.dom.patientForm.reset();
  }

  // Filtra pelo nome completo normalizado e monta os cartões com interpolação codificada para HTML.
  render() {
    const search = normalizeSearch(this.dom.patientSearch.value);
    const patients = this.state.patients.filter((patient) => normalizeSearch(this.state.getPatientName(patient)).includes(search));
    if (patients.length === 0) {
      this.dom.patientList.innerHTML = `<div class="empty-state">Nenhum paciente na recepção.</div>`;
      return;
    }
    this.dom.patientList.innerHTML = patients.map((patient, index) => `
      <article class="patient-card">
        <header>
          <div>
            <strong>${escapeHTML(this.state.getPatientName(patient))}</strong>
            <small>${escapeHTML(patient.age)} anos · ${escapeHTML(patient.gender)}</small>
          </div>
          <span class="tag">${escapeHTML(patient.insurancePlan)}</span>
        </header>
        <div class="meta-line">${escapeHTML(patient.address)}</div>
        <div class="meta-line">${escapeHTML(patient.insuranceName)} · ${escapeHTML(patient.insuranceCard)}</div>
        <div class="patient-actions">
          <button class="small-button" data-select-patient="${escapeHTML(patient.id)}" type="button">Triar</button>
          <button class="small-button danger-button" data-delete-patient="${escapeHTML(patient.id)}" type="button">Remover</button>
        </div>
      </article>
    `).join("");
  }
}

window.PatientRegistration = PatientRegistration;
```

### Código comentado de assets/triagem.js

```javascript
class TriageController {
  // Recebe estado, interface, callbacks e nomes permitidos das classificações.
  constructor(state, dom, callbacks, priorityLabels) {
    this.state = state;
    this.dom = dom;
    this.callbacks = callbacks;
    this.priorityLabels = priorityLabels;
  }

  // Conecta seleção de paciente, sinais vitais, sugestão, envio e limpeza aos respectivos métodos.
  init() {
    this.dom.triagePatient.addEventListener("change", () => this.selectPatient());
    ["bloodPressure", "temperature", "oxygen", "heartRate"].forEach((id) => {
      document.getElementById(id).addEventListener("input", () => this.renderVitalPreview());
    });
    this.dom.suggestRisk.addEventListener("click", () => this.suggestRisk());
    this.dom.triageForm.addEventListener("submit", (event) => this.handleSubmit(event));
    this.dom.clearTriageForm.addEventListener("click", () => this.reset());
  }

  // Ao mudar de paciente, limpa os dados anteriores. Valida o novo ID e atualiza seleção, resumo e prévia.
  selectPatient(id = this.dom.triagePatient.value) {
    if (id !== this.state.selectedPatientId) {
      this.dom.triageForm.reset();
      this.dom.riskSuggestion.textContent = "Sem sugestão";
      this.dom.triageMessage.textContent = "";
    }
    this.state.selectedPatientId = this.state.patients.some(p => p.id === id) ? id : "";
    this.renderPatientOptions(); this.renderPatientSummary(); this.renderVitalPreview();
  }
  // Valida a sintaxe de pressão com barra; retorna NaN para campos que não possam ser interpretados.
  parseBloodPressure(value) {
    // A expressão regular exige dois grupos numéricos separados por barra e aceita espaços ao redor dela.
    const parts = String(value).trim().match(/^(\d{1,3})\s*\/\s*(\d{1,3})$/);
    if (!parts) return {systolic:NaN, diastolic:NaN};
    return {systolic:Number(parts[1]), diastolic:Number(parts[2])};
  }
  // Centraliza leitura e validação: pressão, decimais, oxigenação, frequência opcional e sintomas. Retorna dados e mensagem de erro.
  readVitals() {
    const read = id => document.getElementById(id).value.trim();
    // Valida todo o texto antes da conversão: não aceita sufixos como 38abc.
    const decimal = value => /^(?:\d+(?:[.,]\d+)?|[.,]\d+)$/.test(value) ? Number(value.replace(",", ".")) : NaN;
    const pressure = this.parseBloodPressure(read("bloodPressure"));
    const temperature = decimal(read("temperature")), oxygen = decimal(read("oxygen"));
    // Campo opcional: vazio vira null em vez de zero.
    const heartRate = read("heartRate") ? decimal(read("heartRate")) : null;
    const symptoms = read("symptoms");
    let error = "";
    if (!Number.isFinite(pressure.systolic) || !Number.isFinite(pressure.diastolic) || pressure.systolic <= 0 || pressure.diastolic <= 0 || pressure.systolic <= pressure.diastolic) error = "Informe pressão válida no formato 120/80, com sistólica maior que diastólica.";
    else if (!Number.isFinite(temperature) || temperature <= 0) error = "Informe temperatura numérica positiva, com ponto ou vírgula.";
    else if (!Number.isFinite(oxygen) || oxygen < 0 || oxygen > 100) error = "Informe oxigenação entre 0 e 100%.";
    else if (heartRate !== null && (!Number.isInteger(heartRate) || heartRate <= 0)) error = "Informe frequência cardíaca inteira positiva ou deixe em branco.";
    else if (!symptoms) error = "Informe sintomas e observações.";
    return {pressure,temperature,oxygen,heartRate,symptoms,error};
  }
  // Método legado preservado, mas não chamado pelo envio corrigido. O novo envio exige classificação explícita válida.
  normalizeClassification(value) {
    return value || "branco";
  }

  // Calcula somente após validação. Retorna null em erro; conserva os limites de classificação já existentes no projeto.
  calculateSuggestion() {
    const {pressure, temperature, oxygen, symptoms: rawSymptoms, heartRate, error} = this.readVitals();
    if (error) return null;
    const symptoms = rawSymptoms.toLowerCase();
    let risk = "verde";
    if (oxygen < 90 || pressure.systolic >= 180 || temperature >= 38.5 || symptoms.includes("dor no peito") || symptoms.includes("convuls")) {
      risk = "vermelho";
    } else if (oxygen < 94 || pressure.systolic >= 160 || temperature >= 37.8 || heartRate > 120 || symptoms.includes("falta de ar")) {
      risk = "amarelo";
    } else if (symptoms.includes("receita") || symptoms.includes("atestado")) {
      risk = "branco";
    }
    return risk;
  }

  // Mostra erro e limpa a classificação quando os dados são inválidos; caso contrário, seleciona a opção sugerida.
  suggestRisk() {
    const vitals = this.readVitals();
    if (vitals.error) {
      this.callbacks.showMessage(this.dom.triageMessage, vitals.error);
      this.dom.riskSuggestion.textContent = "Dados inválidos";
      document.querySelectorAll('[name="classification"]').forEach(input => input.checked = false);
      return;
    }
    const risk = this.calculateSuggestion();
    const riskInput = document.querySelector(`[name="classification"][value="${risk}"]`);
    if (riskInput) {
      riskInput.checked = true;
    }
    this.dom.riskSuggestion.textContent = `Sugestão: ${this.priorityLabels[risk]}`;
  }

  // Valida dados, classificação e paciente; bloqueia duplicação ativa; inclui data completa e desfaz a inclusão se save falhar.
  handleSubmit(event) {
    event.preventDefault();
    const data = new FormData(this.dom.triageForm);
    const classification = data.get("classification");
    const vitals = this.readVitals();
    if (vitals.error) { this.callbacks.showMessage(this.dom.triageMessage, vitals.error); return; }
    // Só aceita classificações presentes no conjunto de rótulos; não atribui branco silenciosamente.
    if (!Object.hasOwn(this.priorityLabels, classification)) { this.callbacks.showMessage(this.dom.triageMessage, "Selecione uma classificação."); return; }
    if (!this.state.getSelectedPatient()) { this.callbacks.showMessage(this.dom.triageMessage, "Selecione um paciente válido."); return; }
    // Impede que o mesmo paciente apareça duas vezes na fila ativa.
    if (this.state.triages.some(item => item.patientId === this.state.selectedPatientId)) {
      this.callbacks.showMessage(this.dom.triageMessage, "Este paciente já está na fila. Não é possível duplicar a triagem."); return;
    }
    if (!this.state.selectedPatientId || !data.get("bloodPressure") || !data.get("temperature") || !data.get("oxygen") || !data.get("symptoms")) {
      this.callbacks.showMessage(this.dom.triageMessage, "Preencha todos os dados da triagem.");
      return;
    }
    this.state.triages.push({
      id: createRecordId(),
      patientId: this.state.selectedPatientId,
      bloodPressure: data.get("bloodPressure"),
      temperature: data.get("temperature"),
      oxygen: data.get("oxygen"),
      heartRate: data.get("heartRate"),
      symptoms: data.get("symptoms").trim(),
      classification,
      // Armazena data e hora completas, adequadas ao desempate e independentes da apresentação local.
      createdAt: new Date().toISOString(),
      time: new Date().toLocaleTimeString("pt-BR", {
        hour: "2-digit",
        minute: "2-digit"
      })
    });
    // Remove a entrada recém-criada da memória se a gravação não ocorrer.
    if (!this.state.save()) { this.state.triages.pop(); return; }
    this.reset();
    this.callbacks.showMessage(this.dom.triageMessage, "Triagem registrada.");
    this.callbacks.renderAll();
  }

  // Limpa formulário e seleção atual. Atualiza as opções, o resumo e a prévia sem excluir registros existentes.
  reset() {
    this.dom.triageForm.reset();
    this.state.selectedPatientId = "";
    this.dom.riskSuggestion.textContent = "Sem sugestão";
    this.renderPatientOptions();
    this.renderPatientSummary();
    this.renderVitalPreview();
  }

  // Exibe nome completo e ID, com codificação de HTML para impedir interpretação de marcação digitada.
  renderPatientOptions() {
    if (this.state.patients.length === 0) {
      this.dom.triagePatient.innerHTML = `<option value="">Nenhum paciente cadastrado</option>`;
      return;
    }
    this.dom.triagePatient.innerHTML = `<option value="">Selecionar paciente</option>` + this.state.patients.map((patient) => `
      <option value="${escapeHTML(patient.id)}">${escapeHTML(this.state.getPatientName(patient))} — ${escapeHTML(patient.id)}</option>
    `).join("");
    this.dom.triagePatient.value = this.state.selectedPatientId;
  }

  // Exibe os dados do paciente selecionado. Na ausência de seleção válida, mostra orientação em vez de um resumo antigo.
  renderPatientSummary() {
    const patient = this.state.getSelectedPatient();
    if (!patient) {
      this.dom.patientSummary.innerHTML = `<div class="empty-state">Selecione um paciente para ver o resumo.</div>`;
      return;
    }
    this.dom.patientSummary.innerHTML = `
      <div class="summary-row"><span>Nome</span><strong>${escapeHTML(this.state.getPatientName(patient))}</strong></div>
      <div class="summary-row"><span>Idade</span><strong>${escapeHTML(patient.age)}</strong></div>
      <div class="summary-row"><span>Gênero</span><strong>${escapeHTML(patient.gender)}</strong></div>
      <div class="summary-row"><span>Convênio</span><strong>${escapeHTML(patient.insuranceName)}</strong></div>
      <div class="summary-row"><span>Plano</span><strong>${escapeHTML(patient.insurancePlan)}</strong></div>
    `;
  }

  // Atualiza a prévia dos campos e destaque de oxigenação. A validação autoritativa permanece em readVitals().
  renderVitalPreview() {
    const pressure = document.getElementById("bloodPressure").value || "--";
    const temperature = document.getElementById("temperature").value || "--";
    const oxygen = document.getElementById("oxygen").value || "--";
    const oxygenNumber = Number(oxygen);
    const oxygenClass = oxygenNumber < 90 ? "alert" : "";
    this.dom.vitalPreview.innerHTML = `
      <div class="vital-row"><span>Pressão</span><strong>${escapeHTML(pressure)}</strong></div>
      <div class="vital-row"><span>Temperatura</span><strong>${escapeHTML(temperature)} °C</strong></div>
      <div class="vital-row"><span>Oxigenação</span><strong class="${escapeHTML(oxygenClass)}">${escapeHTML(oxygen)}%</strong></div>
    `;
  }
}

window.TriageController = TriageController;
```

### Código comentado de assets/fila.js

```javascript
class QueueController {
  // Recebe os dados compartilhados, elementos da fila, rótulos e cores conhecidos e callback de atualização.
  constructor(state, dom, priorityLabels, priorityColor, callbacks) {
    this.state = state;
    this.dom = dom;
    this.priorityLabels = priorityLabels;
    this.priorityColor = priorityColor;
    this.callbacks = callbacks;
  }

  // Associa o botão Chamar próximo ao método de chamada.
  init() {
    this.dom.callNext.addEventListener("click", () => this.callNext());
  }

  // Retorna outra lista ordenada: prioridade primeiro; data completa depois; índice original como alternativa para registros antigos.
  orderedTriages() {
    const priorities = {vermelho:0, amarelo:1, verde:2, branco:3};
    return this.state.triages.map((item,index) => ({item,index})).sort((a,b) => {
      // Compara primeiro o peso de cada classificação; desconhecidos ficam depois dos valores conhecidos.
      const priority = (priorities[a.item.classification] ?? 4) - (priorities[b.item.classification] ?? 4);
      if (priority) return priority;
      // Converte timestamps para números; datas antigas ausentes serão tratadas pelo índice original.
      const ta = Date.parse(a.item.createdAt), tb = Date.parse(b.item.createdAt);
      return Number.isFinite(ta) && Number.isFinite(tb) ? ta-tb || a.index-b.index : a.index-b.index;
    }).map(entry => entry.item);
  }
  // Escolhe o primeiro da ordem calculada, retira da fila e salva; restaura o estado em falha. Não cria histórico de atendimento.
  callNext() {
    if (this.state.triages.length === 0) {
      return;
    }
    const called = this.orderedTriages()[0];
    // Mantém a referência original da fila para poder restaurá-la.
    const previous = this.state.triages;
    this.state.triages = previous.filter(item => item.id !== called.id);
    // Cancela a chamada em caso de falha; o paciente permanece aguardando.
    if (!this.state.save()) { this.state.triages = previous; return; }
    const patient = this.state.patients.find((item) => item.id === called.patientId);
    window.alert(`${patient ? this.state.getPatientName(patient) : "Paciente"} encaminhado para atendimento.`);
    this.callbacks.renderAll();
  }

  // Usa a mesma ordenação da chamada e codifica valores dinâmicos na montagem dos cartões.
  render() {
    if (this.state.triages.length === 0) {
      this.dom.queueBoard.innerHTML = `<div class="empty-state">Nenhum paciente aguardando atendimento.</div>`;
      return;
    }
    this.dom.queueBoard.innerHTML = this.orderedTriages().map((triage) => {
      const patient = this.state.patients.find((item) => item.id === triage.patientId);
      const patientName = patient ? this.state.getPatientName(patient) : "Paciente não encontrado";
      return `
        <article class="queue-card">
          <div class="risk-bar" style="background:${escapeHTML(this.priorityColor[triage.classification])}"></div>
          <div class="queue-body">
            <header>
              <div>
                <strong>${escapeHTML(patientName)}</strong>
                <small>Entrada ${escapeHTML(triage.time)}</small>
              </div>
              <span class="tag ${escapeHTML(triage.classification)}">${escapeHTML(this.priorityLabels[triage.classification])}</span>
            </header>
            <div class="tag-row">
              <span class="tag">PA ${escapeHTML(triage.bloodPressure)}</span>
              <span class="tag">Temp ${escapeHTML(triage.temperature)} °C</span>
              <span class="tag">SpO2 ${escapeHTML(triage.oxygen)}%</span>
            </div>
            <p class="meta-line">${escapeHTML(triage.symptoms)}</p>
          </div>
        </article>
      `;
    }).join("");
  }
}

window.QueueController = QueueController;
```

### Código comentado de assets/app.js

```javascript
const patientStorageKey = "ps_escola_pacientes";
const triageStorageKey = "ps_escola_triagens";

const screenTitles = {
  cadastro: "Cadastro de paciente",
  triagem: "Determinação de triagem",
  fila: "Fila do pronto socorro"
};

const priorityLabels = {
  branco: "Branco",
  verde: "Verde",
  amarelo: "Amarelo",
  vermelho: "Vermelho"
};

const priorityColor = {
  branco: "#dbeafe",
  verde: "#51b56d",
  amarelo: "#f0b83c",
  vermelho: "#d94444"
};

class NavigationController {
  // Recebe referências do estado e dos elementos usados por este controlador.
  constructor(state, dom) {
    this.state = state;
    this.dom = dom;
  }

  // Inicializa os recursos do controlador correspondente. Na aplicação principal, inicia todos e apresenta erros de armazenamento.
  init() {
    this.dom.navTabs.forEach((tab) => {
      tab.addEventListener("click", () => this.setScreen(tab.dataset.screen));
    });
  }

  // Atualiza título e classes active para exibir a tela escolhida.
  setScreen(screen) {
    this.state.activeScreen = screen;
    this.dom.screenTitle.textContent = screenTitles[screen];
    this.dom.navTabs.forEach((tab) => {
      tab.classList.toggle("active", tab.dataset.screen === screen);
    });
    this.dom.screens.forEach((section) => {
      section.classList.toggle("active", section.id === screen);
    });
  }
}

class DashboardController {
  // Recebe referências do estado e dos elementos usados por este controlador.
  constructor(state, dom) {
    this.state = state;
    this.dom = dom;
  }

  // Inicializa os recursos do controlador correspondente. Na aplicação principal, inicia todos e apresenta erros de armazenamento.
  init() {
    this.updateClock();
    window.setInterval(() => this.updateClock(), 30000);
  }

  // Formata o relógio para pt-BR com hora e minuto.
  updateClock() {
    const now = new Date();
    this.dom.shiftClock.textContent = now.toLocaleTimeString("pt-BR", {
      hour: "2-digit",
      minute: "2-digit"
    });
  }

  // Atualiza quantidades de cadastros, triagens ativas e classificações vermelhas.
  renderCounters() {
    this.dom.patientCount.textContent = this.state.patients.length;
    this.dom.waitingCount.textContent = this.state.triages.length;
    this.dom.criticalCount.textContent = this.state.triages.filter((item) => item.classification === "vermelho").length;
  }
}

class TriageApp {
  // Monta os controladores e seus callbacks. O callback de seleção só é executado após a inicialização dos objetos.
  constructor() {
    this.state = new AppState(patientStorageKey, triageStorageKey);
    this.dom = this.createDomMap();
    this.navigation = new NavigationController(this.state, this.dom);
    this.dashboard = new DashboardController(this.state, this.dom);
    this.registration = new PatientRegistration(this.state, this.dom, {
      showMessage: (target, message) => this.showMessage(target, message),
      renderAll: () => this.renderAll(),
      // Encaminha seleção da recepção para a função que também limpa os dados da triagem.
      selectPatient: (id) => this.triage.selectPatient(id),
      setScreen: (screen) => this.navigation.setScreen(screen)
    });
    this.triage = new TriageController(this.state, this.dom, {
      showMessage: (target, message) => this.showMessage(target, message),
      renderAll: () => this.renderAll()
    }, priorityLabels);
    this.queue = new QueueController(this.state, this.dom, priorityLabels, priorityColor, {
      renderAll: () => this.renderAll()
    });
  }

  // Agrupa referências dos elementos HTML para evitar buscas espalhadas por toda a aplicação.
  createDomMap() {
    return {
      screenTitle: document.getElementById("screenTitle"),
      navTabs: document.querySelectorAll(".nav-tab"),
      screens: document.querySelectorAll(".screen"),
      shiftClock: document.getElementById("shiftClock"),
      patientForm: document.getElementById("patientForm"),
      clearPatientForm: document.getElementById("clearPatientForm"),
      patientMessage: document.getElementById("patientMessage"),
      patientSearch: document.getElementById("patientSearch"),
      patientList: document.getElementById("patientList"),
      triageForm: document.getElementById("triageForm"),
      clearTriageForm: document.getElementById("clearTriageForm"),
      triageMessage: document.getElementById("triageMessage"),
      triagePatient: document.getElementById("triagePatient"),
      patientSummary: document.getElementById("patientSummary"),
      vitalPreview: document.getElementById("vitalPreview"),
      suggestRisk: document.getElementById("suggestRisk"),
      riskSuggestion: document.getElementById("riskSuggestion"),
      queueBoard: document.getElementById("queueBoard"),
      callNext: document.getElementById("callNext"),
      patientCount: document.getElementById("patientCount"),
      criticalCount: document.getElementById("criticalCount"),
      waitingCount: document.getElementById("waitingCount")
    };
  }

  // Inicializa os recursos do controlador correspondente. Na aplicação principal, inicia todos e apresenta erros de armazenamento.
  init() {
    this.navigation.init();
    this.dashboard.init();
    this.registration.init();
    this.triage.init();
    this.queue.init();
    this.renderAll();
    if (this.state.storageError) this.showMessage(this.dom.patientMessage, this.state.storageError);
  }

  // Exibe mensagens com textContent; removeu-se o temporizador que apagava o texto após 2,8 segundos.
  showMessage(target, message) {
    target.textContent = message;
    // A mensagem fica visível até ser substituída ou limpa pelas ações previstas.
  }

  // Atualiza indicadores, recepção, seleção, resumo, prévia dos sinais e fila a partir do estado atual.
  renderAll() {
    this.dashboard.renderCounters();
    this.registration.render();
    this.triage.renderPatientOptions();
    this.triage.renderPatientSummary();
    this.triage.renderVitalPreview();
    this.queue.render();
  }
}

const app = new TriageApp();
app.init();
```

## Comentários sobre os testes executados

A suíte usa vm do Node.js para carregar as classes reais. document, localStorage, alert e FormData são simulados para exercitar decisões de lógica sem depender do navegador. Os casos não equivalem a uma auditoria clínica ou a um teste completo de segurança.

| Teste | O que verifica | Por que foi necessário |
| --- | --- | --- |
| 1 | Pressão 180/110 interpretada com barra | A função antiga separava por hífen |
| 2 | Temperaturas 38.5, 38,5 e 37.8 preservadas | parseInt descartava decimais |
| 3 | Oxigenação vazia impede sugestão | Number de texto vazio resultava em zero |
| 4 | Oxigenação inválida e temperatura textual rejeitadas | Campos preenchidos não significam números válidos |
| 5 | Vermelho precede verde; data desempata | shift ignorava classificação |
| 6 | Remoção por ID com busca ativa | Índices filtrados podiam remover outra pessoa |
| 7 | Nome completo com caixa e acento normalizados | Busca original considerava somente firstName |
| 8 | Caracteres HTML codificados e marcação literal | Valores eram interpolados diretamente em innerHTML |
| 9 | Troca limpa sintomas anteriores e reinicia sugestão | Dados de um paciente ficavam no formulário de outro |
| 10 | Nome completo e ID aparecem na seleção | Primeiro nome não distinguia homônimos |
| 11 | Segunda triagem ativa é bloqueada | push criava duplicações |
| 12 | Idade negativa é rejeitada | Não existia validação numérica suficiente |
| 13 | JSON corrompido bloqueia gravação e preserva original | JSON.parse interrompia a inicialização |
| 14 | Falha de save cancela chamada e mantém a fila | Evita informar atendimento sem salvar a alteração |

**Teste no navegador:** cadastro de Teste Regressão com dados fictícios; seleção pelo botão Triar; preenchimento de pressão 120/80, temperatura 38,5, oxigenação 98 e frequência 80; sugestão, registro e exibição na fila concluídos. O cadastro inicialmente revelou ausência de crypto.randomUUID no HTTP local; createRecordId resolveu essa incompatibilidade.

**Conferência dos patches:** o patch consolidado foi aplicado em cópia isolada dos originais. Os sete arquivos resultantes foram comparados byte a byte com os arquivos da entrega corrigida, e os 14 testes passaram nessa cópia.

## O que os resultados não demonstram

Não foi demonstrada ausência de todos os bugs possíveis. Não houve teste clínico, pentest completo, teste de carga, validação em todos os navegadores, teste de concorrência entre abas ou recuperação automática de dados reais. A validação de schema no armazenamento cobre campos essenciais, não todas as regras de negócio de cada registro legado.

O método normalizeClassification permanece no arquivo como código legado, mas o envio corrigido não o utiliza: exige classificação explícita. Isso está explicado no bloco comentado para evitar confundir método existente com comportamento ativo.

## Execução e uso no GitHub

O projeto continua estático: abra index.html com a pasta assets ao lado. Para executar os testes, use Node.js 18 ou superior e rode npm test. Não é necessário instalar bibliotecas.

Este documento pode ser usado como README.md na raiz do repositório ou como documentação técnica em docs/README_CORRECOES.md. Caso o mantenha na raiz, preserve também as orientações de instalação/publicação do README da entrega anterior. Os patches completos estão incorporados neste arquivo; os arquivos .patch separados foram entregues no pacote README_e_Patches_Triagem.zip.

Não foi criada licença de código aberto, nem publicado um repositório GitHub em seu nome nesta etapa. O pacote foi preparado para que a publicação seja feita na conta e no repositório escolhidos por você.
