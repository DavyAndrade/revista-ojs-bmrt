# Graphify — Arquitetura & Fluxos da Revista BMRT (OJS)

Este documento mapeia visualmente todos os componentes de infraestrutura, fluxos editoriais e entidades do sistema da revista **Biological Models Research and Technology (BMRT)**.

---

## 1. Grafo de Arquitetura de Infraestrutura (System Architecture)

```mermaid
graph TD
    subgraph "1. Camada de Borda & DNS"
        DNS["🌐 Domínio Oficial BMRT\n(Cloudflare SSL / HTTPS)"]
        ReverseProxy["🛡️ Proxy Reverso\n(Nginx / Traefik - Portas 80/443)"]
    end

    subgraph "2. Docker Stack (Revista BMRT)"
        OJS["🖥️ PKP OJS 3.5 LTS Container\n(PHP 8.3 + Apache MPM)\nPorta Interna 80"]
        DB[("🗄️ MariaDB 11.8 Container\n(Engine InnoDB)\nPorta Interna 3306")]
        Mail["📧 SMTP Transacional\n(Mailpit em Dev / Brevo em Prod)\nPortas 1025 / 8025"]
    end

    subgraph "3. Armazenamento Persistente (Host Volumes)"
        VolConfig["📁 ./volumes/config\n(pkp.config.inc.php, php.custom.ini)"]
        VolDB["📁 ./volumes/db\n(Dados do banco MariaDB)"]
        VolPrivate["📁 ./volumes/private\n(Manuscritos e Pareceres Sigilosos)"]
        VolPublic["📁 ./volumes/public\n(Capas, Logos, Banners, styleSheet.css)"]
    end

    subgraph "4. Serviços Externos & Indexação Acadêmica"
        GoogleScholar["🎓 Google Scholar\n(Highwire & Dublin Core Metatags)"]
        Crossref["🆔 Crossref\n(Depósito de DOI)"]
        ORCID["👤 ORCID API\n(Autenticação de Pesquisadores)"]
        OAIPMH["📚 OAI-PMH Harvester\n(Indexação IBICT / Diadorim / Scielo)"]
    end

    DNS --> ReverseProxy
    ReverseProxy -->|Proxy HTTP| OJS

    OJS -->|SQL mysqli| DB
    OJS -->|SMTP Port 1025| Mail

    OJS --- VolConfig
    OJS --- VolPrivate
    OJS --- VolPublic
    DB --- VolDB

    OJS -.->|Exporta Metatags| GoogleScholar
    OJS -.->|API REST| Crossref
    OJS -.->|OAuth| ORCID
    OJS -.->|XML Protocol| OAIPMH
```

---

## 2. Grafo do Pipeline Editorial (Fluxo de Artigos BMRT)

```mermaid
stateDiagram-v2
    [*] --> SubmissaoIniciada: Autor preenche metadados
    
    state SubmissaoIniciada {
        UploadManuscrito: Upload do manuscrito anonimizado
        UploadCEUA: Anexo de aprovação do Comitê de Ética (CEUA)
        ConfirmacaoEnvio: Disparo de e-mail de confirmação (Mailpit)
    }

    SubmissaoIniciada --> TriagemEditorial: Fila de Submissões Não Designadas

    state TriagemEditorial {
        AnaliseEscopo: Editores-Chefes (Marcel / Cláudia) avaliam escopo
        DecisaoPreliminar: Verificar conformidade técnica e ética animal
    }

    TriagemEditorial --> RejeicaoDesktop: Fora do escopo / Sem CEUA
    RejeicaoDesktop --> [*]: Autor notificado por e-mail

    TriagemEditorial --> AvaliacaoPorPares: Artigo aceito para avaliação

    state AvaliacaoPorPares {
        DesignarAvaliadores: Editor seleciona pareceristas
        ConviteEmail: E-mail com link de aceite (1 clique)
        PareceristaAceita: Acesso cego ao manuscrito
        PreenchimentoParecer: Formulário com critérios metodológicos
        EmissaoRecomendacao: Aceitar / Correções Requeridas / Rejeitar
    }

    AvaliacaoPorPares --> SolicitacaoRevisoes: Correções solicitadas
    SolicitacaoRevisoes --> AvaliacaoPorPares: Autor reenvia versão revisada

    AvaliacaoPorPares --> DecisaoEditorial: Editor analisa todos os pareceres
    DecisaoEditorial --> RejeitadoFinal: Decisão de rejeição
    RejeitadoFinal --> [*]

    DecisaoEditorial --> EdicaoTexto: Decisão de Aceite

    state EdicaoTexto {
        Copydesk: Revisão de texto e normalização
        DiagramacaoLayout: Editoração gráfica e geração do Galley PDF
        LeituraDeProva: Autor e editor aprovam prova final
    }

    EdicaoTexto --> PublicacaoFluxoContinuo: Agendado para Edição do Ano

    state PublicacaoFluxoContinuo {
        InclusaoFasciculo: Artigo publicado no fascículo corrente
        AtribuicaoDOI: Registro automático de DOI
        DisparoNotificacoes: E-mail de nova edição aos leitores
    }

    PublicacaoFluxoContinuo --> [*]: Artigo Live no Portal BMRT
```

---

## 3. Grafo de Entidades & Banco de Dados (Data Entity Graph)

```mermaid
erDiagram
    CONTEXT ||--o{ SECTION : possui
    CONTEXT ||--o{ USER_GROUP : define
    CONTEXT ||--o{ ISSUE : organiza
    CONTEXT ||--o{ SUBMISSION : contem

    SUBMISSION ||--|| PUBLICATION : versao_atual
    SUBMISSION ||--o{ STAGE_ASSIGNMENT : possui_participantes
    SUBMISSION ||--o{ REVIEW_ASSIGNMENT : possui_avaliacoes
    SUBMISSION ||--o{ SUBMISSION_FILE : armazena_documentos

    PUBLICATION ||--o{ AUTHOR : tem_autores
    PUBLICATION }o--|| SECTION : pertence_a
    PUBLICATION }o--o| ISSUE : publicado_em

    USER ||--o{ USER_USER_GROUP : participa_de
    USER_GROUP ||--o{ USER_USER_GROUP : agrupa
    USER ||--o{ STAGE_ASSIGNMENT : atua_como
    USER ||--o{ REVIEW_ASSIGNMENT : avalia_artigo

    CONTEXT {
        int context_id PK
        string path "bmrt"
        string primary_locale "pt_BR"
        boolean enabled 1
    }

    SUBMISSION {
        int submission_id PK
        int context_id FK
        int current_publication_id FK
        int status "1=Ativo, 3=Publicado, 4=Rejeitado"
        datetime date_submitted
    }

    PUBLICATION {
        int publication_id PK
        int submission_id FK
        int section_id FK
        int issue_id FK
        string title
        string abstract
        string doi
    }

    USER {
        int user_id PK
        string username
        string email
        string password_hash
    }
```

---

## 4. Matriz de Papéis e Permissões (RBAC)

```mermaid
graph LR
    subgraph "Nível do Servidor (Multitenant)"
        SiteAdmin["👑 Administrador do Site (Root)\nControle da infraestrutura, plugins globais e novas revistas"]
    end

    subgraph "Nível da Revista BMRT"
        Manager["⭐ Gerente da Revista\nConfiguração de design, equipe, seções e políticas"]
        Editor["📝 Editor da Revista\nGestão do pipeline, pareceres e publicação de edições"]
        SectionEditor["📑 Editor de Seção\nGestão de artigos atribuídos a uma área"]
        Reviewer["🔍 Avaliador (Parecerista)\nAcesso cego exclusivo a artigos convidados"]
        Author["✍️ Autor\nSubmissão, acompanhamento e envio de revisões"]
        Reader["📖 Leitor\nAcesso público a edições, busca e download de PDFs"]
    end

    SiteAdmin --> Manager
    Manager --> Editor
    Editor --> SectionEditor
    SectionEditor --> Reviewer
    Author -.-> Editor
    Reader -.-> Author
```
