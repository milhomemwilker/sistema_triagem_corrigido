# Correções da entrega

| ID | Problema corrigido |
| --- | --- |
| BUG01 | Fila e chamada passaram a considerar prioridade e desempate |
| BUG02 | Exclusão após pesquisa passou a utilizar ID único |
| BUG03 | Pressão arterial passou a reconhecer e validar barra |
| BUG04 | Temperatura preserva decimais com ponto ou vírgula |
| BUG05 | Dados vazios ou inválidos bloqueiam sugestão |
| BUG06 | Troca de paciente limpa os dados da triagem anterior |
| BUG07 | Interpolações HTML recebem codificação de caracteres |
| BUG08 | Idade e sinais vitais recebem validações de formato e valor |
| BUG09 | Falhas de leitura e gravação são tratadas, com preservação do original |
| BUG10 | Duplicidade de triagem ativa é bloqueada |
| BUG11 | Busca considera nome completo, caixa e acentos |
| BUG12 | Seleção mostra nome completo e ID |
| BUG13 | Geração de ID tem alternativa quando randomUUID não está disponível |

Validação: 14 testes de regressão aprovados e fluxo no navegador com dados fictícios. As regras de classificação originais foram preservadas.
