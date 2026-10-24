# 👤 Users & Permissions

Laboratório prático de gerenciamento de **usuários, grupos e permissões no Linux**, simulando um ambiente em que diferentes pessoas têm diferentes níveis de acesso a um mesmo recurso.

## 🎯 Objetivo

Entender **por que um usuário consegue ou não acessar determinado recurso**, praticando:

- criação de usuários e grupos (`useradd`, `groupadd`, `usermod`)
- proprietário e grupo de arquivos (`chown`)
- permissões de leitura, escrita e execução (`chmod`)
- verificação de identidade e acesso (`whoami`, `id`, `groups`, `getent`)
- troubleshooting de `Permission denied`

## 🧰 Ambiente

- Distribuição: <!-- ex: Ubuntu 24.04 LTS -->
- Acesso: usuário com `sudo`

## 🛠️ Passo a passo

### 1. Criar usuários e grupo

```bash
sudo useradd -m maria
sudo useradd -m joao
sudo useradd -m carlos
sudo groupadd leitores
sudo usermod -aG leitores joao
```

Verificação:

```bash
id joao
getent group leitores
```

### 2. Criar o recurso de teste

```bash
sudo mkdir -p /srv/empresa
sudo touch /srv/empresa/relatorio.txt
sudo chown maria:leitores /srv/empresa/relatorio.txt
sudo chmod 640 /srv/empresa/relatorio.txt
ls -l /srv/empresa/relatorio.txt
```

Resultado esperado:

```text
-rw-r----- 1 maria leitores 0 ... relatorio.txt
```

### 3. Testar o acesso de cada usuário

| Usuário | Relação com o arquivo | Leitura | Escrita |
|---------|-----------------------|:-------:|:-------:|
| `maria` | proprietária          | ✅      | ✅      |
| `joao`  | membro do grupo `leitores` | ✅ | ❌      |
| `carlos`| outros                | ❌      | ❌      |

```bash
sudo -u joao cat /srv/empresa/relatorio.txt      # funciona
sudo -u joao sh -c 'echo teste >> /srv/empresa/relatorio.txt'   # Permission denied
sudo -u carlos cat /srv/empresa/relatorio.txt    # Permission denied
```

## 🔍 Troubleshooting

Quando aparece `Permission denied`, investigo nesta ordem:

```bash
whoami                      # quem eu sou?
id                          # a quais grupos pertenço?
ls -l <arquivo>             # quem é o dono, qual o grupo e quais as permissões?
ls -ld <diretório>          # tenho permissão de execução (x) no diretório?
```

| Erro | Causa | Solução |
|------|-------|---------|
| `Permission denied` ao ler | usuário não é dono nem está no grupo | adicionar ao grupo (`usermod -aG`) ou ajustar o `chmod` |
| `Permission denied` ao escrever | permissão de escrita (`w`) ausente para o usuário/grupo | ajustar `chmod` ou `chown` conforme a necessidade |
| Senha rejeitada ao definir com `passwd` | senha não atende à política de complexidade | usar senha mais forte |

## 🧹 Limpeza do ambiente

```bash
sudo userdel -r maria
sudo userdel -r joao
sudo userdel -r carlos
sudo groupdel leitores
sudo rm -rf /srv/empresa
```

Verificação:

```bash
getent passwd maria joao carlos   # sem saída = usuários removidos
getent group leitores             # sem saída = grupo removido
```

## 📸 Evidências

![Execução do laboratório](screenshots/exercicio-02-users-permissions.png)

![Limpeza do ambiente](screenshots/limpeza-ambiente.png)

## 🧠 Aprendizados

- Permissão no Linux é avaliada em camadas: **dono → grupo → outros**.
- Adicionar um usuário a um grupo só vale em novas sessões (`id` mostra o estado real).
- Para acessar um arquivo, o usuário também precisa de `x` em todos os diretórios do caminho.
- Diante de um erro, investigar a causa (`id`, `ls -l`) antes de sair alterando permissões.
