# 📘 Guia Técnico e Decisões de Arquitetura

Este documento descreve as decisões técnicas, ferramentas, padrões e configurações utilizadas na construção deste repositório **Laravel 13 Profissional**.

-----

## 1. Estrutura Docker Moderna (`compose.yaml`)

Este projeto utiliza o arquivo **`compose.yaml`**, seguindo a especificação moderna recomendada pela Docker Inc.

  - **Por que não `docker-compose.yml`?**
    A nomenclatura antiga permanece funcional, mas o Docker adotou `compose.yaml` como padrão oficial e atualizado.

  - **Impacto no projeto:**
    As versões recentes do Laravel Sail já geram automaticamente este formato. A funcionalidade permanece a mesma, mas com maior aderência às convenções do ecossistema Docker atual.

-----

## 2. Stack de Frontend (Tailwind CSS v4)

Acompanhando a evolução do ecossistema Laravel, este boilerplate adota a arquitetura "Utility-First" em sua versão mais moderna e performática.

### ✔️ Decisão de Arquitetura

Optamos por **Tailwind CSS v4**.

  * **Motivo:**
    A versão 4 traz uma reescrita completa do motor do framework. Ela elimina a necessidade de arquivos de configuração complexos (`tailwind.config.js` e `postcss.config.js`), garantindo um build extremamente rápido e um ambiente de desenvolvimento mais limpo, ideal para escalar projetos SaaS e aplicações modernas.

### ✔️ Configuração do Vite

A configuração do frontend foi simplificada ao máximo, utilizando o plugin oficial do Tailwind para o Vite.

  * **Entrada:** `resources/css/app.css`
  * **Processamento:** O `vite.config.js` importa o `@tailwindcss/vite`, e o CSS principal utiliza apenas a diretiva `@import "tailwindcss";`, compilando tudo de forma nativa e otimizada para produção.

-----

## 3. Solução de Desafios de Ambiente (Windows/Docker)

Para garantir que o projeto rode em qualquer sistema operacional (especialmente Windows sem WSL2 nativo), implementamos correções específicas:

### 🔧 Variáveis de Usuário (`WWWUSER`)

O Docker no Windows frequentemente falha ao tentar criar usuários internos com o comando `groupadd`, resultando em erro de build.

  * **Solução:** A injeção das variáveis `WWWUSER=1000` e `WWWGROUP=1000` no arquivo `.env` força o ID do usuário, permitindo que o container suba sem erros de permissão.

### 🔧 Script de Execução (`sail` vs `docker compose`)

O script auxiliar `./vendor/bin/sail` apresenta incompatibilidade com terminais Git Bash (Mingw).

  * **Padronização:** Documentamos o uso direto do binário `docker compose exec laravel.test ...` para garantir que os comandos funcionem universalmente.

-----

## 4. Dependências e Comandos de Instalação

### ✔️ Criar o Projeto Laravel

```bash
composer create-project laravel/laravel:^13.0 laravel-v13
```

  * **Motivo:** Garante a instalação da versão major (13.x), evitando versões instáveis (`dev`) ou antigas.

### ✔️ Instalar Ferramentas de Desenvolvimento

```bash
composer require --dev larastan/larastan barryvdh/laravel-ide-helper
```

  * **Flag `--dev`:** As dependências são restritas ao ambiente local, garantindo que o deploy de produção seja leve e seguro.

-----

## 5. Arquivos de Padronização e Qualidade (QA)

### 📄 `.editorconfig`

  * Garante padronização entre diferentes editores (VS Code, PHPStorm).
  * Configura indentação padrão (4 espaços) e regra específica para YAML (2 espaços).

### 📄 `pint.json`

  * Configura o **Laravel Pint**, formatador automático de código.
  * Mantém o estilo estrito de acordo com a **PSR-12**.

### 📄 `phpstan.neon`

  * Configurações do **Larastan** (Análise Estática).
  * **Level 5** aplicado propositalmente: Rigor suficiente para detectar bugs reais sem burocratizar o desenvolvimento.

-----

## 6. Automação via `composer.json`

Foi adicionada uma automação via **scripts Composer**:

  * Sempre que o comando `composer update` é executado, o **IDE Helper** é regenerado automaticamente.
  * **Benefícios:** Autocomplete sempre atualizado e melhoria significativa na Developer Experience (DX).

-----

## 7. Integração Contínua (GitHub Actions)

O workflow `.github/workflows/laravel.yml` implementa uma pipeline sólida de CI que executa:

1.  **Checkout:** Clona o repositório.
2.  **Configuração do PHP & Node:** Instala as versões necessárias no runner (PHP 8.3+ e Node 20).
3.  **Qualidade (QA):**
      * Laravel Pint (Estilo)
      * Larastan (Lógica)
        *Se qualquer um falhar, o build é interrompido.*
4.  **Testes e Build:**
    Executa a suíte de testes do backend e a compilação do Tailwind v4 no frontend.

### Benefícios:

  * Evita regressões.
  * Impede que código fora do padrão entre na branch `main`.
  * Automatiza validação contínua do projeto.

-----