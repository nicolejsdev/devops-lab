# 💾 Storage & Filesystems

Laboratório prático de **armazenamento no Linux**: discos, partições, sistemas de arquivos, pontos de montagem e investigação de consumo de espaço com `du`, incluindo limpeza controlada de arquivos grandes e de revisões antigas do Snap.

## 🎯 Objetivo

Entender como o Linux organiza discos, partições, sistemas de arquivos e pontos de montagem, e praticar **troubleshooting de armazenamento**: descobrir quais diretórios e arquivos estão consumindo mais espaço.

Comandos praticados:

- `df`, `lsblk`, `findmnt` (visão geral do armazenamento)
- `du` (investigação de consumo de espaço)
- `snap list --all`, `snap remove` (revisões antigas de pacotes Snap)

## 🧰 Ambiente

- Distribuição: <!-- ex: Ubuntu 24.04 LTS -->
- Filesystem raiz: `ext4`
- Acesso: usuário com `sudo`

## 🛠️ Passo a passo

### 1. Visão geral do armazenamento

```bash
df -h          # espaço total, usado e disponível por filesystem
lsblk          # discos, partições e pontos de montagem
findmnt /      # onde a partição raiz está montada e com qual filesystem
```

A partição principal estava montada em `/` com o filesystem `ext4`.

![Disco e filesystems](screenshots/exercicio-01-disco-e-filesystems.png)

### 2. Descobrir o que consome espaço

Investigação de cima para baixo, aprofundando a cada passo:

```bash
sudo du -sh /* 2>/dev/null        # maiores diretórios da raiz
du -sh ~/* 2>/dev/null            # dentro do diretório pessoal
du -sh ~/Downloads/* 2>/dev/null  # dentro de Downloads
```

| Nível | O que encontrei |
|-------|-----------------|
| `/` | maior uso em `/home`, `/snap` e `/var` |
| `~` | `Downloads` com cerca de **7,9 GB** |
| `~/Downloads` | uma ISO do Windows 11 com cerca de **7,9 GB** |

![Investigação do uso do disco](screenshots/exercicio-02-investigacao-uso-disco.png)

### 3. Limpeza controlada

Com a ISO confirmada como desnecessária, o arquivo foi removido e o espaço verificado de novo:

```bash
rm ~/Downloads/<arquivo>.iso
df -h
```

| | Uso | Usado |
|---|:---:|:---:|
| Antes | 7% | ~29 GB |
| Depois | 5% | ~21 GB |

A remoção liberou aproximadamente **8 GB**.

### 4. Investigar o `/var`

```bash
sudo du -sh /var/* 2>/dev/null
sudo du -sh /var/lib/* 2>/dev/null
```

`/var/lib` era um dos que mais usavam espaço e, dentro dele, `/var/lib/snapd` era um dos principais consumidores.

### 5. Investigar o Snap

```bash
sudo du -sh /var/lib/snapd/* 2>/dev/null
sudo du -sh /snap/* 2>/dev/null
snap list --all
```

O Snap mantinha **revisões antigas** dos pacotes, marcadas como `disabled` no `snap list --all`. Elas foram removidas pelo próprio Snap, e não apagando arquivos à mão:

```bash
sudo snap remove <nome> --revision=<revisão>
snap list --all     # confirma que não há mais revisões disabled
```

## 🔍 Troubleshooting

Fluxo usado, baseado em evidências:

| Etapa | Comando | O que descobri |
|-------|---------|----------------|
| 1. Visão geral | `df -h` | quanto espaço estava em uso |
| 2. Maiores diretórios | `sudo du -sh /*` | `/home`, `/snap` e `/var` |
| 3. Aprofundar | `du -sh ~/*` e `du -sh ~/Downloads/*` | uma ISO de 7,9 GB |
| 4. Agir | `rm` no arquivo | limpeza controlada |
| 5. Confirmar | `df -h` | 7% → 5% de uso |

O Snap foi investigado em paralelo (passos 4 e 5 acima), seguindo o mesmo princípio: **identificar, agir e confirmar**.

## 🧠 Aprendizados

- `df` mostra o uso por filesystem e `du` mostra o uso por diretório. Um responde "onde está cheio" e o outro "quem encheu".
- A investigação funciona de cima para baixo: raiz → diretório → subdiretório → arquivo.
- Diretórios gerenciados pelo sistema (como `/var/lib/snapd`) devem ser limpos com a ferramenta do próprio gerenciador, nunca com `rm` manual.
- Antes de apagar, confirmar que o arquivo realmente não é mais necessário.
