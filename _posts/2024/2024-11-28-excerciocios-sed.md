---
layout: post
title: "Explorando o Poder do `sed`: Um Guia Completo para Manipulação de Texto no Linux"
date: 2024-11-28 11:12:13 -0300
categories: [Sed]
tags: [Sed]
excerpt: "Descubra como dominar o comando `sed`, uma ferramenta poderosa para manipulação de texto no Linux. Este guia completo oferece conceitos básicos, exercícios práticos e casos de uso avançados."
comments: true
---

## **Introdução**

O comando `sed` é uma ferramenta indispensável para qualquer profissional que trabalhe com Linux. Seja para realizar substituições simples, editar arquivos em massa ou automatizar tarefas complexas, o `sed` se destaca pela sua versatilidade e eficiência. Este guia foi cuidadosamente elaborado para profissionais de todos os níveis, desde iniciantes até especialistas, oferecendo uma jornada completa pelo universo dessa poderosa ferramenta.

Neste tutorial, você encontrará:

- **Conceitos básicos:** Como começar a usar o `sed` para manipular texto diretamente no terminal.
- **Exercícios práticos:** Desde substituições simples até transformações avançadas em arquivos grandes.
- **Casos de uso reais:** Exemplos de automação, formatação de dados e integração com outras ferramentas como `grep`, `awk` e `xargs`.
- **Tabela de referência rápida:** Uma seção para consulta instantânea de comandos e padrões.

Seja você um administrador de sistemas, desenvolvedor ou entusiasta de tecnologia, este guia é a sua porta de entrada para dominar o `sed`. Vamos começar!

---

## Exercícios de uso do comando sed

**Texto proposto:**

```
cat texto_sed.txt
Linha 1: Este é um arquivo para práticas com o sed.
Linha 2: Ele contém vários exemplos de texto.
Linha 3: Algumas linhas possuem números: 12345.
Linha 4: Outras linhas possuem palavras repetidas: teste teste teste.
Linha 5: Existem palavras como exemplo1 e exemplo2 que seguem um padrão.
Linha 6: Aqui há palavras separadas por vírgulas, como: maçã, banana, laranja.
Linha 7: Esta linha está marcada com o texto especial.
Linha 8: Outro padrão é data no formato AAAA-MM-DD, como 2024-11-28.
Linha 9: Às vezes há múltiplos espaços  entre palavras.
Linha 10: Linhas podem conter erros de digitação: exmplo, prátcas.
Linha 11: Esta linha é irrelevante e pode ser removida.
Linha 12: Números flutuam aqui: 1.23, 45.67, 890.12.
Linha 13: O texto especial aparece novamente aqui.
  Linha 14: Múltiplos espaços no início da linha.

Linha 15: SED, Sed, sed – mistura de maiúsculas e minúsculas.
Linha 16: Esta linha usa delimitadores diferentes: Texto1;Texto2;Texto3.
Linha 17: Uma linha com espaços extras no final.         
Linha 18: Uma linha      com    múltiplos espaços no meio.
Linha 19: PALAVRAS EM CAIXA ALTA PARA TESTES DE MAIÚSCULAS.
Linha 20: Uma linha vazia está logo abaixo.

Linha 21: 

Linha 22: Exemplo de linha com um | delimitador de texto.
Linha 23: Outro exemplo com datas: 2023-10-15 e 2024-01-01.
Linha 24: Palavras misturadas: exmplos, corrije, seding.
Linha 25: Um exemplo de linha com números separados: 12,34,56,78.
Linha 26: Esta linha é irrelevante e deve ser excluída.
Linha 27: Final do arquivo atualizado com novas sugestões.
Linha 28: O servidor principal usa o IP 192.168.1.10.
Linha 29: Um endereço de loopback é 127.0.0.1.
Linha 30: Este é um endereço IPv6: fe80::1ff:fe23:4567:890a.
Linha 31: Contato: usuario@exemplo.com.
Linha 32: O domínio www.exemplo.com está ativo.
Linha 33: Documentação disponível em https://docs.exemplo.com/docs.
Linha 34: Site antigo: http://www.antigo.com.
Linha 35: Telefone: (11) 98765-4321.
Linha 36: Contato internacional: +1-800-555-0199.
Linha 37: Endereço: Rua Exemplo, 123 - CEP: 12345-678.
Linha 38: Código postal dos EUA: 90210.
Linha 39: [2024-11-28 12:00:00] INFO: Serviço iniciado.
Linha 40: [2024-11-28 12:30:00] ERROR: Falha na autenticação.
Linha 41: [2024-11-28 13:00:00] WARN: Espaço em disco baixo.
Linha 42: Alice, Bob, Charlie, Dave.
Linha 43: Grupo: Maria; João; Carla; Lucas.
Linha 44: Nome, Idade, Cidade
Linha 45: Alice, 30, São Paulo
Linha 46: Bob, 25, Rio de Janeiro
Linha 47: Formato americano: 11/28/2024.
Linha 48: Formato ISO 8601: 2024-11-28T14:30:00Z.
Linha 49: Formato brasileiro: 28/11/2024 14:30.
Linha 50: Valores monetários: R$1234,56; $567.89; €42.10.
Linha 51: Coordenadas: Latitude: -23.55052, Longitude: -46.63330.
Linha 52: Valores em notação científica: 1.23e+10, -4.56e-8.
Linha 53: Parágrafo 1: Este é um texto longo para práticas com o sed. Ele inclui diversas frases para análise e modificação.
Linha 54: Parágrafo 2: Outro exemplo de texto que pode ser manipulado. Teste o sed com expressões regulares.
Linha 55: username=admin
Linha 56: password=senha123
Linha 57: host=localhost
Linha 58: port=8080
Linha 59: Código: ABC123XYZ.
Linha 60: Token: 9f8d7e6c5b4a3d2c1f0.
```

---

## **Exercícios Básicos**

### **1.1 Substituir a palavra "exemplo" por "EXEMPLO" em todo o arquivo**

#### Comando:
```bash
sed 's/\bexemplo\b/EXEMPLO/g' texto_sed.txt
```

#### Explicação:
- `s/\bexemplo\b/EXEMPLO/`: Substitui a palavra `exemplo` por `EXEMPLO`.
- `\b`: Delimita a palavra inteira (não afeta `exemplo1` ou `exemplo.com`).
- `g`: Substitui todas as ocorrências na linha.

### **1.2 Remover todas as linhas que contêm "irrelevante"**

#### Comando:
```bash
sed '/irrelevante/d' texto_sed.txt
```

#### Explicação:
- `/irrelevante/`: Filtra linhas que contêm a palavra `irrelevante`.
- `d`: Deleta essas linhas.

### **1.3 Adicionar a linha "Nova linha adicionada" antes de todas as linhas com IPs**

#### Comando:
```bash
sed '/[0-9]\{1,3\}\(\.[0-9]\{1,3\}\)\{3\}/i Nova linha adicionada' texto_sed.txt
```

#### Explicação:
- `/[0-9]\{1,3\}\(\.[0-9]\{1,3\}\)\{3\}/`: Seleciona linhas contendo endereços IPv4.
- `i`: Insere a linha especificada antes das linhas selecionadas.

### **1.4 Remover múltiplos espaços no início ou no final das linhas**

#### Comando:
```bash
sed 's/^[[:space:]]*//;s/[[:space:]]*$//' texto_sed.txt
```

#### Explicação:
- `^[[:space:]]*`: Remove espaços no início da linha.
- `[[:space:]]*$`: Remove espaços no final da linha.
- `;`: Permite aplicar dois comandos em sequência.

### **1.5 Substituir o delimitador `;` por `|` na Linha 43**

#### Comando:
```bash
sed '43s/;/|/g' texto_sed.txt
```

#### Explicação:
- `43`: Aplica o comando apenas à Linha 43.
- `s/;/|/g`: Substitui todos os `;` por `|` na linha.

### **1.6 Substituir números inteiros por `[NÚMERO]`**

#### Comando:
```bash
sed 's/\b[0-9]\+\b/[NÚMERO]/g' texto_sed.txt
```

#### Explicação:
- `\b[0-9]\+\b`: Seleciona números inteiros (delimitados por bordas de palavras).
- `[NÚMERO]`: Substitui os números selecionados pelo texto `[NÚMERO]`.

### **1.7 Transformar todas as palavras em maiúsculas na Linha 19**

#### Comando:
```bash
sed '19s/.*/\U&/' texto_sed.txt
```

#### Explicação:
- `19`: Aplica o comando apenas à Linha 19.
- `\U&`: Converte o texto capturado (`.*`) para maiúsculas.

### **1.8 Remover linhas vazias**

#### Comando:
```bash
sed '/^$/d' texto_sed.txt
```

#### Explicação:
- `/^$/`: Seleciona linhas vazias (`^` indica início e `$` indica fim da linha sem conteúdo).
- `d`: Deleta as linhas selecionadas.

### **1.9 Substituir "https" por "http" nas URLs**

#### Comando:
```bash
sed 's/https:/http:/g' texto_sed.txt
```

#### Explicação:
- `s/https:/http:/`: Substitui `https:` por `http:`.
- `g`: Garante que todas as ocorrências em cada linha sejam alteradas.

### **1.10 Adicionar o texto "[EDITADO]" ao final de cada linha que contenha "sed"**

#### Comando:
```bash
sed '/\bsed\b/s/$/ [EDITADO]/' texto_sed.txt
```

#### Explicação:
- `/\bsed\b/`: Seleciona linhas que contêm a palavra `sed` inteira.
- `s/$/ [EDITADO]/`: Adiciona `[EDITADO]` ao final da linha (`$` representa o final da linha).

---

### **Resumo dos Comandos Básicos**

| **Exercício** | **Comando Principal**                    | **Função**                                             |
|---------------|------------------------------------------|-------------------------------------------------------|
| 1.1           | `s/\bexemplo\b/EXEMPLO/g`               | Substituir "exemplo" por "EXEMPLO".                  |
| 1.2           | `/irrelevante/d`                        | Remover linhas contendo "irrelevante".               |
| 1.3           | `/[0-9]\{1,3\}\(\.[0-9]\{1,3\}\)...`    | Adicionar linha antes de linhas com IPs.             |
| 1.4           | `s/^[[:space:]]*//;s/[[:space:]]*$//`   | Remover espaços no início ou fim das linhas.         |
| 1.5           | `43s/;/|/g`                             | Substituir `;` por `|` na Linha 43.                  |
| 1.6           | `s/\b[0-9]\+\b/[NÚMERO]/g`              | Substituir números inteiros por `[NÚMERO]`.          |
| 1.7           | `19s/.*/\U&/`                           | Converter texto da Linha 19 para maiúsculas.         |
| 1.8           | `/^$/d`                                 | Remover linhas vazias.                                |
| 1.9           | `s/https:/http:/g`                      | Substituir `https` por `http` em URLs.               |
| 1.10          | `/\bsed\b/s/$/ [EDITADO]/`              | Adicionar "[EDITADO]" ao final de linhas com "sed".  |

---

### **Desafios - Exercícios Básicos**

#### **1.1 Substituir palavras específicas apenas em uma linha**
**Pergunta:** 
- Como você substituiria `sed` por `SED` apenas na Linha 15?

**Resposta Esperada:**
```bash
sed '15s/sed/SED/' texto_sed.txt
```

#### **1.2 Adicionar texto ao início de linhas específicas**
**Pergunta:**
- Qual comando adicionaria `# Comentário` ao início das linhas contendo "texto especial"?

**Resposta Esperada:**
```bash
sed '/texto especial/s/^/# Comentário /' texto_sed.txt
```

#### **1.3 Identificar e remover espaços extras**
**Pergunta:**
- Como remover espaços no final das linhas, mas manter os do início?

**Resposta Esperada:**
```bash
sed 's/[[:space:]]*$//' texto_sed.txt
```

---

## **Exercícios Intermediários**

### **2.1 Extrair todas as datas no formato `AAAA-MM-DD`**

#### Comando:
```bash
sed -n 's/.*\([0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}\).*/\1/p' texto_sed.txt
```

#### Explicação:
- `-n`: Suprime a saída padrão.
- `s/.*\([0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}\).*/\1/p`:
  - Captura datas no formato `AAAA-MM-DD` usando regex.
  - `p`: Imprime apenas as capturas.

---

### **2.2 Substituir e-mails pelo texto `[EMAIL_OCULTADO]`**

#### Comando:
```bash
sed 's/[a-zA-Z0-9._%+-]\+@[a-zA-Z0-9.-]\+\.[a-zA-Z]\{2,6\}/[EMAIL_OCULTADO]/g' texto_sed.txt
```

#### Explicação:
- `[a-zA-Z0-9._%+-]\+`: Captura a parte local de um e-mail.
- `@[a-zA-Z0-9.-]\+`: Captura o domínio.
- `\.[a-zA-Z]\{2,6\}`: Captura a extensão do domínio.
- Substitui e-mails encontrados por `[EMAIL_OCULTADO]`.

---

### **2.3 Transformar listas delimitadas por vírgulas em listas numeradas (Linha 42)**

#### Comando:
```bash
sed '42s/\([^,]*\), \([^,]*\), \([^,]*\), \([^,]*\)/1. \1\n2. \2\n3. \3\n4. \4/' texto_sed.txt
```

#### Explicação:
- `42`: Aplica o comando apenas à Linha 42.
- `\([^,]*\)`: Captura palavras separadas por vírgulas.
- `1. \1\n2. \2\n...`: Formata como uma lista numerada, usando `\n` para quebra de linha.

---

### **2.4 Adicionar o texto `[PRIVADO]` antes de todas as linhas com senhas**

#### Comando:
```bash
sed '/password/i [PRIVADO]' texto_sed.txt
```

#### Explicação:
- `/password/`: Filtra linhas que contêm a palavra `password`.
- `i [PRIVADO]`: Insere `[PRIVADO]` antes dessas linhas.

---

### **2.5 Substituir valores monetários por `[VALOR]`**

#### Comando:
```bash
sed 's/R\$\?[0-9]\+,\?[0-9]*/[VALOR]/g' texto_sed.txt
```

#### Explicação:
- `R\$\?[0-9]\+,\?[0-9]*`: Captura valores monetários no formato `R$1234,56` ou `1234,56`.
- `[VALOR]`: Substitui os valores encontrados pelo texto `[VALOR]`.

---

### **2.6 Alterar a ordem das palavras separadas por vírgulas (Linha 6)**

#### Comando:
```bash
sed '6s/\([^,]*\), \([^,]*\), \([^,]*\)/\3, \2, \1/' texto_sed.txt
```

#### Explicação:
- `6`: Aplica o comando apenas à Linha 6.
- `\([^,]*\)`: Captura palavras separadas por vírgulas.
- `\3, \2, \1`: Reorganiza as capturas na ordem inversa.

---

### **2.7 Transformar logs de eventos (Linhas 39-41) em formato JSON**

#### Comando:
```bash
sed -n '39,41s/\[\(.*\)\] \(.*\): \(.*\)/{\n  "timestamp": "\1",\n  "level": "\2",\n  "message": "\3"\n}/p' texto_sed.txt
```

#### Explicação:
- `39,41`: Aplica o comando às linhas 39 a 41.
- `\[\(.*\)\]`: Captura o timestamp.
- `\(.*\):`: Captura o nível do log (`INFO`, `ERROR`, etc.).
- `\(.*\)`: Captura a mensagem do log.
- Formata como JSON com chaves `timestamp`, `level` e `message`.

---

### **2.8 Substituir coordenadas por texto padronizado `[LAT/LONG]`**

#### Comando:
```bash
sed 's/Latitude: [+-]*[0-9.]\+, Longitude: [+-]*[0-9.]\+/[LAT\/LONG]/g' texto_sed.txt
```

#### Explicação:
- `Latitude: [+-]*[0-9.]\+, Longitude: [+-]*[0-9.]\+`:
  - Captura coordenadas no formato `Latitude: -23.55052, Longitude: -46.63330`.
- `[LAT/LONG]`: Substitui coordenadas capturadas pelo texto `[LAT/LONG]`.

---

### **2.9 Excluir todas as linhas com tokens alfanuméricos**

#### Comando:
```bash
sed '/[a-zA-Z0-9]\{8,\}/d' texto_sed.txt
```

#### Explicação:
- `[a-zA-Z0-9]\{8,\}`: Seleciona linhas que contêm tokens com 8 ou mais caracteres alfanuméricos.
- `d`: Deleta essas linhas.

---

### **2.10 Adicionar sufixo ` | VALOR FINAL` em todas as linhas com números decimais**

#### Comando:
```bash
sed '/[0-9]\+\.[0-9]\+/s/$/ | VALOR FINAL/' texto_sed.txt
```

#### Explicação:
- `/[0-9]\+\.[0-9]\+/`: Seleciona linhas que contêm números decimais.
- `s/$/ | VALOR FINAL/`: Adiciona o texto `| VALOR FINAL` ao final da linha.

---

### **Resumo dos Comandos Intermediários**

| **Exercício** | **Comando Principal**                           | **Função**                                           |
|---------------|-------------------------------------------------|-----------------------------------------------------|
| 2.1           | `s/.*\([0-9]\{4\}-[0-9]\{2\}...\)/\1/p`         | Extrair datas no formato `AAAA-MM-DD`.              |
| 2.2           | `s/[a-zA-Z0-9._%+-]\+@[a-zA-Z0-9.-]...\)`       | Substituir e-mails por `[EMAIL_OCULTADO]`.          |
| 2.3           | `42s/\([^,]*\), \([^,]*\),...`                  | Transformar listas delimitadas por vírgulas.        |
| 2.4           | `/password/i [PRIVADO]`                         | Adicionar `[PRIVADO]` antes de linhas com senhas.   |
| 2.5           | `s/R\$\?[0-9]\+,\?[0-9]*/[VALOR]/g`             | Substituir valores monetários por `[VALOR]`.        |
| 2.6           | `6s/\([^,]*\), \([^,]*\),...`                   | Alterar a ordem de palavras separadas por vírgulas. |
| 2.7           | `39,41s/\[\(.*\)\] \(.*\): \(.*\)/{...}`         | Transformar logs em formato JSON.                   |
| 2.8           | `s/Latitude: [+-]*[0-9.]\+, Longitude...`       | Substituir coordenadas por `[LAT/LONG]`.            |
| 2.9           | `/[a-zA-Z0-9]\{8,\}/d`                          | Excluir linhas com tokens alfanuméricos longos.     |
| 2.10          | `/[0-9]\+\.[0-9]\+/s/$/ | VALOR FINAL/`         | Adicionar sufixo em linhas com números decimais.    |

---

### **Desafios - Exercícios Intermediários**

#### **2.1 Reorganizar listas separadas por vírgulas**
**Pergunta:**
- Dado o texto "maçã, banana, laranja", qual comando inverte a ordem para "laranja, banana, maçã"?

**Resposta Esperada:**
```bash
sed 's/\([^,]*\), \([^,]*\), \([^,]*\)/\3, \2, \1/' texto_sed.txt
```

#### **2.2 Normalizar e-mails**
**Pergunta:**
- Como substituir qualquer e-mail por `[EMAIL_OCULTADO]`?

**Resposta Esperada:**
```bash
sed 's/[a-zA-Z0-9._%+-]\+@[a-zA-Z0-9.-]\+\.[a-zA-Z]\{2,6\}/[EMAIL_OCULTADO]/g' texto_sed.txt
```

#### **2.3 Adicionar texto condicionalmente**
**Pergunta:**
- Qual comando adiciona `[SENHA]` antes de linhas contendo "password"?

**Resposta Esperada:**
```bash
sed '/password/i [SENHA]' texto_sed.txt
```

---

## **Exercícios Avançados**

### **3.1 Reorganizar datas no formato `AAAA-MM-DD` para `DD/MM/AAAA` em todo o arquivo**

#### Comando:
```bash
sed 's/\([0-9]\{4\}\)-\([0-9]\{2\}\)-\([0-9]\{2\}\)/\3\/\2\/\1/g' texto_sed.txt
```

#### Explicação:
- `\([0-9]\{4\}\)`: Captura quatro dígitos (ano).
- `\([0-9]\{2\}\)`: Captura dois dígitos (mês e dia).
- `\3\/\2\/\1`: Reorganiza os grupos capturados para o formato `DD/MM/AAAA`.
- `g`: Aplica a substituição em todas as ocorrências por linha.

---

### **3.2 Inserir uma linha com `# LOG` antes de cada evento de log (Linhas 39-41)**

#### Comando:
```bash
sed '39,41s/^/# LOG: /' texto_sed.txt
```

#### Explicação:
- `39,41`: Aplica o comando às linhas 39 a 41.
- `s/^/# LOG: /`: Insere `# LOG: ` no início de cada linha.

---

### **3.3 Substituir tokens por suas versões truncadas (apenas os primeiros 5 caracteres)**

#### Comando:
```bash
sed 's/\b[a-zA-Z0-9]\{6,\}\b/\U&/g;s/\([a-zA-Z0-9]\{5\}\)[a-zA-Z0-9]*/\1/g' texto_sed.txt
```

#### Explicação:
1. Primeiro comando:
   - `s/\b[a-zA-Z0-9]\{6,\}\b/\U&/g`: Garante que tokens com 6 ou mais caracteres sejam selecionados.
2. Segundo comando:
   - `s/\([a-zA-Z0-9]\{5\}\)[a-zA-Z0-9]*/\1/g`: Trunca os tokens para os primeiros 5 caracteres.

---

### **3.4 Alterar valores no formato `chave=valor` para `chave: valor`**

#### Comando:
```bash
sed 's/^\([a-zA-Z_]\+\)=\(.*\)/\1: \2/' texto_sed.txt
```

#### Explicação:
- `^\([a-zA-Z_]\+\)=\(.*\)`:
  - Captura a chave antes do `=` e o valor depois.
- `\1: \2`: Substitui `=` por `:`.

---

### **3.5 Reverter a ordem das palavras em linhas que contenham "parágrafo"**

#### Comando:
```bash
sed '/parágrafo/s/\([^ ]*\) \([^ ]*\)/\2 \1/g' texto_sed.txt
```

#### Explicação:
- `/parágrafo/`: Filtra linhas que contêm a palavra `parágrafo`.
- `\([^ ]*\) \([^ ]*\)`: Captura duas palavras consecutivas.
- `\2 \1`: Inverte a ordem das capturas.

---

### **3.6 Identificar e remover valores científicos (`1.23e+10`)**

#### Comando:
```bash
sed 's/\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b//g' texto_sed.txt
```

#### Explicação:
- `\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b`:
  - Seleciona valores no formato científico (e.g., `1.23e+10`).
- `g`: Remove todas as ocorrências na linha.

---

### **3.7 Excluir todas as linhas com palavras repetidas consecutivas**

#### Comando:
```bash
sed '/\b\([a-zA-Z]\+\) \1\b/d' texto_sed.txt
```

#### Explicação:
- `\b\([a-zA-Z]\+\) \1\b`: Captura palavras consecutivas repetidas.
- `d`: Deleta linhas que atendem ao padrão.

---

### **3.8 Adicionar índice crescente a todas as linhas do arquivo**

#### Comando:
```bash
sed '=' texto_sed.txt | sed 'N;s/\n/: /'
```

#### Explicação:
1. **`=`**:
   - Exibe os números das linhas antes de cada linha do arquivo.
2. **Pipeline**:
   - Combina os números das linhas com o conteúdo usando `N` (une duas linhas consecutivas).
3. **`s/\n/: /`**:
   - Substitui a quebra de linha entre o número e o conteúdo da linha por `: `.

---

### **3.9 Substituir URLs pelo domínio extraído**

#### Comando:
```bash
sed 's|https\?://\([^/]*\).*|\1|g' texto_sed.txt
```

#### Explicação:
- `https\?://\([^/]*\)`:
  - Captura o domínio de URLs no formato `http://` ou `https://`.
- Substitui a URL inteira pelo domínio.

---

### **3.10 Criar uma lista JSON com dados de nomes e cidades (Linhas 44-46)**

#### Comando:
```bash
sed -n '44,46s/\([^,]*\), \([^,]*\), \(.*\)/{\n  "name": "\1",\n  "age": \2,\n  "city": "\3"\n},/p' texto_sed.txt
```

#### Explicação:
- `44,46`: Aplica o comando às linhas 44 a 46.
- `\([^,]*\), \([^,]*\), \(.*\)`:
  - Captura o nome, idade e cidade.
- Formata os dados em estilo JSON.

---

### **Resumo dos Comandos Avançados**

| **Exercício** | **Comando Principal**                          | **Função**                                           |
|---------------|------------------------------------------------|-----------------------------------------------------|
| 3.1           | `s/\([0-9]\{4\}\)-\([0-9]\{2\}...\)...`        | Reorganizar datas no formato `DD/MM/AAAA`.          |
| 3.2           | `39,41s/^/# LOG: /`                            | Adicionar `# LOG` antes de eventos de log.          |
| 3.3           | `s/\b[a-zA-Z0-9]\{6,\}...\`                   | Truncar tokens para os primeiros 5 caracteres.      |
| 3.4           | `s/^\([a-zA-Z_]\+\)=\(.*\)/\1: \2/`            | Alterar `chave=valor` para `chave: valor`.          |
| 3.5           | `/parágrafo/s/\([^ ]*\) \([^ ]*\)...`          | Reverter palavras em linhas com "parágrafo".        |
| 3.6           | `s/\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b//g`        | Remover valores científicos.                        |
| 3.7           | `/\b\([a-zA-Z]\+\) \1\b/d`                     | Excluir linhas com palavras repetidas consecutivas. |
| 3.8           | `= | sed 'N;s/\n/: /'`                         | Adicionar índice crescente às linhas.               |
| 3.9           | `s|https\?://\([^/]*\)...|\1|g`                | Substituir URLs pelo domínio.                       |
| 3.10          | `44,46s/\([^,]*\), \([^,]*\)...`               | Criar lista JSON com nomes e cidades.               |

---

### **Desafios - Exercícios Avançados**


#### **3.1 Converter logs para JSON**
**Pergunta:**
- Dado um log `[2024-11-28 12:00:00] INFO: Serviço iniciado.`, qual comando transforma isso em JSON?

**Resposta Esperada:**
```bash
sed 's/\[\(.*\)\] \(.*\): \(.*\)/{\n  "timestamp": "\1",\n  "level": "\2",\n  "message": "\3"\n}/' texto_sed.txt
```

#### **3.2 Reverter palavras em linhas com padrões específicos**
**Pergunta:**
- Como inverter palavras apenas nas linhas que contêm "exemplo"?

**Resposta Esperada:**
```bash
sed '/exemplo/s/\([^ ]*\) \([^ ]*\)/\2 \1/' texto_sed.txt
```

---

## **Exercícios Especializados**

### **4.1 Converter o arquivo para formato JSON básico**

#### Comando:
```bash
sed -e '1i {' -e 's/^Linha \([0-9]*\): \(.*\)/  "linha_\1": "\2",/' -e '$s/,$/\n}/' texto_sed.txt
```

#### Explicação:
1. **`1i {`**:
   - Insere `{` na primeira linha (início do JSON).
2. **`s/^Linha \([0-9]*\): \(.*\)/  "linha_\1": "\2",/`**:
   - Captura o número da linha e seu conteúdo, formatando como `"linha_n": "conteúdo",`.
3. **`$s/,$/\n}/`**:
   - Remove a vírgula final na última linha e insere `}` para fechar o JSON.

---

### **4.2 Transformar o arquivo em formato YAML**

#### Comando:
```bash
sed -e '1i linhas:' -e 's/^Linha \([0-9]*\): \(.*\)/  - linha: \1\n    texto: "\2"/' texto_sed.txt
```

#### Explicação:
1. **`1i linhas:`**:
   - Insere `linhas:` no início do arquivo (cabeçalho do YAML).
2. **`s/^Linha \([0-9]*\): \(.*\)/  - linha: \1\n    texto: "\2"/`**:
   - Formata cada linha como um item YAML, incluindo o número e o texto correspondente.

---

### **4.3 Criar uma lista com os domínios de URLs presentes no arquivo**

#### Comando:
```bash
sed -n 's|https\?://\([^/]*\).*|\1|p' texto_sed.txt | sort | uniq
```

#### Explicação:
1. **`https\?://\([^/]*\)`**:
   - Captura o domínio de URLs no formato `http://` ou `https://`.
2. **Pipeline**:
   - `sort | uniq`: Remove domínios duplicados, listando apenas valores únicos.

---

### **4.4 Substituir números flutuantes por `[FLOAT]`**

#### Comando:
```bash
sed 's/\b[0-9]\+\.[0-9]\+\b/[FLOAT]/g' texto_sed.txt
```

#### Explicação:
- `\b[0-9]\+\.[0-9]\+\b`: Seleciona números flutuantes.
- `[FLOAT]`: Substitui os valores capturados pelo texto `[FLOAT]`.

---

### **4.5 Identificar valores monetários e somar**

#### Comando:
```bash
sed -n 's/.*\([0-9]\+,\?[0-9]*\).*/\1/p' texto_sed.txt | tr ',' '.' | awk '{sum+=$1} END {print sum}'
```

#### Explicação:
1. **`sed -n 's/.*\([0-9]\+,\?[0-9]*\).*/\1/p'`**:
   - Extrai valores monetários.
2. **`tr ',' '.'`**:
   - Converte vírgulas para pontos (para cálculos no formato numérico).
3. **`awk`**:
   - Soma os valores extraídos.

---

### **4.6 Criar cabeçalhos com base em palavras-chave**

#### Comando:
```bash
sed '/^Linha [0-9]*:/s/^/# SEÇÃO: /' texto_sed.txt
```

#### Explicação:
- `/^Linha [0-9]*:/`: Seleciona linhas que começam com `Linha n:`.
- `s/^/# SEÇÃO: /`: Adiciona `# SEÇÃO:` no início dessas linhas.

---

### **4.7 Formatar logs como JSON**

#### Comando:
```bash
sed -n '/^\[.*\]/s/\[\(.*\)\] \(.*\): \(.*\)/{\n  "timestamp": "\1",\n  "level": "\2",\n  "message": "\3"\n}/p' texto_sed.txt
```

#### Explicação:
1. **`/^\[.*\]/`**:
   - Filtra linhas que começam com colchetes (logs).
2. **Substituição**:
   - Converte as linhas em objetos JSON com chaves `timestamp`, `level` e `message`.

---

### **4.8 Criar backup das linhas alteradas em um novo arquivo**

#### Comando:
```bash
sed '/exemplo/s/exemplo/EXEMPLO/w alteradas.txt' texto_sed.txt
```

#### Explicação:
- `/exemplo/s/exemplo/EXEMPLO/`: Substitui `exemplo` por `EXEMPLO`.
- `w alteradas.txt`: Salva as linhas alteradas no arquivo `alteradas.txt`.

---

### **4.9 Excluir linhas contendo valores científicos (`1.23e+10`)**

#### Comando:
```bash
sed '/\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b/d' texto_sed.txt
```

#### Explicação:
- `/\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b/`: Seleciona linhas com valores científicos.
- `d`: Deleta as linhas correspondentes.

---

### **4.10 Adicionar índice e reformatar como CSV**

#### Comando:
```bash
sed '=' texto_sed.txt | sed 'N;s/\n/, /' > arquivo.csv
```

#### Explicação:
1. **`=`**:
   - Exibe o número da linha antes de cada linha do arquivo.
2. **`N;s/\n/, /`**:
   - Combina o número da linha com seu conteúdo, separando-os por `,`.
3. **`> arquivo.csv`**:
   - Salva o resultado em um arquivo CSV.

---

### **Resumo dos Comandos Especializados**

| **Exercício** | **Comando Principal**                            | **Função**                                           |
|---------------|--------------------------------------------------|-----------------------------------------------------|
| 4.1           | `s/^Linha \([0-9]*\): \(.*\)...`                 | Converter para JSON básico.                        |
| 4.2           | `s/^Linha \([0-9]*\): \(.*\)...`                 | Converter para YAML básico.                        |
| 4.3           | `s|https\?://\([^/]*\)...|\1|p`                  | Listar domínios de URLs.                           |
| 4.4           | `s/\b[0-9]\+\.[0-9]\+\b/[FLOAT]/g`               | Substituir números flutuantes por `[FLOAT]`.        |
| 4.5           | `sed + tr + awk`                                 | Somar valores monetários extraídos.                |
| 4.6           | `/^Linha [0-9]*:/s/^/# SEÇÃO: /`                 | Criar cabeçalhos baseados em palavras-chave.        |
| 4.7           | `/^\[.*\]/s/\[\(.*\)\]...`                       | Formatar logs como JSON.                           |
| 4.8           | `/exemplo/s/exemplo/EXEMPLO/w alteradas.txt`     | Criar backup das linhas alteradas.                 |
| 4.9           | `/\b[0-9]\+\.[0-9]\+e[+-][0-9]\+\b/d`            | Excluir linhas com valores científicos.            |
| 4.10          | `= | sed 'N;s/\n/, /' > arquivo.csv`             | Adicionar índice e salvar como CSV.                |

---

### **Desafios - Exercícios Especializados**

#### **4.1 Geração dinâmica de scripts**
**Pergunta:**
- Dado o IP `192.168.0.1`, qual comando gera um script que pinga esse endereço?

**Resposta Esperada:**
```bash
sed -n 's/.*IP \([0-9.]*\).*/ping -c 1 \1/p' texto_sed.txt > ping_script.sh
```

#### **4.2 Processar tabelas para Markdown**
**Pergunta:**
- Dada a tabela "Nome, Idade, Cidade", como convertê-la para Markdown?

**Resposta Esperada:**
```bash
sed 's/,/ | /g;s/^/| /;s/$/ |/' texto_sed.txt
```

#### **4.3 Extração de domínios únicos**
**Pergunta:**
- Como listar os domínios únicos presentes no arquivo?

**Resposta Esperada:**
```bash
sed -n 's|https\?://\([^/]*\).*|\1|p' texto_sed.txt | sort | uniq
```

---

## **Erros Comuns no Uso do `sed`**

Aqui estão os erros mais frequentes ao usar o `sed` e como resolvê-los:

### **1. Esquecer de Escapar Caracteres Especiais**

#### Cenário:
Ao tentar substituir uma barra `/` em uma URL, o comando falha devido ao uso incorreto do delimitador padrão.

#### Exemplo Errado:
```bash
sed 's/http://https://g' texto_sed.txt
```

#### Problema:
O `sed` interpreta as barras (`/`) como delimitadores do comando `s`.

#### Solução:
Escape as barras ou use um delimitador alternativo, como `|`:
```bash
sed 's|http://|https://|g' texto_sed.txt
```

### **2. Aplicar Substituições Globalmente Quando Não Necessário**

#### Cenário:
Você deseja substituir apenas a primeira ocorrência de "teste" em cada linha, mas usa o modificador `g`.

#### Exemplo Errado:
```bash
sed 's/teste/TESTE/g' texto_sed.txt
```

#### Problema:
O modificador `g` substitui todas as ocorrências na linha, não apenas a primeira.

#### Solução:
Remova o modificador `g` para afetar apenas a primeira ocorrência:
```bash
sed 's/teste/TESTE/' texto_sed.txt
```

### **3. Confundir Delimitadores em Substituições**

#### Cenário:
Ao usar diferentes delimitadores, você se esquece de que o primeiro caractere define o delimitador para o comando inteiro.

#### Exemplo Errado:
```bash
sed 's;http://https://;g' texto_sed.txt
```

#### Problema:
O `;` usado após `https://` não é reconhecido como delimitador de encerramento.

#### Solução:
Certifique-se de que todos os delimitadores estejam consistentes:
```bash
sed 's;http://;https://;g' texto_sed.txt
```

### **4. Falha ao Escapar Caracteres de Regex**

#### Cenário:
Você quer substituir um ponto literal (`.`) por uma vírgula, mas não escapa o ponto.

#### Exemplo Errado:
```bash
sed 's/./,/g' texto_sed.txt
```

#### Problema:
O `.` corresponde a qualquer caractere, não ao ponto literal.

#### Solução:
Escape o ponto com uma barra invertida (`\`):
```bash
sed 's/\./,/g' texto_sed.txt
```

### **5. Confundir Regex e Strings Literais**

#### Cenário:
Você quer substituir uma string contendo `*`, mas o `*` é interpretado como parte da expressão regular.

#### Exemplo Errado:
```bash
sed 's/*/TEXTO/g' texto_sed.txt
```

#### Problema:
O `*` é um operador regex que significa "zero ou mais do caractere anterior".

#### Solução:
Escape o `*` ou use a opção `-r` para desativar o comportamento padrão de regex:
```bash
sed 's/\*/TEXTO/g' texto_sed.txt
```

### **6. Ignorar a Opção `-i` em Edições In-Place**

#### Cenário:
Você espera modificar diretamente um arquivo, mas o `sed` imprime o resultado apenas na saída padrão.

#### Exemplo Errado:
```bash
sed 's/exemplo/EXEMPLO/' texto_sed.txt
```

#### Problema:
O arquivo original não é alterado, pois o comando não usa a opção `-i` (edição in-place).

#### Solução:
Adicione `-i` para editar o arquivo diretamente:
```bash
sed -i 's/exemplo/EXEMPLO/' texto_sed.txt
```

### **7. Aplicar Substituições em Todo o Arquivo Sem Filtrar**

#### Cenário:
Você quer substituir "linha" apenas nas linhas que contêm "texto especial", mas aplica o comando a todas as linhas.

#### Exemplo Errado:
```bash
sed 's/linha/LINHA/' texto_sed.txt
```

#### Problema:
O comando afeta todas as ocorrências de "linha", não apenas as desejadas.

#### Solução:
Adicione um filtro para restringir as linhas afetadas:
```bash
sed '/texto especial/s/linha/LINHA/' texto_sed.txt
```

### **8. Trabalhar com Arquivos Grandes Sem Saber o Limite de Buffer**

#### Cenário:
Você processa um arquivo muito grande, mas o `sed` falha ao tentar capturar múltiplas linhas com `N`.

#### Problema:
O `sed` tem um limite de buffer por linha, e arquivos muito grandes podem causar problemas.

#### Solução:
Use ferramentas como `awk` ou processe o arquivo em blocos:
```bash
sed -n '1,100p' texto_sed.txt | sed 's/exemplo/EXEMPLO/g'
```

### **9. Esquecer de Suprimir a Saída Padrão com `-n`**

#### Cenário:
Você quer imprimir apenas linhas que contêm "ERROR", mas todas as linhas aparecem na saída.

#### Exemplo Errado:
```bash
sed '/ERROR/p' texto_sed.txt
```

#### Problema:
O comando imprime todas as linhas, porque o `p` não suprime a saída padrão.

#### Solução:
Use `-n` para suprimir a saída padrão:
```bash
sed -n '/ERROR/p' texto_sed.txt
```

### **10. Substituições Sem Salvar no Arquivo Final**

#### Cenário:
Você realiza substituições complexas, mas se esquece de redirecionar a saída para um novo arquivo.

#### Exemplo Errado:
```bash
sed 's/exemplo/EXEMPLO/' texto_sed.txt
```

#### Problema:
A saída modificada é apenas exibida no terminal.

#### Solução:
Redirecione a saída para um arquivo ou use `-i`:
```bash
sed 's/exemplo/EXEMPLO/' texto_sed.txt > texto_modificado.txt
```
Ou:
```bash
sed -i 's/exemplo/EXEMPLO/' texto_sed.txt
```

## **Resumo**

| **Erro**                         | **Causa**                                             | **Solução**                                          |
|-----------------------------------|-------------------------------------------------------|-----------------------------------------------------|
| Esquecer de escapar caracteres    | Caracteres como `/`, `.` ou `*` são tratados como regex | Use `\` para escapar ou delimitadores alternativos. |
| Aplicar substituições globalmente | Uso desnecessário do modificador `g`                 | Remova `g` para afetar apenas a primeira ocorrência. |
| Ignorar a opção `-i`              | O arquivo original não é modificado                  | Use `-i` para edição in-place.                      |
| Esquecer de suprimir a saída      | Uso do comando `p` sem `-n`                          | Adicione `-n` para mostrar apenas as capturas.      |

---

## **Tabela de Referência Rápida do `sed`**

Abaixo estão os principais elementos do `sed`, organizados por categorias para facilitar a consulta.

---

### **1. Operadores Básicos**

| **Comando**       | **Descrição**                                                                 |
|--------------------|-------------------------------------------------------------------------------|
| `s/padrão/subst/`  | Substitui o **padrão** pela **substituição** na linha.                        |
| `p`                | Imprime a linha.                                                             |
| `d`                | Deleta a linha selecionada.                                                  |
| `i texto`          | Insere **texto** antes da linha atual.                                       |
| `a texto`          | Adiciona **texto** depois da linha atual.                                    |
| `c texto`          | Substitui a linha atual por **texto**.                                       |

---

### **2. Modificadores do Comando `s`**

| **Modificador** | **Descrição**                                      |
|------------------|--------------------------------------------------|
| `/g`            | Substitui todas as ocorrências na linha.          |
| `/i`            | Ignora maiúsculas e minúsculas na correspondência.|
| `/n`            | Substitui apenas a **n-ésima** ocorrência na linha.|

---

### **3. Seleção de Linhas**

| **Padrão**     | **Descrição**                                                              |
|----------------|---------------------------------------------------------------------------|
| `N`            | Seleciona a linha de número **N**.                                        |
| `N,M`          | Seleciona o intervalo de linhas de **N** até **M**.                       |
| `/regex/`      | Seleciona linhas que correspondem à **regex**.                            |
| `!`            | Aplica o comando a linhas que **não** correspondem ao padrão.             |

**Exemplo:**
```bash
sed '2,4d' arquivo.txt         # Remove as linhas 2 a 4
sed '/erro/d' arquivo.txt      # Remove linhas contendo "erro"
sed '/^[[:space:]]*$/d'        # Remove linhas vazias
```

---

### **4. Operadores de Espaço**

| **Operador** | **Descrição**                                                                                   |
|--------------|-------------------------------------------------------------------------------------------------|
| `h`          | Copia o conteúdo do espaço padrão (*pattern space*) para o espaço de retenção (*hold space*).   |
| `g`          | Substitui o conteúdo do espaço padrão pelo espaço de retenção.                                  |
| `x`          | Troca os conteúdos do espaço padrão e do espaço de retenção.                                   |
| `H`          | Adiciona o conteúdo do espaço padrão ao espaço de retenção.                                    |

**Exemplo:**
```bash
sed '/START/,/END/{h;/END/x}' arquivo.txt  # Alterna as seções START/END
```

---

### **5. Classes de Caracteres**

| **Classe**          | **Descrição**                     | **Exemplo**                           |
|---------------------|-----------------------------------|---------------------------------------|
| `[[:digit:]]`       | Números (`0-9`).                 | `/[[:digit:]]+/` (encontra números). |
| `[[:alpha:]]`       | Letras (`a-z`, `A-Z`).           | `/[[:alpha:]]+/` (encontra palavras).|
| `[[:space:]]`       | Espaços, tabulações, etc.         | `/[[:space:]]+/` (encontra espaços). |
| `[[:upper:]]`       | Letras maiúsculas.                | `/[[:upper:]]+/` (encontra `ABC`).   |
| `[[:lower:]]`       | Letras minúsculas.                | `/[[:lower:]]+/` (encontra `abc`).   |

---

### **6. Âncoras**

| **Âncora** | **Descrição**                                                                 |
|------------|-------------------------------------------------------------------------------|
| `^`        | Representa o início da linha.                                                |
| `$`        | Representa o final da linha.                                                |
| `\b`       | Representa a borda de uma palavra.                                           |

**Exemplo:**
```bash
sed '/^inicio/p' arquivo.txt   # Imprime linhas que começam com "inicio"
sed '/fim$/p' arquivo.txt      # Imprime linhas que terminam com "fim"
```

---

### **7. Escapando Caracteres Especiais**

| **Caractere** | **Descrição**                     | **Como Escapar**           |
|---------------|-----------------------------------|----------------------------|
| `/`           | Delimitador padrão do `s`.       | `\/` ou outro delimitador. |
| `.`           | Qualquer caractere.              | `\.` (para ponto literal). |
| `*`           | Zero ou mais do caractere anterior. | `\*` (para asterisco literal). |
| `[` e `]`     | Conjuntos de caracteres.          | `\[` e `\]`.               |

---

### **8. Opções Comuns**

| **Opção**   | **Descrição**                                                                 |
|-------------|-------------------------------------------------------------------------------|
| `-n`        | Suprime a saída padrão; imprime apenas as linhas especificadas.              |
| `-i`        | Faz edições in-place (diretamente no arquivo).                               |
| `-e`        | Permite adicionar múltiplos comandos em uma única execução.                  |
| `-f arquivo`| Lê comandos de um arquivo externo.                                           |

---

### **9. Exemplos Rápidos**

| **Comando**                  | **Função**                                                         |
|------------------------------|-------------------------------------------------------------------|
| `sed 's/foo/bar/'`           | Substitui `foo` por `bar` na primeira ocorrência de cada linha.    |
| `sed -i 's/foo/bar/g'`       | Substitui todas as ocorrências de `foo` por `bar` no arquivo.      |
| `sed -n '/foo/p'`            | Imprime apenas linhas contendo `foo`.                             |
| `sed '/foo/d'`               | Deleta linhas contendo `foo`.                                     |
| `sed '5q'`                   | Para a leitura após a quinta linha.                               |
| `sed 's/[0-9]/#/g'`          | Substitui todos os dígitos por `#`.                                |
| `sed '/START/,/END/d'`       | Remove todas as linhas entre "START" e "END".                     |

---

## **Integração do `sed` com Outras Ferramentas**

Aqui estão exemplos de como o `sed` pode ser usado em conjunto com outras ferramentas para automação e manipulação avançada de dados.

### **1. Combinação com `grep`**

#### **1.1 Filtrar Linhas e Substituir um Padrão**
Use `grep` para localizar linhas específicas e `sed` para realizar substituições nelas.

**Exemplo:**
```bash
grep 'erro' log.txt | sed 's/erro/ERRO_CRÍTICO/g'
```

**Descrição:**
- `grep 'erro' log.txt`: Seleciona apenas linhas contendo `erro`.
- `sed 's/erro/ERRO_CRÍTICO/g'`: Substitui `erro` por `ERRO_CRÍTICO` nessas linhas.

#### **1.2 Substituir Apenas em Linhas Encontradas pelo `grep`**
Use `xargs` para aplicar substituições apenas em arquivos que contenham uma palavra específica.

**Exemplo:**
```bash
grep -l 'palavra' *.txt | xargs -I {} sed -i 's/palavra/substituta/g' {}
```

**Descrição:**
- `grep -l 'palavra' *.txt`: Lista arquivos que contêm `palavra`.
- `xargs`: Executa `sed` em cada arquivo encontrado.

### **2. Integração com `awk`**

#### **2.1 Calcular Valores Após Substituições**
Use `sed` para formatar os dados e `awk` para cálculos.

**Exemplo:**
```bash
sed 's/,/./g' valores.txt | awk '{soma+=$1} END {print soma}'
```

**Descrição:**
- `sed 's/,/./g' valores.txt`: Substitui vírgulas por pontos nos valores numéricos.
- `awk`: Soma os valores processados.

#### **2.2 Reorganizar Dados Complexos**
Combine `sed` e `awk` para processar tabelas e reformatá-las.

**Exemplo:**
```bash
sed 's/,/;/g' tabela.txt | awk -F';' '{print $2, $1, $3}'
```

**Descrição:**
- `sed 's/,/;/g' tabela.txt`: Substitui vírgulas por `;`.
- `awk -F';'`: Reorganiza as colunas.

### **3. Integração com `find`**

#### **3.1 Renomear Arquivos em Massa**
Use `find` para localizar arquivos e `sed` para alterar seus nomes.

**Exemplo:**
```bash
find . -name '*.txt' | sed 's/.txt$/.md/' | xargs -I {} mv {} {}
```

**Descrição:**
- `find . -name '*.txt'`: Lista arquivos `.txt`.
- `sed 's/.txt$/.md/'`: Altera a extensão para `.md`.
- `xargs mv`: Renomeia os arquivos.

#### **3.2 Editar Arquivos Encontrados**
Aplique mudanças diretamente nos arquivos localizados com `find`.

**Exemplo:**
```bash
find . -type f -name '*.conf' -exec sed -i 's/localhost/127.0.0.1/g' {} +
```

**Descrição:**
- `find . -type f -name '*.conf'`: Localiza arquivos `.conf`.
- `sed -i`: Substitui `localhost` por `127.0.0.1` nesses arquivos.

### **4. Processamento com `xargs`**

#### **4.1 Processar Arquivos em Lote**
Use `xargs` para passar uma lista de arquivos para o `sed`.

**Exemplo:**
```bash
ls *.log | xargs sed -i 's/erro/warning/g'
```

**Descrição:**
- `ls *.log`: Lista arquivos `.log`.
- `xargs sed -i`: Substitui `erro` por `warning` em todos os arquivos listados.

### **5. Integração com Scripts Shell**

#### **5.1 Substituir Baseado em Entradas Dinâmicas**
Use variáveis Shell para tornar os comandos dinâmicos.

**Exemplo:**
```bash
novo_valor="nova_configuracao"
sed -i "s/configuracao_antiga/$novo_valor/g" arquivo.txt
```

**Descrição:**
- O valor de `novo_valor` é inserido dinamicamente no comando `sed`.

#### **5.2 Gerar Comandos Baseados em um Arquivo**
Extraia dados de um arquivo para criar comandos.

**Exemplo:**
```bash
sed 's/.*/mv & &.backup/' lista_arquivos.txt | bash
```

**Descrição:**
- `sed`: Cria comandos `mv` para renomear arquivos, adicionando `.backup`.
- `| bash`: Executa os comandos gerados.

### **6. Casos de Uso Reais**

#### **6.1 Analisar Logs e Gerar Relatórios**
Use o `sed` para filtrar eventos críticos e combiná-lo com outras ferramentas para criar relatórios.

**Exemplo:**
```bash
grep 'ERROR' log.txt | sed 's/^/[CRÍTICO] /' > erros_criticos.txt
```

**Descrição:**
- `grep`: Filtra apenas linhas com `ERROR`.
- `sed`: Adiciona o prefixo `[CRÍTICO]` a essas linhas.

#### **6.2 Transformar Dados para Formatos Personalizados**
Combine `sed` e outras ferramentas para gerar formatos como JSON ou YAML.

**Exemplo:**
```bash
sed 's/^\(.*\), \(.*\), \(.*\)$/- name: \1\n  age: \2\n  city: \3/' tabela.txt
```

**Descrição:**
- Converte uma tabela CSV para YAML.

### **Resumo de Integrações**

| **Ferramenta** | **Uso Combinado**                                                                       |
|----------------|-----------------------------------------------------------------------------------------|
| **`grep`**     | Filtrar linhas antes de processar com o `sed`.                                         |
| **`awk`**      | Realizar cálculos ou reorganizar dados após substituições.                             |
| **`find`**     | Localizar arquivos e aplicar transformações neles.                                     |
| **`xargs`**    | Passar resultados de um comando para o `sed` em massa.                                 |
| **Shell**      | Criar scripts dinâmicos que integram substituições com outros comandos do sistema.     |

---


## **Casos de Uso em Automação**

---

### **1. Geração Automática de Backups**

#### **Descrição**
Crie backups para arquivos de texto, adicionando a extensão `.bak` e, opcionalmente, um carimbo de data.

#### **Script**
```bash
#!/bin/bash
for file in *.txt; do
  cp "$file" "${file%.txt}_$(date +%Y%m%d).bak"
  sed -i.bak 's/localhost/127.0.0.1/g' "$file"
done
```

#### **Explicação**
- **`cp "$file" "${file%.txt}_$(date +%Y%m%d).bak"`**:
  - Copia o arquivo, adicionando a data ao nome do backup.
- **`sed -i.bak 's/localhost/127.0.0.1/g' "$file"`**:
  - Substitui `localhost` por `127.0.0.1` no arquivo original, criando um backup automático com `.bak`.

---

### **2. Normalização de Dados em Arquivos Grandes**

#### **Descrição**
Limpe e normalize grandes arquivos de texto, removendo linhas irrelevantes, múltiplos espaços e palavras-chave sensíveis.

#### **Script**
```bash
#!/bin/bash
sed -i '/irrelevante/d' data.txt       # Remove linhas com "irrelevante"
sed -i 's/[[:space:]]\+/ /g' data.txt # Substitui múltiplos espaços por um único
sed -i 's/password/[REDACTED]/g' data.txt # Substitui "password" por "[REDACTED]"
```

#### **Explicação**
- **`/irrelevante/d`**: Remove linhas irrelevantes.
- **`s/[[:space:]]\+/ /g`**: Normaliza múltiplos espaços consecutivos.
- **`s/password/[REDACTED]/g`**: Oculta palavras sensíveis.

---

### **3. Geração de Relatórios CSV**

#### **Descrição**
Extraia dados de um arquivo de texto e formate-os em um relatório CSV.

#### **Script**
```bash
#!/bin/bash
sed -n '/^Linha [0-9]*:/p' texto_sed.txt | sed 's/^Linha \([0-9]*\): \(.*\)/\1, "\2"/' > relatorio.csv
```

#### **Explicação**
- **Primeiro `sed`**: Filtra apenas linhas numeradas (`/^Linha [0-9]*:/`).
- **Segundo `sed`**: Formata cada linha como `número, "texto"`.
- **Redirecionamento**: Salva o resultado em `relatorio.csv`.

---

### **4. Limpeza de Logs e Exportação de Erros**

#### **Descrição**
Filtre erros críticos de um arquivo de log e gere um relatório com timestamps.

#### **Script**
```bash
#!/bin/bash
grep 'ERROR' logs.txt | sed 's/\[ERROR\]/[CRITICAL]/g' > erros_criticos.txt
```

#### **Explicação**
- **`grep 'ERROR'`**: Seleciona apenas linhas contendo `ERROR`.
- **`sed 's/\[ERROR\]/[CRITICAL]/g'`**: Substitui a tag `[ERROR]` por `[CRITICAL]`.
- **Redirecionamento**: Salva o resultado em `erros_criticos.txt`.

---

### **5. Reformatar Logs em JSON**

#### **Descrição**
Converta logs com timestamps e mensagens em formato JSON.

#### **Script**
```bash
#!/bin/bash
sed -n '/^\[.*\]/s/\[\(.*\)\] \(.*\): \(.*\)/{\n  "timestamp": "\1",\n  "level": "\2",\n  "message": "\3"\n}/p' logs.txt > logs.json
```

#### **Explicação**
- **`/^\[.*\]/`**: Seleciona linhas que começam com `[`.
- **Substituição**: Formata cada linha como JSON com `timestamp`, `level` e `message`.
- **Redirecionamento**: Salva o resultado em `logs.json`.

---

### **6. Renomear Arquivos em Massa**

#### **Descrição**
Renomeie arquivos substituindo espaços por underscores.

#### **Script**
```bash
#!/bin/bash
for file in *.txt; do
  mv "$file" "$(echo "$file" | sed 's/ /_/g')"
done
```

#### **Explicação**
- **`for file in *.txt`**: Itera sobre todos os arquivos `.txt`.
- **`sed 's/ /_/g'`**: Substitui espaços por underscores no nome do arquivo.

---

### **7. Extração de Dados Sensíveis**

#### **Descrição**
Extraia apenas e-mails de um arquivo para análise.

#### **Script**
```bash
#!/bin/bash
sed -n 's/.*\([a-zA-Z0-9._%+-]\+@[a-zA-Z0-9.-]\+\.[a-zA-Z]\{2,6\}\).*/\1/p' data.txt > emails_extraidos.txt
```

#### **Explicação**
- **Regex**: Captura e-mails no formato padrão.
- **Redirecionamento**: Salva os e-mails extraídos em `emails_extraidos.txt`.

---

### **8. Processar Tabelas para Markdown**

#### **Descrição**
Converta uma tabela CSV para o formato Markdown.

#### **Script**
```bash
#!/bin/bash
sed 's/,/ | /g;s/^/| /;s/$/ |/' tabela.csv > tabela.md
```

#### **Explicação**
- **`s/,/ | /g`**: Substitui vírgulas por ` | ` (delimitador Markdown).
- **`s/^/| /;s/$/ |`**: Adiciona barras verticais no início e no final de cada linha.
- **Redirecionamento**: Salva o resultado em `tabela.md`.

---

### **9. Geração de Inventários para Ansible**

#### **Descrição**
Formate uma lista de servidores e IPs como inventário do Ansible.

#### **Script**
```bash
#!/bin/bash
sed 's/\(.*\) ansible_host=\(.*\)/- name: \1\n  ip: \2/' hosts.txt > inventory.yml
```

#### **Explicação**
- **Regex**: Captura o nome do servidor e o IP.
- **Substituição**: Formata como YAML.

---

### **10. Validação de Arquivos Configuracionais**

#### **Descrição**
Encontre linhas inválidas em arquivos de configuração (`key=value`).

#### **Script**
```bash
#!/bin/bash
sed -n '/^[a-zA-Z_]\+=.*$/!p' config.conf > erros_config.txt
```

#### **Explicação**
- **`^[a-zA-Z_]\+=.*$`**: Seleciona linhas válidas.
- **`!p`**: Imprime linhas que **não** correspondem ao padrão.

---

### **Resumo**

| **Caso de Uso**               | **Script/Comando Principal**                                          |
|-------------------------------|------------------------------------------------------------------------|
| Geração de Backups            | `cp "$file" "${file%.txt}_$(date +%Y%m%d).bak"`                       |
| Limpeza de Dados              | `sed -i 's/[[:space:]]\+/ /g'`                                        |
| Relatórios CSV                | `sed 's/^Linha \([0-9]*\): \(.*\)/\1, "\2"/'`                         |
| Exportação de Logs Críticos   | `grep 'ERROR' | sed 's/\[ERROR\]/[CRITICAL]/g'`                       |
| Logs para JSON                | `sed 's/\[\(.*\)\] \(.*\): \(.*\)/.../'`                              |
| Renomear Arquivos             | `mv "$file" "$(echo "$file" | sed 's/ /_/g')"`                        |
| Extração de E-mails           | `sed -n 's/.../\1/p'`                                                 |
| Markdown para Tabelas         | `sed 's/,/ | /g;s/^/| /;s/$/ |/'`                                     |
| Inventários para Ansible      | `sed 's/.../- name: \1\n  ip: \2/'`                                   |
| Validação de Configuração     | `sed -n '/^[a-zA-Z_]\+=.*$/!p'`                                       |

---