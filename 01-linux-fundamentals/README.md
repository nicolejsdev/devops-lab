# 🐧 Linux Fundamentals

Laboratório prático de Linux desenvolvido para consolidar os fundamentos da linha de comando (CLI), a navegação pelo filesystem e as operações básicas com arquivos e diretórios.

A proposta foi aprender fazendo: executar comandos, observar os resultados, investigar erros e entender como cada operação funciona no ambiente Linux.

## 🎯 Objetivos

- Praticar a navegação pelo filesystem Linux.
- Compreender a diferença entre caminhos absolutos e relativos.
- Criar e organizar arquivos e diretórios.
- Realizar operações de cópia, movimentação e remoção.
- Localizar arquivos utilizando o `find`.
- Interpretar mensagens de erro e investigar possíveis causas.

## 🛠️ Tecnologias e comandos

**Ambiente:** Linux Ubuntu · Terminal Bash

| Comando | Aplicação prática |
|---|---|
| `pwd` | Verificar o diretório atual |
| `ls` | Listar arquivos e diretórios |
| `cd` | Navegar pelo filesystem |
| `mkdir` | Criar diretórios |
| `touch` | Criar arquivos |
| `cp` | Copiar arquivos e diretórios |
| `mv` | Mover ou renomear arquivos e diretórios |
| `rm` | Remover arquivos e diretórios |
| `find` | Localizar arquivos e diretórios |
| `tree` | Visualizar a estrutura em formato de árvore |
| `cat` | Exibir o conteúdo de arquivos de texto |

## 🧪 Exercício 01 — Navegação e organização do filesystem

O primeiro exercício consistiu em criar uma estrutura de diretórios, adicionar arquivos de log e praticar operações comuns de gerenciamento de arquivos.

### Estrutura do exercício

```text
lab-linux/
├── logs/
├── scripts/
├── backups/
├── config/
└── reports/
```

Durante a prática, foram criados arquivos, realizadas cópias e movimentações e utilizadas ferramentas de busca para verificar a organização do ambiente.

### Estrutura final do laboratório

```text
01-linux-fundamentals/
├── backups/
│   └── app.log
├── config/
├── logs/
│   ├── app.log
│   └── system.log
├── reports/
│   └── error.log
├── screenshots/
│   └── exercicio-01-filesystem.png
└── scripts/
```

A organização separa os arquivos por finalidade:

- `logs/` — arquivos de log.
- `scripts/` — espaço destinado a scripts.
- `backups/` — cópias de arquivos.
- `config/` — arquivos de configuração.
- `reports/` — relatórios e arquivos relacionados.
- `screenshots/` — evidências da execução prática.

Durante o exercício, o arquivo `app.log` foi copiado para `backups/`, e o arquivo `error.log` foi organizado em `reports/`.

## 📍 Caminhos absolutos e relativos

Outro ponto importante foi compreender como o diretório atual influencia a execução dos comandos.

### Caminho absoluto

Um caminho absoluto identifica a localização de um recurso a partir da raiz do sistema (`/`).

```bash
/home/nicole/01-linux-fundamentals
```

Esse caminho não depende do diretório atual para identificar o destino.

### Caminho relativo

Um caminho relativo utiliza o diretório atual como referência.

```bash
../backups
```

Nesse exemplo, `..` representa o diretório pai.

Outro exemplo:

```bash
./logs
```

Nesse caso, `.` representa o diretório atual.

Esses conceitos foram utilizados nas operações com arquivos e diretórios, ajudando a entender por que um mesmo caminho pode funcionar em um contexto e falhar em outro.

## 🔎 Localização de arquivos

O comando `find` foi utilizado para procurar arquivos com a extensão `.log`.

```bash
find .. -name "*.log"
```

O comando procura arquivos e diretórios com nomes correspondentes ao padrão informado, a partir do diretório indicado. Nesse caso, a busca começa em `..`, ou seja, no diretório pai.

Essa prática ajudou a compreender como localizar arquivos sem precisar percorrer manualmente toda a estrutura.

## 🌳 Visualização da estrutura

Para conferir a organização final, utilizei:

```bash
tree
```

A visualização em árvore facilitou a identificação dos diretórios e arquivos existentes e ajudou a verificar se a estrutura estava organizada conforme o planejado.

## 🐛 Erros encontrados e troubleshooting

Durante a execução dos exercícios, alguns comandos não produziram o resultado esperado. Em vez de simplesmente repetir as instruções, investiguei o contexto da execução e procurei entender o motivo de cada erro.

### 1. Diretório não encontrado

Comando utilizado:

```bash
cd lab-linux/logs
```

O terminal retornou uma mensagem informando que o diretório não foi encontrado.

**O que aconteceu?**

O caminho relativo foi interpretado a partir do diretório atual, e a estrutura informada não correspondia à localização em que o comando foi executado.

**Como investigar?**

```bash
pwd
ls
```

O `pwd` permite conferir a localização atual, enquanto o `ls` ajuda a verificar os arquivos e diretórios disponíveis.

**Aprendizado:** antes de utilizar um caminho relativo, é importante entender de onde o comando está sendo executado.

### 2. Tentativa de executar um diretório

Comando digitado:

```bash
logs/
```

O Bash tentou interpretar a entrada como um comando a ser executado, em vez de utilizá-la para navegar até o diretório.

Para entrar no diretório, o comando correto é:

```bash
cd logs/
```

**Aprendizado:** informar um caminho não é o mesmo que executar uma operação. O comando utilizado determina o que o shell tentará fazer.

### 3. Tentativa de executar um arquivo de log

Comando digitado:

```bash
../backups/app.log
```

O terminal retornou:

```text
Permission denied
```

O caminho foi interpretado como uma tentativa de execução do arquivo. Como se tratava de um arquivo de log, e não de um programa destinado à execução, essa não era a operação desejada.

Para visualizar seu conteúdo, utilizei:

```bash
cat ../backups/app.log
```

**Aprendizado:** arquivos podem ter finalidades diferentes. Para trabalhar com seu conteúdo, é necessário utilizar um comando apropriado à operação desejada.

## 🔍 Como abordei os erros

Essas situações ajudaram a desenvolver o hábito de investigar antes de executar novamente um comando.

Ao encontrar um problema, procurei verificar:

- Qual era o diretório atual?
- O caminho informado estava correto?
- O arquivo ou diretório existia naquele local?
- O comando correspondia à operação desejada?
- O que a mensagem de erro indicava?

Essa abordagem faz parte do troubleshooting: observar o comportamento do sistema, levantar possíveis causas e testar uma solução.

## 📸 Evidência da execução

A imagem abaixo registra a prática realizada no terminal e a estrutura de arquivos criada durante o exercício.

![Execução do exercício 01 — Navegação e filesystem](screenshots/exercicio-01-filesystem.png)

## 🧠 Principais aprendizados

Ao concluir este laboratório, pratiquei:

- Navegação pelo filesystem Linux.
- Utilização de comandos básicos no terminal.
- Manipulação e organização de arquivos e diretórios.
- Diferenças entre caminhos absolutos e relativos.
- Localização de arquivos com `find`.
- Verificação da estrutura com `tree`.
- Interpretação de mensagens de erro.
- Investigação de problemas relacionados a caminhos e execução de comandos.

O principal aprendizado foi perceber que trabalhar com Linux vai além de memorizar comandos. É preciso entender o contexto em que eles são executados, observar os resultados e investigar quando algo não funciona como esperado.
