# Sistema de Triagem PS Escola

Aplicação web educacional para cadastrar pacientes, registrar triagens e organizar a fila por classificação. Desenvolvida com HTML, CSS e JavaScript, sem dependências externas de execução.

Esta entrega contém 13 correções de bugs e 14 testes automatizados de regressão. Um fluxo de cadastro, triagem e fila também foi verificado no navegador com dados fictícios.

## Funcionalidades

- Cadastro e remoção de pacientes por identificador único.
- Pesquisa pelo nome completo, sem diferenciar maiúsculas ou acentos.
- Registro de pressão arterial, temperatura, oxigenação, frequência cardíaca e sintomas.
- Classificação manual e sugestão segundo as regras educacionais existentes.
- Fila ordenada por vermelho, amarelo, verde e branco.
- Bloqueio de triagens duplicadas enquanto o paciente aguarda atendimento.
- Persistência local no navegador e tratamento de falhas de armazenamento.

## Executar no computador

1. Extraia o ZIP e abra a pasta `sistema-triagem`.
2. Abra `index.html` em um navegador moderno.
3. Mantenha a pasta `assets` ao lado do HTML.

Não é necessário instalar pacotes ou executar `npm install`.

Para usar um servidor local, caso tenha Python 3 instalado:

```bash
cd sistema-triagem
python -m http.server 8000
```

Abra <http://localhost:8000>. No Windows, também é possível usar `py -m http.server 8000`; no Linux, `python3 -m http.server 8000`.

## Como utilizar

1. Em **Cadastro**, preencha os dados e clique em **Cadastrar paciente**.
2. Clique em **Triar** para abrir o paciente selecionado.
3. Informe pressão no formato `120/80`; temperatura aceita `36.5` ou `36,5`.
4. Preencha os demais campos obrigatórios e selecione uma classificação ou solicite uma sugestão.
5. Clique em **Registrar triagem**.
6. Em **Fila**, acompanhe a ordenação e utilize **Chamar próximo**.

Trocar o paciente limpa o formulário de triagem. A remoção do paciente também exclui suas triagens ativas.

## Estrutura do projeto

| Caminho | Responsabilidade |
| --- | --- |
| `index.html` | Estrutura das telas e formulários |
| `assets/styles.css` | Estilos e layout |
| `assets/state.js` | Estado, persistência, geração de IDs e funções de proteção/normalização |
| `assets/cadastro.js` | Cadastro, pesquisa e remoção |
| `assets/triagem.js` | Validação, sugestão e registro da triagem |
| `assets/fila.js` | Ordenação, exibição e chamada da fila |
| `assets/app.js` | Inicialização, navegação e indicadores |
| `tests/regressao.cjs` | Testes automatizados sem bibliotecas externas |
| `CHANGELOG.md` | Relação das correções |
| `package.json` | Metadados e comando de testes |

## Executar os testes

Requer Node.js 18 ou superior somente para os testes.

```bash
npm test
```

Alternativa:

```bash
node tests/regressao.cjs
```

Resultado esperado: `14 testes aprovados`.

Os testes executam funções em um ambiente simulado de DOM e armazenamento. Não substituem testes completos em diferentes navegadores. O fluxo manual verificado utilizou dados fictícios.

## Publicar o código no GitHub

### Pela interface do GitHub

1. Crie um repositório chamado `sistema-triagem` na sua conta.
2. Extraia o ZIP antes do envio.
3. Use **Add file > Upload files** e envie o conteúdo da pasta `sistema-triagem`, preservando `assets` e `tests`.
4. Confira se `index.html` e `README.md` estão na raiz do repositório.
5. Confirme o envio com **Commit changes**.

Enviar somente o ZIP não disponibiliza os arquivos como um site. Para incluir também os arquivos de configuração iniciados por ponto, prefira o Git pelo terminal.

### Pelo terminal

Instale o Git e crie um repositório vazio no GitHub. Dentro da pasta extraída, execute:

```bash
cd sistema-triagem
git init
git add .
git commit -m "Adiciona sistema de triagem com correções e testes"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/sistema-triagem.git
git push -u origin main
```

Substitua `SEU_USUARIO` pelo seu usuário. Faça a autenticação solicitada pelo GitHub sem inserir credenciais nos arquivos do projeto.

## Disponibilizar a interface com GitHub Pages

Após enviar o código, abra as configurações de **Pages** do repositório. Quando a publicação por branch estiver disponível, selecione a branch `main` e a pasta raiz `/ (root)`. Use o endereço informado pelo GitHub após a conclusão da publicação.

O projeto é estático e não exige etapa de build. A disponibilidade de Pages depende da conta e das configurações do repositório. Considere o site publicado como acessível ao público, salvo controle de acesso explicitamente configurado na plataforma.

## Armazenamento e recuperação

Os dados ficam no `localStorage` do navegador, e não no GitHub ou em um banco de dados remoto. Outro dispositivo, perfil ou endereço de acesso possui armazenamento separado.

A versão corrigida salva pacientes e triagens juntos na chave `ps_escola_pacientes_snapshot_v2`. Quando essa chave não existe, tenta ler `ps_escola_pacientes` e `ps_escola_triagens`, preservadas para compatibilidade.

Se houver JSON inválido ou estrutura incompatível, o sistema mostra uma mensagem e bloqueia gravações para evitar sobrescrever o original. A recuperação é manual: preserve uma cópia do conteúdo antes de qualquer intervenção no armazenamento. Não há recuperação automática implementada.

Falhas de gravação cancelam a alteração em memória. Apagar os dados do site no navegador remove os registros locais.

## Escopo e limitações

- Uso educacional com dados fictícios; as regras de classificação não foram validadas como protocolo clínico.
- Sem backend, autenticação própria, controle de perfis, sincronização entre usuários ou atualização entre abas em tempo real.
- Sem prontuário ou histórico de atendimentos: chamar o próximo retira a triagem da fila ativa.
- Sem fluxo de reavaliação; uma nova triagem ativa para o mesmo paciente é bloqueada.
- Registros novos têm data e hora completas; registros antigos sem esse campo mantêm a ordem relativa no desempate.
- A faixa de idade adotada é de 0 a 150 anos. Temperatura recebe validação numérica positiva, sem limites clínicos adicionais.
- O tratamento de HTML protege as interpolações utilizadas nesta versão; não representa auditoria completa de segurança.

## Licença

Nenhuma licença de código aberto foi definida nesta entrega. Antes de autorizar reutilização por terceiros, escolha uma licença compatível com a autoria e os direitos do projeto.
