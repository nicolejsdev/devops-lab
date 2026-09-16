# 💾 05 - Storage & Filesystems

## 🎯 Objetivo

Entender como o Linux organiza discos, partições, sistemas de arquivos e pontos de montagem.

O laboratório também teve como objetivo praticar *troubleshooting* de armazenamento, identificando quais diretórios e arquivos estavam consumindo mais espaço em disco.

---

## 💡 Conceitos Praticados

- Uso de espaço em disco
- Discos e partições
- Sistemas de arquivos
- Pontos de montagem
- Investigação de uso de disco
- Identificação de arquivos grandes
- Limpeza controlada
- Snap e revisões de pacotes
- Troubleshooting de armazenamento

---

## 1. 🔍 Análise do Disco e Filesystem

Foram utilizados os comandos `df`, `lsblk` e `findmnt` para analisar o armazenamento do sistema.

### 📊 Verificando o uso do disco
```bash
df -h

```

O comando `df -h` permite visualizar o espaço total, utilizado e disponível nos sistemas de arquivos.

### 💽 Visualizando discos e partições

```bash
lsblk

```

O comando `lsblk` permite visualizar os discos, partições e seus pontos de montagem.

### 📍 Verificando o ponto de montagem

```bash
findmnt /

```

O comando `findmnt /` foi utilizado para verificar onde a partição raiz está montada. A partição principal do sistema estava montada em `/` utilizando o filesystem `ext4`.

### 🖼️ Evidência

---

## 2. 🕵️‍♂️ Investigação do Uso de Espaço

Depois de verificar o estado geral do disco, foi realizada uma investigação para descobrir quais diretórios estavam ocupando mais espaço.

O primeiro comando utilizado foi:

```bash
sudo du -sh /* 2>/dev/null

```

A análise mostrou maior utilização em diretórios como:

* 📂 `/home`
* 📂 `/snap`
* 📂 `/var`

A investigação foi então aprofundada.

### 👤 Investigando o diretório pessoal

```bash
du -sh ~/* 2>/dev/null

```

Foi identificado que o diretório `Downloads` utilizava aproximadamente **7,9 GB**.

### 📥 Investigando Downloads

```bash
du -sh ~/Downloads/* 2>/dev/null

```

Foi identificado um arquivo ISO do Windows utilizando aproximadamente **7,9 GB**:

*  `Win11_25H2_Portuguese_x64_v2.iso`

---

## 3. 🧹 Limpeza Controlada

Após verificar que a ISO não era mais necessária, o arquivo foi removido:

```bash
rm ~/Downloads/Win11_25H2_Portuguese_x64_v2.iso

```

Depois da remoção, o espaço disponível foi verificado novamente:

```bash
df -h

```

### 📈 Resultado

* 🔴 **Antes da limpeza:**
* Uso: **7%**
* Usado: **~29 GB**


* 🟢 **Depois da limpeza:**
* Uso: **5%**
* Usado: **~21 GB**



✨ A remoção liberou aproximadamente **8 GB** de espaço!

---

## 4. 📂 Investigação do `/var`

Também foi investigado o diretório `/var` para entender onde o espaço estava sendo utilizado:

```bash
sudo du -sh /var/* 2>/dev/null

```

A análise mostrou que `/var/lib` era um dos diretórios que mais utilizavam espaço.
A investigação continuou:

```bash
sudo du -sh /var/lib/* 2>/dev/null

```

Foi identificado o diretório `/var/lib/snapd` como um dos principais consumidores de espaço.

---

## 5. 🧩 Investigação do Snap

O armazenamento relacionado ao Snap foi analisado com:

```bash
sudo du -sh /var/lib/snapd/* 2>/dev/null
sudo du -sh /snap/* 2>/dev/null

```

Durante a investigação, foi identificado que o Snap mantinha algumas revisões antigas dos pacotes instalados. Para verificar as versões instaladas e antigas:

```bash
snap list --all

```

As revisões antigas apareciam com a indicação `disabled`. Essas revisões foram removidas utilizando o próprio Snap:

```bash
sudo snap remove <nome> --revision=<revisão>

```

Após a limpeza, uma nova verificação foi realizada:

```bash
snap list --all

```

As revisões antigas `disabled` foram removidas com sucesso.

### 🖼️ Evidência

---

## 6. 🔄 Abordagem de Troubleshooting

A investigação seguiu uma sequência lógica baseada em evidências:

```text
df -h
  ↓ (Verificação do espaço utilizado)
sudo du -sh /*
  ↓ (Identificação dos maiores diretórios)
du -sh ~/*
  ↓ (Identificação de Downloads)
du -sh ~/Downloads/*
  ↓ (Identificação da ISO de 7,9 GB)
rm ~/Downloads/Win11_25H2_Portuguese_x64_v2.iso
  ↓ (Remoção controlada)
df -h
  ↓ (Verificação do resultado)

```

Também foi realizada uma investigação independente do armazenamento utilizado pelo Snap.

---

## 7. 💻 Comandos Utilizados

```bash
df -h
lsblk
findmnt /
sudo du -sh /*
du -sh ~/*
du -sh ~/Downloads/*
sudo du -sh /var/*
sudo du -sh /var/lib/*
sudo du -sh /var/lib/snapd/*
sudo du -sh /snap/*
snap list --all
sudo snap remove <nome> --revision=<revisão>

```

---

## 8. 🧠 O Que Aprendi

* Como verificar o espaço utilizado e disponível em um filesystem.
* Como visualizar discos e partições.
* Como identificar pontos de montagem.
* Como investigar o consumo de espaço utilizando `du`.
* Como localizar arquivos grandes.
* Como realizar uma limpeza de forma controlada.
* Como investigar o armazenamento utilizado pelo Snap.
* Como identificar revisões antigas de pacotes.
* A importância de não apagar manualmente arquivos de diretórios gerenciados pelo sistema.
* Como utilizar uma sequência de diagnóstico para resolver problemas de armazenamento.

---

### 📸 9. Evidências

As evidências deste laboratório estão disponíveis no diretório `screenshots/`.

#### 🖼️ Exercício 01 — Disco e Filesystems

![Exercício 01 - Disco e Filesystems](screenshots/exercicio-01-disco-e-filesystems.png)

#### 🖼️ Exercício 02 — Investigação do uso do disco

![Exercício 02 - Investigação do uso do disco](screenshots/exercicio-02-investigacao-uso-disco.png)
```

```
