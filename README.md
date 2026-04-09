# n8n Automation Stack (Queue Mode)

Este repositório contém a configuração completa para subir uma instância de alta performance e escalável do **n8n** utilizando o modo de fila (**Queue Mode**). Essa arquitetura é ideal para ambientes de produção onde a estabilidade e o processamento paralelo são fundamentais.

---

## 🏗️ Arquitetura da Stack

A stack é composta por 4 serviços principais:

1.  **n8n (Main/Editor):** A interface principal onde você constrói seus fluxos. Ele gerencia as credenciais e o agendamento, mas delega a execução pesada para os workers.
2.  **n8n-worker:** O "trabalhador" que executa os fluxos de automação. Você pode escalar este serviço para rodar múltiplos workers se necessário.
3.  **Redis:** Atua como o "Broker" de mensagens, gerenciando a fila de tarefas que o n8n envia para os workers.
4.  **PostgreSQL:** O banco de dados relacional que armazena os fluxos, usuários, credenciais e histórico de execuções.

---

## 🛠️ Pré-requisitos

- **Docker** e **Docker Compose** instalados.
- Um domínio ou subdomínio configurado (ex: `n8n.seudominio.com`).
- Proxy Reverso (Cloudflare Tunnel, Traefik ou Nginx) para gerenciamento de SSL/HTTPS.

---

## ⚙️ Configuração (Variáveis de Ambiente)

Antes de subir os serviços, você deve configurar o arquivo `.env`. Abaixo está o detalhamento das variáveis:

| Variável | Descrição | Exemplo / Dica |
| :--- | :--- | :--- |
| `POSTGRES_USER` | Usuário do Banco de Dados | `n8n_admin` |
| `POSTGRES_PASSWORD` | Senha do Banco de Dados | *Use uma senha forte* |
| `N8N_ENCRYPTION_KEY` | Chave de criptografia para credenciais | Gere uma com `openssl rand -hex 24` |
| `DOMAIN_NAME` | Seu subdomínio para acesso externo | `n8n.meuprojeto.com` |
| `TZ` | Fuso horário do sistema | `America/Sao_Paulo` |

---

## 🚀 Passo a Passo para Subir a Stack

### 1. Configurar o arquivo `.env`
Abra o arquivo `.env` e preencha as informações necessárias. 
> **Dica:** Para gerar a chave de criptografia no terminal:
> ```bash
> openssl rand -hex 24
> ```

### 2. Subir os Serviços
Execute o comando abaixo para baixar as imagens e iniciar os containers em segundo plano:

```bash
docker-compose up -d
```

### 3. Verificar o Status
Certifique-se de que todos os containers estão saudáveis:

```bash
docker-compose ps
```

### 4. Acesso
Após alguns segundos, acesse o endereço configurado em `DOMAIN_NAME` no seu navegador.

---

## 💾 Manutenção e Logs

### Visualizar Logs
Para acompanhar o que está acontecendo em tempo real:
```bash
docker-compose logs -f n8n
```

### Reiniciar um Serviço Específico
Caso precise reiniciar apenas o worker:
```bash
docker-compose restart n8n-worker
```

### Limpeza de Dados (Pruning)
Por padrão, o n8n armazena muitas informações de execução. É altamente recomendado adicionar as variáveis de `PRUNE` mencionadas na análise técnica.

---

## 🛡️ Segurança e Melhores Práticas

1.  **Backup:** Faça backup regular do volume `postgres_data`.
2.  **Versões:** Evite usar a tag `:latest` em produção. Prefira versões estáveis.
3.  **Criptografia:** Nunca mude a `N8N_ENCRYPTION_KEY` após configurar a instância, ou você perderá o acesso às suas credenciais salvas.
