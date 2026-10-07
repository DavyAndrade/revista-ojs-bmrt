# Revista BMRT - Open Journal Systems (OJS)

Ambiente containerizado oficial para a revista científica **Biomedical Model Research & Technologies (BMRT)**, utilizando o **Open Journal Systems (OJS 3.5 LTS)** mantido pelo Public Knowledge Project (PKP).

Este projeto viabiliza a publicação científica de acesso aberto (Diamond Open Access / sem taxas para autores) com gestão completa de submissões, avaliação por pares cega (*blind peer-review*), editoração e publicação de edições.

---

## 🚀 Arquitetura & Stack

* **OJS:** `pkpofficial/ojs:stable-3_5_0` (PHP 8.3 + Apache)
* **Banco de Dados:** MariaDB 11.8
* **SMTP Local / Testes:** Mailpit (capturador e visualizador web de e-mails transacionais)
* **Volumes Persistentes:**
  * `./volumes/db`: Dados do banco MariaDB
  * `./volumes/private`: Manuscritos, pareceres e arquivos sigilosos
  * `./volumes/public`: Capas de edições, logos e assets públicos
  * `./volumes/config`: Configurações de PHP, Apache e OJS

---

## 🛠️ Como Executar

### 1. Clonar o repositório
```bash
git clone https://github.com/DavyAndrade/revista-ojs-bmrt.git
cd revista-ojs-bmrt
```

### 2. Configurar variáveis de ambiente
Copie o arquivo de exemplo e ajuste se necessário:
```bash
cp .env.example .env
cp volumes/config/pkp.config.inc.php.example volumes/config/pkp.config.inc.php
```

### 3. Permissões de volumes (Linux)
Certifique-se de que as pastas de armazenamento tenham permissões adequadas:
```bash
chmod -R 777 ./volumes
```

### 4. Subir os containers
```bash
docker compose up -d
```

---

## 🌐 URLs de Acesso

* **Portal da Revista BMRT:** [http://localhost:8080/index.php/bmrt](http://localhost:8080/index.php/bmrt)
* **Painel de Submissões & Editorial:** [http://localhost:8080/index.php/bmrt/submissions](http://localhost:8080/index.php/bmrt/submissions)
* **Login Geral:** [http://localhost:8080/index.php/index/login](http://localhost:8080/index.php/index/login)
* **Webmail de Testes (Mailpit):** [http://localhost:8025](http://localhost:8025)

---

## 🔒 Segurança

* O arquivo `.env` e as configurações ativas com chaves criptográficas (`volumes/config/pkp.config.inc.php`) **não são versionados** no Git.
* Arquivos binários do banco (`volumes/db/`) e documentos submetidos (`volumes/private/`) são ignorados via `.gitignore`.
