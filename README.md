# 🚀 Gerenciamento de Autenticação com Keycloak, Nginx, Certbot e PostgreSQL

Este repositório contém uma configuração completa para rodar um ambiente seguro de autenticação baseado em **Keycloak** com **Nginx (proxy reverso)**, **Certbot (SSL)** e **PostgreSQL**.

## **📌 Tecnologias Utilizadas**

- **Keycloak** - Serviço de gerenciamento de identidades.
- **PostgreSQL** - Banco de dados para persistência dos dados do Keycloak.
- **Certbot + Cloudflare** - Geração e renovação automática de certificados SSL.
- **Nginx** - Proxy reverso para redirecionar tráfego para o Keycloak.
- **Ofelia** - Agendador para renovação automática do Certbot.
- **Docker + Docker Compose** - Contêinerização do ambiente.

---

## **📌 Passo a Passo para Execução**

### **1️⃣ Criar o arquivo `.env`**

Crie um arquivo `.env` na raiz do projeto e defina as credenciais para o Keycloak e PostgreSQL:

```ini
POSTGRES_USER=keycloak
POSTGRES_PASSWORD=uma_senha_forte
POSTGRES_DB=keycloak
KEYCLOAK_ADMIN=admin
KEYCLOAK_ADMIN_PASSWORD=uma_senha_segura
```

---

### **2️⃣ Criar o arquivo `cloudflare.ini`**

O Certbot precisa de um arquivo de credenciais para se comunicar com a API da Cloudflare.

Crie o arquivo **`cloudflare.ini`** na raiz do projeto e adicione:

```ini
# Credenciais da API Cloudflare
dns_cloudflare_api_token=SEU_CLOUDFLARE_API_TOKEN
```

⚠️ **Importante:**

- Substitua `SEU_CLOUDFLARE_API_TOKEN` pelo token da API Cloudflare.
- Aplique as permissões corretas para evitar que outras pessoas tenham acesso:
  ```sh
  chmod 600 cloudflare.ini
  ```

---

### **3️⃣ Construir e subir os contêineres**

Com tudo configurado, inicie os serviços com o seguinte comando:

```sh
docker compose up -d
```

Isso iniciará:

- Keycloak na porta `9000`
- Nginx na porta `443` (HTTPS) e `80` (HTTP)
- Certbot para emitir certificados SSL
- PostgreSQL para armazenar os dados do Keycloak
- Ofelia para automatizar a renovação do Certbot

---

### **4️⃣ Acessar o Keycloak**

Depois que todos os contêineres estiverem rodando, acesse o Keycloak via HTTPS:

```
https://auth.imaginer.com.br
```

- **Usuário Admin:** `admin`
- **Senha Admin:** `uma_senha_segura` (definida no `.env`)

Caso precise acessar diretamente o Keycloak via linha de comando:

```sh
docker exec -it keycloak bash
```

Para verificar os logs do Keycloak:

```sh
docker logs -f keycloak
```

---

## **📌 Renovação Automática de Certificados**

A renovação do certificado SSL é feita automaticamente a cada 12 horas pelo **Ofelia**.

Caso precise renovar manualmente, execute:

```sh
docker compose run --rm certbot certbot renew --dns-cloudflare --dns-cloudflare-credentials /cloudflare.ini --quiet
```

Para verificar logs do Certbot:

```sh
docker logs -f certbot
```

---

## **📌 Derrubar os Contêineres**

Se precisar parar e remover os contêineres:

```sh
docker compose down
```

Caso queira reiniciar com força:

```sh
docker compose down && docker compose up -d --force-recreate
```

---

## **📌 Estrutura do Projeto**

```
.
├── docker-compose.yml
├── nginx.conf
├── cloudflare.ini  # Arquivo de credenciais Cloudflare (chmod 600)
├── .env            # Variáveis de ambiente
└── README.md       # Documentação
```

Agora seu ambiente está pronto para rodar com **Keycloak, PostgreSQL, Certbot e Nginx**! 🚀🔥
