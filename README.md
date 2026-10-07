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

## 👥 Gestão de Equipe & Papéis (Roles)

O OJS separa as permissões entre o escopo da revista e o escopo do servidor:

### 1. Administrador da Revista (Gerente da Revista)
Responsável pela gestão diária da BMRT (equipe, design, pareceres, publicação).
* **Como adicionar pelo painel:**
  1. Acesse o painel da BMRT: **Configurações** -> **Usuários e Papéis** -> **Usuários**.
  2. Clique em **Adicionar Usuário**.
  3. Preencha os dados e marque:
     * ☑ **Gerente da revista** (*Journal manager*)
     * ☑ **Editor da revista** (*Journal editor*)
  4. Salve.

### 2. Administrador Global do Sistema (Root / Sysadmin)
Acesso de infraestrutura no servidor, capaz de criar novas revistas e gerenciar configurações globais.
* **Como promover um usuário existente via terminal:**
```bash
docker exec pkp_app_bmrt_ojs php -r '
require "/var/www/html/tools/bootstrap.php";
$user = \APP\facades\Repo::user()->getByUsername("NOME_DO_USUARIO");
if ($user) {
    $m = new mysqli("db", "pkp", "bmrt_db_pass_2026", "pkp");
    $m->query("INSERT IGNORE INTO user_user_groups (user_group_id, user_id) VALUES (1, " . $user->getId() . ")");
    echo "Usuário promovido a Administrador Global do Site!\n";
}
'
```

---

## 📂 Por que este repositório é focado em Docker e Shell?

O repositório adota o padrão moderno de **Infraestrutura como Código (IaC)**:

1. **Core do OJS empacotado:** Os mais de 100 MB de arquivos PHP, Smarty, Vue.js e bibliotecas do motor OJS já vêm testados, compilados e otimizados dentro da imagem oficial `pkpofficial/ojs:stable-3_5_0` fornecida pelo PKP. Versionar o core no repositório seria duplicar código de terceiros e dificultar atualizações de versão.
2. **Atualizações com 1 comando:** Para atualizar a versão do OJS, basta alterar a tag no arquivo `.env` e rodar `docker compose pull && docker compose up -d`.
3. **Onde entra código customizado (HTML, CSS, PHP)?**
   * **CSS Customizado:** Pode ser enviado diretamente pelo painel web da revista (*Configurações -> Website -> Aparência*).
   * **Temas Customizados:** Podem ser desenvolvidos mapeando uma pasta local para `./volumes/plugins/themes/seu-tema` (onde você programa arquivos `.tpl`, `.less`/`.css` e PHP do tema).
   * **Plugins Customizados:** Criados e versionados dentro de volumes mapeados em plugins.

---

## 🔒 Segurança

* O arquivo `.env` e as configurações ativas com chaves criptográficas (`volumes/config/pkp.config.inc.php`) **não são versionados** no Git.
* Arquivos binários do banco (`volumes/db/`) e documentos submetidos (`volumes/private/`) são ignorados via `.gitignore`.
