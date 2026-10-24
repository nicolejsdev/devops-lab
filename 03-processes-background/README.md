# ⚙️ Processes & Background

Laboratório prático de **gerenciamento de processos no Linux**: ciclo de vida, estados, sinais, jobs em background, prioridade e inspeção via `/proc`.

## 🎯 Objetivo

Entender **como identificar o que está executando no sistema e como controlar um processo**, praticando:

- criação e identificação de processos (PID, PPID)
- foreground, background e suspensão (`Ctrl+Z`, `jobs`, `bg`, `fg`)
- estados de processo (`R`, `S`, `T`, `Z`)
- envio de sinais e exit codes (`kill`, `$?`)
- monitoramento (`ps`, `top`) e inspeção em `/proc`

## 🧰 Ambiente

- Distribuição: <!-- ex: Ubuntu 24.04 LTS -->
- Shell: `bash`
- Pacotes usados: `procps` (`ps`, `top`, `pgrep`), `psmisc` (`pstree`)

## 🧠 Conceitos básicos

| Conceito | Descrição |
|----------|-----------|
| **PID** | identificador único do processo |
| **PPID** | PID do processo pai (quem o criou) |
| **Job** | processo controlado pelo shell atual (`jobs`, `%1`) |
| **Sinal** | mensagem enviada ao processo (`SIGTERM`, `SIGSTOP`...) |
| **Exit code** | número retornado ao terminar (`0` = sucesso) |

### Estados de processo (coluna `STAT` do `ps`)

| Estado | Significado |
|:------:|-------------|
| `R` | executando ou pronto para executar |
| `S` | dormindo, aguardando evento (ex.: `sleep`) |
| `D` | espera ininterrupta, geralmente I/O (não responde a sinais) |
| `T` | parado/suspenso (`Ctrl+Z`, `SIGSTOP`) |
| `Z` | zumbi: terminou, mas o pai ainda não leu o exit code |

## 🛠️ Passo a passo

### 1. Criar um processo e suspendê-lo

```bash
sleep 300
# pressione Ctrl + Z
jobs -l
```

Resultado esperado:

```text
[1]+ 14107 Stopped                 sleep 300
```

O `-l` já mostra o PID. O estado `Stopped` corresponde a `T` no `ps`.

### 2. Retomar em background

```bash
bg %1
jobs -l
```

```text
[1]+ 14107 Running                 sleep 300 &
```

Para trazer de volta ao foreground: `fg %1`.

### 3. Identificar o processo

```bash
pgrep -a sleep
ps -o pid,ppid,stat,etime,cmd -C sleep
```

```text
    PID    PPID STAT     ELAPSED CMD
  14107    2210 S          01:12 sleep 300
```

Alternativa (o `[s]` evita que o próprio `grep` apareça):

```bash
ps -ef | grep '[s]leep'
```

Observe que o `PPID` é o PID do seu shell (confirme com `echo $$`).

### 4. Inspecionar o processo em `/proc`

```bash
PID=$(pgrep -n sleep)
grep -E 'Name|State|PPid' /proc/$PID/status
ls -l /proc/$PID/exe
cat /proc/$PID/cmdline | tr '\0' ' '; echo
```

```text
Name:   sleep
State:  S (sleeping)
PPid:   2210
```

> `/proc` é um pseudo-sistema de arquivos: o kernel expõe ali o estado de cada processo em tempo real.

### 5. Encerrar com sinal e verificar o exit code

```bash
kill $PID                  # envia SIGTERM (15)
jobs                       # [1]+ Terminated  sleep 300
pgrep -a sleep             # sem saída = finalizado
```

Para ver o exit code, rode o processo com `&` e use `wait`:

```bash
sleep 300 &
PID=$!
kill $PID
wait $PID; echo $?
```

```text
143
```

`143 = 128 + 15`: o processo morreu por `SIGTERM`. Com `kill -9`, o código seria `137` (128 + 9).

## 📊 Monitoramento com `top`

```bash
top
```

| Coluna | Significado |
|--------|-------------|
| `PID` | identificador do processo |
| `USER` | usuário dono |
| `PR` / `NI` | prioridade / valor de *nice* |
| `S` | estado (`R`, `S`, `T`, `Z`...) |
| `%CPU` / `%MEM` | uso de CPU e memória |
| `TIME+` | tempo total de CPU consumido |
| `COMMAND` | comando de origem |

Atalhos: `P` ordena por CPU, `M` por memória, `k` envia sinal a um PID, `1` mostra cada core, `q` sai.

No cabeçalho, o **load average** (1, 5 e 15 min) indica quantos processos estão na fila de execução. Valores constantemente acima do número de cores sugerem sobrecarga.

## 📡 Sinais mais comuns

| Sinal | Nº | Efeito | Pode ser capturado? |
|-------|:--:|--------|:-------------------:|
| `SIGTERM` | 15 | pede encerramento limpo (padrão do `kill`) | ✅ |
| `SIGINT` | 2 | interrupção (`Ctrl+C`) | ✅ |
| `SIGTSTP` | 20 | suspensão pelo terminal (`Ctrl+Z`) | ✅ |
| `SIGSTOP` | 19 | suspende à força | ❌ |
| `SIGCONT` | 18 | retoma processo suspenso | ✅ |
| `SIGKILL` | 9 | encerra à força | ❌ |

`SIGKILL` e `SIGSTOP` não podem ser capturados nem ignorados. Por isso `kill -9` deve ser o **último recurso**: o processo não libera recursos nem salva estado.

## 🔍 Troubleshooting

> Um processo está em background e preciso encerrá-lo. Como proceder?

| Etapa | Comando | O que verifico |
|-------|---------|----------------|
| 1. Localizar | `jobs -l` / `pgrep -a <nome>` | PID e comando |
| 2. Inspecionar | `ps -o pid,ppid,stat,etime,cmd -p <PID>` | estado, pai e tempo de execução |
| 3. Encerrar | `kill <PID>` | pedido educado (SIGTERM) |
| 4. Confirmar | `pgrep -a <nome>` | sem saída = encerrado |
| 5. Escalar | `kill -9 <PID>` | só se o passo 3 não funcionar |

## 🧪 Extensões do laboratório

> Seção opcional: inclua apenas o que você executar.

### Prioridade com `nice`

```bash
nice -n 10 sleep 300 &
ps -o pid,ni,cmd -C sleep       # NI = 10
renice -n 15 -p $(pgrep -n sleep)
```

Valores de `nice` vão de `-20` (maior prioridade) a `19` (menor). Só o root pode diminuir o valor.

### Suspender e retomar por sinal

```bash
sleep 300 &
kill -STOP %1; ps -o pid,stat,cmd -C sleep    # STAT = T
kill -CONT %1; ps -o pid,stat,cmd -C sleep    # STAT = S
```

### Árvore de processos

```bash
pstree -p $$
ps -ef --forest | head -20
```

### Sobreviver ao fechamento do terminal

```bash
nohup sleep 300 > /dev/null 2>&1 &
disown
```

Sem `nohup`/`disown`, o shell envia `SIGHUP` aos jobs ao fechar, e eles encerram.

## 📸 Evidências

### Processos em background

![Processos em background](screenshots/exercicio-01-processos-background.png)

### Monitoramento com top

![Monitoramento com top](screenshots/exercicio-02-top.png)

## 🧠 Aprendizados

- Todo processo tem **PID**, **PPID** e um **estado**; o kernel expõe tudo em `/proc`.
- `kill` **envia um sinal**; o padrão (`SIGTERM`) permite encerramento limpo.
- Exit code `128 + N` indica término por sinal `N`.
- `jobs` enxerga só a sessão atual do shell; para o sistema todo, use `ps` e `top`.
- Fluxo de troubleshooting: localizar, inspecionar, agir e **confirmar o resultado**.

