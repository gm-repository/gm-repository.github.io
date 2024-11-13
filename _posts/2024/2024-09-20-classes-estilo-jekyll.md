---
layout: post
title: "Classes de Estilo no Jekyll"
date: 2024-09-20 11:51:00 -0300
categories: [Jekyll]
tags: [Jekyll]
---

### Tabela de Referência para Classes de Estilo no Jekyll

| Classe                | Propósito                                         | Exemplo de Uso em Markdown                                               |
|-----------------------|---------------------------------------------------|--------------------------------------------------------------------------|
| **`{:.filepath}`**    | Destacar caminhos de arquivos e diretórios        |```<br>`/etc/nginx/nginx.conf`<br>{:.filepath}```                   |
| **`{:.command}`**     | Destacar comandos de terminal                     |```<br>`sudo apt update`<br>{:.command}```                          |
| **`{:.variable}`**    | Variáveis de ambiente ou placeholders             |```<br>`PATH_TO_CONFIG`<br>{:.variable}```                          |
| **`{:.parameter}`**   | Parâmetros de comandos ou opções de configuração  |```<br>`--verbose`<br>{:.parameter}```                              |
| **`{:.option}`**      | Opções dentro de comandos ou arquivos de configuração |```<br>`ENABLE_LOGS`<br>{:.option}```                          |
| **`{:.output}`**      | Exibir saídas de comandos                         |```<br>File downloaded successfully.<br>{:.output}```               |
| **`{:.warning}`**     | Alertas críticos ou avisos                        |```<br>**Aviso:** Procedimento pode causar perda de dados.<br>{:.warning}``` |
| **`{:.success}`**     | Mensagens de sucesso ou conclusão                 |```<br>**Sucesso:** Instalação concluída.<br>{:.success}```         |
| **`{:.info}`**        | Informações adicionais ou contexto                |```<br>**Informação:** Processo pode levar alguns minutos.<br>{:.info}``` |
| **`{:.tip}`**         | Dicas e boas práticas                             |```<br>**Dica:** Use `sudo` com cautela.<br>{:.tip}```              |
| **`{:.note}`**        | Notas importantes                                 |```<br>**Nota:** Verifique a instalação do Netcat.<br>{:.note}```   |
| **`{:.important}`**   | Informações essenciais                            |```<br>**Importante:** Sempre faça backup antes de alterações.<br>{:.important}``` |
| **`{:.prompt-tip}`**  | Dicas rápidas                                     |```<br>**Dica:** Este comando é útil para troubleshooting.<br>{:.prompt-tip}``` |
| **`{:.prompt-info}`** | Nota rápida                                       |```<br>**Nota:** Verifique se os serviços estão ativos.<br>{:.prompt-note}``` |
| **`{:.prompt-warning}`** | Avisos de precaução                           |```<br>**Aviso:** Operação irreversível.<br>{:.prompt-warning}``` |
| **`{:.prompt-danger}`** | Informação essencial                         |```<br>**Importante:** Não feche o sistema durante a atualização.<br>{:.prompt-important}``` |

---

> Esta é uma dica útil para o usuário.
{: .prompt-tip }

> Informações adicionais que podem ajudar a entender o contexto.
{: .prompt-info }

> Aviso! Este ponto é importante para evitar problemas.
{: .prompt-warning }

> Perigo! Esta ação pode causar perda de dados.
{: .prompt-danger }

