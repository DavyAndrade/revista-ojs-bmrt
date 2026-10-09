# Design e Identidade Visual — Revista BMRT (SBCAL)

Documento técnico de especificação do Design System e análise de engenharia reversa do portal atual da revista **Biological Models Research and Technology (BMRT)** hospedada no provedor comercial Periodikos (`http://www.bmrt.periodikos.com.br/`), servindo como especificação normativa para o ecossistema PKP OJS 3.5 LTS.

---

## 1. Identidade e Metadados Oficiais

- **Título Oficial:** *Biological Models Research and Technology*
- **Abreviação ISO / NLM:** *Biol M Res & Tech*
- **Entidade Mantenedora:** Sociedade Brasileira de Ciência em Animais de Laboratório (SBCAL)
- **Editor-Chefe:** Prof. Dr. Marcel Frajblat (UFRJ)
- **ISSN Eletrônico (e-ISSN):** `2675-9225`
- **Periodicidade:** Fluxo Contínuo (*Continuous flow*)
- **E-mail Editorial Oficial:** `bmrt.sbcal.journal@gmail.com`
- **Histórico & Acervo:**
  - 2012–2020: *Revista da Sociedade Brasileira de Ciência em Animais de Laboratório* (RESBCAL).
  - 2021–Atual: Internacionalização da revista com a nova marca BMRT (Volumes 1 ao 5).

---

## 2. Paleta de Cores e Tokens Visuais

A identidade da BMRT apoia-se em uma escala biocientífica dominada por tons de verde folha/esmeralda institucional (SBCAL), cinza-ardósia escuro para texto e tipografia, e superfícies neutras de alto contraste.

| Token / Papel | HEX | RGB | Aplicação Primária |
| :--- | :--- | :--- | :--- |
| `--color-primary` | `#198754` | `rgb(25, 135, 84)` | Barra de navegação principal, botões de ação primária, borda de cards |
| `--color-primary-dark` | `#006B46` | `rgb(0, 107, 70)` | Hover de menus, badges em foco, sublinhados de links ativos |
| `--color-accent-green` | `#008037` | `rgb(0, 128, 55)` | Badges de áreas temáticas, botões de download de PDF, cabeçalhos de anos no arquivo |
| `--color-primary-light` | `#E0F0E7` | `rgb(224, 240, 231)` | Fundo da barra de pesquisa, caixas de destaque leve |
| `--color-primary-border` | `#66B387` | `rgb(102, 179, 135)` | Ícones de pesquisa, links no rodapé, bordas sutis de acentuação |
| `--color-text-dark` | `#243743` | `rgb(36, 55, 67)` | Cor base de títulos (`h1`-`h4`), rodapé superior, títulos de artigos |
| `--color-text-muted` | `#5B6972` | `rgb(91, 105, 114)` | Nomes de autores em itálico, metadados secundários de paginação |
| `--color-text-body` | `#787F83` | `rgb(120, 127, 131)` | Corpo de texto institucional, citações DOI, descritivos de missão |
| `--color-bg-body` | `#F1F2F3` | `rgb(241, 242, 243)` | Background geral da página fora dos containers brancos |
| `--color-bg-card` | `#FFFFFF` | `rgb(255, 255, 255)` | Fundo dos cards de artigos, caixas de texto e seções de leitura |
| `--color-border-subtle` | `#DDDDDD` | `rgb(221, 221, 221)` | Separadores de blocos e divisórias verticais/horizontais |

---

## 3. Tipografia

- **Família Tipográfica Principal:** `'Open Sans', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`
- **Escala de Tamanhos:**
  - `h1` (Título de Página): `24px` / `1.714rem`, `font-weight: 600`, cor `#243743`.
  - `h2` (Títulos de Seção / Edição): `18px` / `1.285rem`, `font-weight: 600`, cor `#243743`.
  - Título do Artigo em Lista: `17px`, `line-height: 24px`, link cor `#243743` (hover: `#198754`).
  - Autores do Artigo: `13px` a `15px`, `font-style: italic`, cor `#5B6972`.
  - Corpo de Texto Institucional: `15px`, `line-height: 23px`, cor `#787F83`.
  - Barra de Navegação: `14px`, `font-weight: 500`, cor `#FFFFFF`.

---

## 4. Estrutura de Layout e Grid (Desktop vs Mobile)

### 4.1. Grid do Portal Original
- **Framework Base:** Zurb Foundation 5.0 modificado.
- **Largura Máxima do Container Principal:** `960px` a `1140px` centralizado (`.row { max-width: 960px; }`).
- **Comportamento em Duas Colunas:**
  - Coluna Principal (Conteúdo/Artigos/Corpo): `medium-8 columns` ou `medium-12 columns` conforme presença de menu resumo.
  - Coluna Lateral Direita (Banners e Acesso rápido): `medium-4 columns` contendo banners de submissão, ética e links úteis.

### 4.2. Estrutura Hierárquica do Header (Periodikos vs OJS)
No Periodikos, o header é composto por 3 camadas sobrepostas com fundo branco/geométrico:
1. **Faixa Superior de Utilidades / ISSN:**
   - Contém o selo oficial SBCAL + ISSN à direita (`banner_602586850e882563281d54d5.png`).
2. **Faixa Central da Marca:**
   - Imagem de fundo: Banner geométrico vetorial com padrão científico suave (`banner.png`, 1920x226).
   - Logotipo principal da revista BMRT SBCAL (`logo.png`, 570x180) alinhado à esquerda.
3. **Barra de Navegação Principal:**
   - Altura: `44px` a `46px`.
   - Fundo: Verde solido `#198754` com itens em `#FFFFFF`.
   - Itens do menu:
     1. *Home* (`/`)
     2. *About the Journal* (`/about`)
     3. *Editorial Board* (`/board`)
     4. *Guidelines and Policies* (`/instructions`)
     5. *Current Edition* (`/current`)
     6. *Archive* (`/archive`)
     7. *Contact* (`/contact`)
     8. Campo de busca embutido à direita com fundo `#E0F0E7`.

---

## 5. Mapeamento de Componentes e Objetos Visuais

### 5.1. Card de Artigo (*Article Summary Card*)
```
+------------------------------------------------------------------------------------+
| [Biol M Res & Tech, vol.5 n1, 2025]                                                |
| Resumos da XXVIII Semana Científica Benjamin Eurico Malucelli                      |
| [Autores em itálico]                                                               |
| [Badge: Area I]  [Badge: Research Article]             [ PDF (Botão Verde) ]       |
+------------------------------------------------------------------------------------+
```
- **Borda de Destaque:** Borda esquerda sólida de `4px` ou `5px` em `#198754`.
- **Fundo:** `#FFFFFF` com leve sombra de elevação `box-shadow: 0 1px 4px rgba(0,0,0,0.06)`.
- **Badges:**
  - Áreas Temáticas: Fundo `#008037`, texto branco, padding `1px 8px`.
  - Tipo de Manuscrito: Fundo `#243743`, texto branco, padding `1px 8px`.
  - Galley (PDF): Fundo `#008037`, ícone FontAwesome `fa-file-pdf-o`, hover `#006B46`.

### 5.2. Rodapé Institucional (*Footer*)
- **Fundo:** `#243743` (cinza ardósia escuro).
- **Divisória Superior:** Linha de acentuação verde `#198754` (3px a 4px).
- **Conteúdo:**
  - Navegação rápida em links horizontais/verticais em `#66B387`.
  - Selo e informações de direitos da Sociedade Brasileira de Ciência em Animais de Laboratório (SBCAL).
  - Ícones de redes sociais: Facebook, Twitter/X, Instagram e YouTube.

---

## 6. Mapeamento de Seções Editoriais e Escopo

### As 6 Áreas Oficiais da BMRT:
1. **Area I:** Education, legislation, ethics, and biosafety in laboratory animals.
2. **Area II:** Animal welfare and behavior.
3. **Area III:** Production and husbandry of biological models.
4. **Area IV:** Use of the biological models for scientific and didactic purposes.
5. **Area V:** Facility's building and management.
6. **Area VI:** Alternative methods to the use of animals and non-conventional biological models.

### Categorias de Manuscritos:
1. *Research Article* (Artigo Original)
2. *Review Articles* (Artigos de Revisão)
3. *Brief Communication / Technical Communication* (Comunicações Breves)
4. *Case Report* (Relato de Casos Clínicos)
5. *Conference Abstracts* (Resumos de Congressos)

---

## 7. Inventário de Assets Gráficos Preservados no Repositório

Todos os assets originais foram extraídos e versionados localmente sob a pasta `assets/`:

| Arquivo no Repositório | Resolução Original | Função |
| :--- | :--- | :--- |
| `assets/branding/logo.png` | 570 × 180 | Logotipo oficial BMRT SBCAL |
| `assets/branding/banner.png` | 1920 × 226 | Banner de fundo geométrico do topo |
| `assets/branding/banner-issn.png` | 475 × 86 | Selo SBCAL + e-ISSN 2675-9225 |
| `assets/branding/capa-v1.jpg` | 3508 × 1536 | Arte de capa editorial de fascículo |
| `assets/banners/banner-submissao.png` | 250 × 250 | Botão de chamada para submissão |
| `assets/banners/banner-cadastro.png` | 250 × 250 | Botão de cadastro de avaliadores/autores |
| `assets/banners/banner-ethics.jpg` | 210 × 167 | Selo de ética em publicação |
| `assets/sponsors/alesco.png` | 164 × 59 | Logo de patrocinador/apoiador institucional |
| `assets/sponsors/dgranja.png` | 2820 × 2364 | Logo de apoiador institucional |
| `assets/sponsors/iclas.png` | 105 × 59 | Logo do ICLAS (*International Council for Laboratory Animal Science*) |
| `assets/sponsors/ictb-mpcal.png` | 118 × 59 | Logo ICTB / Fiocruz |

---

## 8. Diretrizes de Implementação no OJS 3.5 LTS

1. **Tema Nativo DefaultTheme:**
   - Utilizar as opções nativas do OJS (`baseColour = #198754`) para aplicar as cores em botões, links, abas e paginação sem criar código desnecessário (Princípio Ponytail).
2. **Identidade do Cabeçalho:**
   - Evitar imagens estáticas que deformem a barra de navegação com floats absolutos.
   - O logotipo institucional do cabeçalho deve respeitar proporção horizontal de até `80px` de altura no desktop para manter alinhamento com a barra do OJS.
3. **Menu de Navegação:**
   - Manter as 7 seções principais no Menu Primário do OJS: *Início*, *Sobre*, *Corpo Editorial*, *Diretrizes para Autores*, *Edição Atual*, *Acervo*, *Contato*.
