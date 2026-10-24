# 🔧 Services

Laboratório prático de **gerenciamento de serviços no Linux com `systemd`**: iniciar, parar, reiniciar, habilitar e desabilitar o `ssh.service`, observando a diferença entre o estado atual do serviço e a sua configuração de boot.

## 🎯 Objetivo

Entender a diferença entre um serviço estar **executando** e estar **habilitado para iniciar automaticamente**, praticando:

- controle de serviços (`systemctl start | stop | restart`)
- inicialização automática (`systemctl enable | disable | is-enabled`)
- leitura de status (`systemctl status`)
- identificação do processo (PID) associado ao serviço
- relação entre unidades do `systemd` (`ssh.service` e `ssh.socket`)

## 🧰 Ambiente

- Distribuição: <!-- ex: Ubuntu 24.04 LTS -->
- Serviço de teste: `ssh.service`
- Acesso: usuário com `sudo`

> ⚠️ Em uma máquina acessada via SSH, parar ou desabilitar o serviço pode derrubar seu acesso. Faça o lab em uma VM ou máquina local.

## 🛠️ Passo a passo

### 1. Iniciar o serviço

```bash
sudo systemctl start ssh
systemctl status ssh
```

Resultado esperado:

```text
Active: active (running)
```

### 2. Parar o serviço

```bash
sudo systemctl stop ssh
systemctl status ssh
```

Resultado esperado:

```text
Active: inactive (dead)
```

Observação: mesmo com o `ssh.service` parado, o `ssh.socket` continuou ativo, mostrando que um serviço pode estar relacionado a outras unidades do `systemd`.

### 3. Reiniciar o serviço

```bash
sudo systemctl restart ssh
systemctl status ssh
```

Resultado esperado:

```text
Active: active (running)
```

O processo `sshd` recebeu um **novo PID** após o reinício (compare o `Main PID` do `status` antes e depois).

### 4. Habilitar a inicialização automática

```bash
sudo systemctl enable ssh
systemctl is-enabled ssh
```

Resultado esperado:

```text
enabled
```

### 5. Desabilitar a inicialização automática

```bash
sudo systemctl disable ssh
systemctl is-enabled ssh
```

Resultado esperado:

```text
disabled
```

### 6. Restaurar o ambiente

O serviço foi habilitado novamente para os próximos laboratórios:

```bash
sudo systemctl enable ssh
systemctl is-enabled ssh
```

## 🔀 Start x Enable

| Comando | Efeito agora | Efeito no boot |
|---------|:------------:|:--------------:|
| `start` | inicia o serviço | nenhum |
| `stop` | para o serviço | nenhum |
| `enable` | nenhum | inicia automaticamente |
| `disable` | nenhum | deixa de iniciar |

Ou seja, **estar rodando** e **estar habilitado** são coisas independentes.

## 🔍 Troubleshooting

Durante os testes, as mensagens do `status` mencionaram o `ssh.socket`, o que mostrou que o serviço depende de outras unidades.

Fluxo de investigação usado:

| Etapa | Ação |
|-------|------|
| 1. Verificar | `systemctl status ssh` |
| 2. Analisar | ler o campo `Active` e as unidades relacionadas |
| 3. Agir | `start`, `stop` ou `restart` |
| 4. Confirmar | `systemctl status ssh` novamente |

## 📸 Evidências

### Status do serviço
![Status do serviço](screenshots/exercicio-01-service-status.png)

### Parando o serviço
![Parando o serviço](screenshots/exercicio-02-stop-service.png)

### Reiniciando o serviço
![Reiniciando o serviço](screenshots/exercicio-03-restart-service.png)

### Habilitando o serviço
![Habilitando o serviço](screenshots/exercicio-04-enable-service.png)

### Desabilitando o serviço
![Desabilitando o serviço](screenshots/exercicio-05-disable-service.png)

## 🧠 Aprendizados

- **Active** (estado atual) e **enabled** (boot) são independentes.
- `restart` gera um novo PID para o processo `sshd`.
- Um serviço pode ter unidades relacionadas, como o `ssh.socket`.
- Raciocínio para diagnóstico: serviço → processo → PID → porta → acesso.

