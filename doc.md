# Aula 1 — Setup do Ambiente — Plano de Sessão

## Índice

- [1. Visão Geral da Sessão](#1-visão-geral-da-sessão)
- [2. Pré-requisitos](#2-pré-requisitos)
- [3. Estrutura dos 60 Minutos](#3-estrutura-dos-60-minutos)
  - [3.1. Alinhamento Inicial (10 min)](#31-alinhamento-inicial-10-min)
  - [3.2. Conceito Central (15 min)](#32-conceito-central-15-min)
  - [3.3. Implementação Guiada (30 min)](#33-implementação-guiada-30-min)
  - [3.4. Consolidação (5 min)](#34-consolidação-5-min)
- [4. Roteiro do Mentor](#4-roteiro-do-mentor)
- [5. Entregável da Sessão](#5-entregável-da-sessão)

---

## 1. Visão Geral da Sessão

**Aula**: 1 de 23
**Etapa do Projeto**: Backend — Infraestrutura
**Camada da Arquitetura**: Nenhuma (setup de ambiente — pré-condição para todas as camadas)
**Conceito Central**: Setup profissional de projeto Node.js + TypeScript + Prisma + PostgreSQL
**Entregável da sessão**: mentor demonstra o setup completo ao vivo — `npx prisma studio` abre, `npx tsc --noEmit` passa, `docker ps` mostra container rodando
**Entregável do mentorado (pós-sessão)**: mentorado replica o setup do zero no próprio repositório até o próximo encontro
**Arquivo de Referência**: [`01-modelagem-banco.md`](../../01-modelagem-banco.md)

> **Nota sobre o ciclo Red/Green/Refactor:** esta é a única aula da mentoria onde não há use case a implementar nem teste unitário a escrever. O critério de verificação é o ambiente funcionando. A partir da Aula 2, o ciclo Red → Green → Refactor é obrigatório em todas as sessões.

---

## 2. Pré-requisitos

**O mentorado deve chegar com:**

- [ ] Node.js 20+ instalado (`node -v` retorna v20 ou superior)
- [ ] npm disponível (`npm -v` retorna sem erro)
- [ ] Git instalado e configurado com nome e email (`git config --list`)
- [ ] Um runtime Docker instalado — Docker Desktop ou **Rancher Desktop** (gratuito, recomendado: selecionar `dockerd (moby)` na instalação)
- [ ] VS Code instalado com extensão Prisma (`Prisma.prisma`)
- [ ] Conta no GitHub com repositório `espetohub-api` criado (vazio, sem README)

**Fallback se pré-requisito não cumprido:**

> Se Docker não estiver instalado: dedique os primeiros 10 min para instalar o Rancher Desktop juntos. Comando Windows: `winget install suse.rancherdesktop`. Ajuste o cronograma — comprime a seção de conceito para 10 min e mantém os 30 min de implementação. Se Node.js não estiver instalado: encerre a sessão, marque como "pré-aula" e reagende. Node.js é bloqueador — não há como avançar sem ele.

---

## 3. Estrutura dos 60 Minutos

### Cronograma

```mermaid
gantt
    title Sessão 1 — Setup do Ambiente
    dateFormat mm
    axisFormat %M min

    section Alinhamento
    Apresentar o projeto EspetoHub       :a1, 00, 5m
    Explicar o formato da mentoria       :a2, after a1, 5m

    section Conceito Central
    Por que esta stack?                  :c1, 10, 5m
    O que é Clean Architecture           :c2, after c1, 5m
    Por que Prisma e não SQL puro        :c3, after c2, 5m

    section Implementação
    npm init e tsconfig                  :i1, 25, 5m
    ESLint e Prettier                    :i2, after i1, 6m
    Husky, lint-staged e commitlint      :i3, after i2, 7m
    Prisma init e .env e .gitignore      :i4, after i3, 5m
    Docker Compose e banco rodando       :i5, after i4, 4m
    Estrutura de pastas src/             :i6, after i5, 3m

    section Consolidação
    Checklist e tarefa da semana         :f1, 55, 5m
```

---

### 3.1. Alinhamento Inicial (10 min)

> Esta seção substitui a Revisão nas aulas seguintes. Na Aula 1 não há tarefa anterior a revisar.

**O que fazer:**

1. Apresentar o **EspetoHub** em 2 minutos — o que é, quem usa, qual o problema que resolve
2. Explicar o **formato da mentoria**: 1h por semana, 1 conceito por sessão, tarefa semanal obrigatória, sem avançar sem entender
3. Estabelecer o **contrato de aprendizagem**: o mentorado deve ser capaz de explicar tudo o que foi feito — sem isso, não avançamos

**Perguntas de contexto (mentorado responde livremente):**

1. "Você já trabalhou com TypeScript antes? Em que contexto?"
2. "Já usou algum ORM? O que achou?"

> Essas perguntas são diagnóstico, não avaliação. Use as respostas para calibrar o nível de explicação do conceito central.

---

### 3.2. Conceito Central (15 min)

**O problema do mundo real:**

> Imagine que você começou um projeto, colocou toda a lógica de negócio no mesmo arquivo que faz query no banco. Seis meses depois, o cliente quer trocar PostgreSQL por MongoDB. Você precisa reescrever tudo. Como evitar isso?

**Fluxo de entrega do conceito:**

```mermaid
sequenceDiagram
    participant M as Mentor
    participant D as Mentorado

    M->>D: Apresenta o problema: "e se precisar trocar o banco?"
    D->>M: Tenta responder com o que sabe
    M->>D: Explica Clean Architecture como separação de responsabilidades
    M->>D: Mostra as camadas que vamos construir no EspetoHub
    M->>D: Explica por que Prisma como ORM (DX, migrations, type safety)
    M->>D: Explica por que PostgreSQL (relações, transações ACID)
    D->>M: Explica com as próprias palavras o que é cada camada
    Note over M,D: Só avança se o mentorado consegue nomear as camadas
```

**Tabela de apresentação:**

| Passo | Ação do Mentor                                                                                                                                                                               |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1     | **Problema**: "Se você coloca query SQL direto no controller, o que acontece quando muda o banco?"                                                                                           |
| 2     | **Solução**: Clean Architecture — cada camada tem uma responsabilidade. Trocar o banco = trocar só o Repository                                                                              |
| 3     | **Trade-off**: mais arquivos, mais indireção — mas cada arquivo faz uma coisa e pode ser testado sozinho                                                                                     |
| 4     | **No EspetoHub**: Route → Controller → Use Case → Repository → Database — vamos construir esse fluxo aula a aula                                                                             |
| 5     | **Na Arquitetura**: hoje criamos apenas a estrutura de pastas — as camadas serão preenchidas a partir da Aula 6                                                                              |
| 6     | **Padronização**: ESLint captura erros antes de rodar, Prettier elimina debates de estilo, Husky bloqueia commits que violam as regras — padrão de time profissional desde o primeiro commit |

**Por que Prisma?**

- Schema como fonte de verdade — você escreve uma vez, o Prisma gera as migrations e os tipos TypeScript
- `npx prisma studio` para inspecionar o banco visualmente durante o desenvolvimento
- Type safety: se você tentar acessar um campo que não existe, o TypeScript avisa antes de rodar

**Por que PostgreSQL?**

- O EspetoHub tem muitos relacionamentos (pedido → itens → produtos → addons) — banco relacional é a escolha certa
- Suporta transações ACID — fundamental para operações financeiras (pagamentos, turnos de caixa)
- Suporta Row-Level Security — usaremos na Etapa 3 para isolamento de tenant no banco

**Pergunta de validação (gate — não avança sem passar):**

> "Me explica: se eu precisar trocar o PostgreSQL por outro banco no futuro, qual camada do código eu precisaria mexer e por quê?"

_Resposta esperada:_ somente o Repository — porque é a única camada que conhece o banco. O Use Case não sabe como os dados são persistidos.

---

### 3.3. Implementação Guiada (30 min)

> **Formato desta sessão:** mentor codifica ao vivo enquanto explica cada decisão. Mentorado **não digita** — observa, faz perguntas e anota o raciocínio por trás de cada passo. A implementação no repositório do mentorado é a tarefa da semana.
>
> **Regra de ouro:** pause a cada passo e pergunte ao mentorado o que você acabou de fazer e por quê — se ele não conseguir responder, explique novamente antes de avançar.

---

**Passo 1 — Inicializar repositório e TypeScript** _(~7 min)_

**O que vamos fazer:**

> Criamos a pasta do projeto, inicializamos o `package.json` (arquivo que descreve o projeto e suas dependências), instalamos o TypeScript e as ferramentas necessárias para compilar e executar arquivos `.ts`, e geramos o `tsconfig.json` (arquivo que configura como o TypeScript deve se comportar no projeto). Ao final deste passo, o TypeScript estará configurado e `npx tsc --noEmit` deve rodar sem erros.

```bash
# 1. Criar a pasta do projeto e entrar nela
mkdir espetohub-api
cd espetohub-api

# 2. Inicializar o package.json com valores padrão (evita o wizard interativo)
npm init -y

# 3. Instalar as dependências de desenvolvimento do TypeScript:
#    - typescript     → o compilador TypeScript em si (tsc)
#    - ts-node        → executa arquivos .ts diretamente sem compilar antes (útil para scripts)
#    - @types/node    → tipos do Node.js para o TypeScript reconhecer APIs como fs, path, process
#    - tsx            → alternativa moderna ao ts-node, mais rápida para rodar scripts
npm install -D typescript ts-node @types/node tsx

# 4. Gerar o tsconfig.json com as opções padrão — vamos editar manualmente na sequência
npx tsc --init
```

`tsconfig.json` final:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

> **Durante a demo:** ao editar o tsconfig, pergunte ao mentorado: "Por que `strict: true`?" — espere a resposta antes de continuar. Resposta esperada: captura erros de tipo em tempo de compilação, não em runtime.
>
> **Verificação ao vivo:** execute `npx tsc --noEmit` na sua máquina para o mentorado ver o resultado esperado. Explique o que o comando faz e por que roda sem criar arquivos em `dist/`.

---

**Passo 2 — ESLint + Prettier** _(~6 min)_

**O que vamos fazer:**

> Configuramos duas ferramentas de qualidade de código que trabalham juntas: o **ESLint** analisa o código em busca de erros e padrões ruins (ex: variáveis declaradas e não usadas), enquanto o **Prettier** garante que o código siga sempre o mesmo estilo visual (indentação, vírgulas, aspas). Ao final deste passo, `npm run lint` e `npm run format:check` devem passar sem erros.
>
> **Por que instalar os dois?** ESLint cuida de _qualidade lógica_ (regras de código), Prettier cuida de _formatação visual_ (aparência). O pacote `eslint-config-prettier` desativa as regras do ESLint que conflitam com o Prettier, evitando que as duas ferramentas briguem entre si.

```bash
# Pacotes que vamos instalar:
#   - eslint                  → o motor principal de análise estática de código
#   - @eslint/js              → regras base recomendadas para JavaScript puro
#   - typescript-eslint       → integração do ESLint com TypeScript (regras específicas de TS)
#   - prettier                → formatador de código
#   - eslint-config-prettier  → desativa regras do ESLint que conflitam com o Prettier
npm install -D eslint @eslint/js typescript-eslint prettier eslint-config-prettier
```

`eslint.config.mjs` na raiz do projeto:

```js
import js from "@eslint/js";
import tseslint from "typescript-eslint";
import prettierConfig from "eslint-config-prettier";

export default tseslint.config(
  { ignores: ["dist/", "node_modules/"] },
  js.configs.recommended,
  ...tseslint.configs.recommended,
  prettierConfig,
  {
    rules: {
      "@typescript-eslint/no-unused-vars": "error",
      "@typescript-eslint/no-explicit-any": "warn",
    },
  },
);
```

`.prettierrc` na raiz do projeto:

```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 100
}
```

`.prettierignore` na raiz do projeto:

```
node_modules/
dist/
```

Adicione ao `package.json` dentro de `"scripts"`:

```json
"lint": "eslint .",
"lint:fix": "eslint . --fix",
"format": "prettier --write .",
"format:check": "prettier --check ."
```

> **Durante a demo:** após configurar o ESLint, insira um erro proposital (ex: declare `const x = 1` sem usar `x`) e execute `npm run lint` para o mentorado ver o erro sendo capturado. Pergunte: "Por que queremos que o lint falhe antes de rodar o código?" — resposta esperada: capturar erros de tipo e estilo em desenvolvimento, não em produção.
>
> **Verificação ao vivo:** execute `npm run format:check` — deve passar sem erros.

---

**Passo 3 — Husky, lint-staged e commitlint** _(~7 min)_

**O que vamos fazer:**

> Configuramos automações que rodam **automaticamente** nos momentos certos do fluxo do Git, sem depender de o desenvolvedor lembrar de executar manualmente. Ao final deste passo, qualquer commit com mensagem fora do padrão será rejeitado, e o código será formatado e verificado antes de cada commit.
>
> **As três ferramentas e o papel de cada uma:**
>
> - **Husky** — registra scripts nos _Git Hooks_ (gatilhos nativos do Git, como `pre-commit` e `commit-msg`). É o mecanismo que faz o Git chamar nossas automações no momento certo.
> - **lint-staged** — roda ESLint e Prettier **somente nos arquivos que estão no stage** (`git add`). Isso é mais rápido do que rodar em todos os arquivos do projeto.
> - **commitlint** — valida a mensagem do commit antes de ele ser criado. Se a mensagem não seguir o padrão Conventional Commits (ex: `feat(auth): add login endpoint`), o commit é bloqueado.

```bash
# Pacotes que vamos instalar:
#   - husky                            → registra Git Hooks no projeto
#   - lint-staged                      → roda linters apenas nos arquivos em stage
#   - @commitlint/cli                  → valida mensagens de commit
#   - @commitlint/config-conventional  → conjunto de regras do padrão Conventional Commits
npm install -D husky lint-staged @commitlint/cli @commitlint/config-conventional

# Inicializa o Husky: cria a pasta .husky/ e adiciona o script "prepare" no package.json
# O script "prepare" garante que o Husky seja instalado automaticamente após npm install
npx husky init
```

`.commitlintrc.json` na raiz do projeto:

```json
{
  "extends": ["@commitlint/config-conventional"]
}
```

Adicione ao `package.json` no nível raiz (fora de `"scripts"`):

```json
"lint-staged": {
  "*.ts": [
    "eslint --fix",
    "prettier --write"
  ]
}
```

Sobrescreva `.husky/pre-commit` (criado pelo `npx husky init`) com:

```sh
npx lint-staged
```

Crie `.husky/commit-msg`:

```sh
npx --no -- commitlint --edit $1
```

> **Durante a demo:** tente fazer um commit com mensagem inválida (ex: `git commit -m "arrumei umas coisas"`) para o mentorado ver o commitlint bloqueando. Em seguida, mostre um commit válido (ex: `chore(setup): initialize project`). Pergunte: "Qual o benefício de padronizar mensagens de commit?" — resposta esperada: rastreabilidade, geração de changelog automático, leitura do histórico em equipe.
>
> **Conventional commits que usaremos neste projeto:**

| Prefixo    | Quando usar                              |
| ---------- | ---------------------------------------- |
| `feat`     | Nova funcionalidade                      |
| `fix`      | Correção de bug                          |
| `chore`    | Setup, configuração, manutenção          |
| `refactor` | Refatoração sem mudança de comportamento |
| `test`     | Adição ou correção de testes             |
| `docs`     | Alterações em documentação               |

---

**Passo 4 — Instalar Prisma, proteger credenciais** _(~5 min)_

**O que vamos fazer:**

> Instalamos o Prisma e inicializamos a configuração dele no projeto. O `npx prisma init` cria dois arquivos: `prisma/schema.prisma` (onde definiremos os modelos do banco) e `.env` (onde ficará a URL de conexão com o banco, contendo usuário e senha). Como o `.env` tem credenciais sensíveis, ele **nunca pode ser commitado** — por isso adicionamos ele ao `.gitignore` imediatamente. Criamos também um `.env.example` com valores fictícios para documentar quais variáveis o projeto precisa, sem expor as reais.
>
> **Por que dois pacotes do Prisma?**
>
> - `prisma` (devDependency) — a CLI: `npx prisma migrate`, `npx prisma studio`, `npx prisma generate`. Usada apenas durante o desenvolvimento.
> - `@prisma/client` (dependency) — a biblioteca que o código da aplicação importa para fazer queries no banco. Vai para produção.

```bash
# Instalar a CLI do Prisma como devDependency (usada apenas em desenvolvimento)
npm install -D prisma

# Instalar o Prisma Client como dependency de runtime (usado pela aplicação em produção)
npm install @prisma/client

# Inicializar o Prisma: cria prisma/schema.prisma e o arquivo .env com DATABASE_URL
npx prisma init --datasource-provider postgresql

# Proteger o .env: adicionar ao .gitignore para que nunca seja commitado acidentalmente
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore
echo "dist/" >> .gitignore
```

Criar `.env.example` (para documentar sem expor):

```bash
echo 'DATABASE_URL="postgresql://usuario:senha@localhost:5432/espetohub_dev?schema=public"' > .env.example
```

> **Durante a demo:** antes de criar o `.gitignore`, pergunte ao mentorado: "O que acontece se o `.env` for commitado com a senha do banco?" — espere a resposta. Depois execute `git status` na sua máquina e mostre que o `.env` **não aparece** na listagem.
>
> **Ponto de atenção visual:** mostre ao mentorado o arquivo `.env` aberto no VS Code ao lado do `.gitignore` — ele precisa ver que o nome do arquivo no `.gitignore` é idêntico ao nome do arquivo real.

---

**Passo 5 — Docker Compose e banco rodando** _(~4 min)_

**O que vamos fazer:**

> Criamos o `docker-compose.yml` que define e sobe um container PostgreSQL localmente. O Docker Compose permite descrever a infraestrutura como código — qualquer dev que clonar o repositório sobe o banco com um único comando (`docker compose up -d`), sem precisar instalar o PostgreSQL na máquina.
>
> **Por que usar um volume (`postgres_data`)?** O PostgreSQL armazena os dados dentro do container. Sem um volume declarado, tudo o que você gravou no banco seria perdido ao parar o container (`docker compose down`). O volume persiste os dados em disco, fora do ciclo de vida do container.
>
> **O que o `-d` significa em `docker compose up -d`?** _Detached mode_ — o container sobe em segundo plano e o terminal fica livre. Sem o `-d`, o terminal ficaria travado exibindo os logs do PostgreSQL.

```yaml
# REFERÊNCIA DO MENTOR — docker-compose.yml na raiz do projeto

services: # lista de containers que o Compose vai gerenciar
  db:
    image: postgres:16 # imagem oficial do PostgreSQL versão 16
    container_name: espetohub-pg # nome do container (aparece no docker ps)
    environment: # variáveis de ambiente que configuram o banco na inicialização
      POSTGRES_USER: espeto
      POSTGRES_PASSWORD: espeto
      POSTGRES_DB: espetohub_dev
    ports:
      - "5432:5432" # mapeia porta do host:porta do container
    volumes:
      - postgres_data:/var/lib/postgresql/data # persiste os dados em disco

volumes:
  postgres_data: # declara o volume nomeado gerenciado pelo Docker
```

`.env` com a URL real:

```env
DATABASE_URL="postgresql://espeto:espeto@localhost:5432/espetohub_dev?schema=public"
```

```bash
# Subir o container em segundo plano (-d = detached)
docker compose up -d

# Verificar que o container está rodando (deve mostrar espetohub-pg com status Up)
docker ps
```

> **Durante a demo:** após executar `docker compose up -d`, mostre `docker ps` ao mentorado e explique cada coluna. Pergunte: "O que acontece com os dados do banco se eu rodar `docker compose down`?" — explique o papel do `volumes` após ouvir a resposta.

---

**Passo 6 — Estrutura de pastas da Clean Architecture** _(~3 min)_

**O que vamos fazer:**

> Criamos a estrutura de diretórios do projeto que reflete as camadas da Clean Architecture. As pastas ficam vazias por enquanto — serão preenchidas a partir da Aula 2. O objetivo aqui é que o mentorado veja o mapa completo da arquitetura e consiga associar cada pasta a uma responsabilidade antes de começar a codar.
>
> **Mapa das camadas:**
>
> | Pasta                         | Camada         | O que vai aqui                                                       |
> | ----------------------------- | -------------- | -------------------------------------------------------------------- |
> | `src/domain/entities/`        | Domínio        | Classes que representam conceitos do negócio (ex: `Order`, `Tenant`) |
> | `src/domain/errors/`          | Domínio        | Erros específicos do negócio (ex: `InsufficientStockError`)          |
> | `src/domain/use-cases/`       | Domínio        | Interfaces/contratos dos use cases                                   |
> | `src/application/use-cases/`  | Aplicação      | Implementação das regras de negócio                                  |
> | `src/infra/db/prisma/`        | Infraestrutura | Repositórios concretos que fazem queries no banco via Prisma         |
> | `src/infra/http/controllers/` | Infraestrutura | Controllers que recebem a requisição HTTP e chamam o use case        |
> | `src/infra/http/middlewares/` | Infraestrutura | Middlewares (autenticação, validação de tenant, etc.)                |
> | `src/infra/http/routes/`      | Infraestrutura | Definição das rotas da API                                           |
> | `src/main/factories/`         | Main           | Fábricas que montam use cases com suas dependências injetadas        |

```bash
# Criar toda a estrutura de pastas de uma vez
# O -p cria pastas intermediárias automaticamente (não falha se já existir)
mkdir -p src/domain/entities
mkdir -p src/domain/errors
mkdir -p src/domain/use-cases
mkdir -p src/application/use-cases
mkdir -p src/infra/db/prisma
mkdir -p src/infra/http/controllers
mkdir -p src/infra/http/middlewares
mkdir -p src/infra/http/routes
mkdir -p src/main/factories
```

> **Durante a demo:** após criar as pastas, abra o Explorer do VS Code na sua máquina para o mentorado ver a estrutura completa. Pergunte: "Onde vai ficar o código que faz query no banco?" → `src/infra/db/prisma/`. "Onde vai ficar a regra de negócio de criar um pedido?" → `src/application/use-cases/`. O mentorado deve conseguir responder — se não conseguir, revise o diagrama de camadas do conceito central antes de avançar.

---

**Padrão de commit ao final:**

```
chore(setup): initialize project with TypeScript, Prisma, ESLint, Prettier and Husky

- Node.js 20 + TypeScript 5 with strict mode enabled
- ESLint + typescript-eslint configured with strict rules
- Prettier configured with project code style
- Husky + lint-staged: lint and format enforced on every commit
- commitlint: conventional commits enforced via commit-msg hook
- Prisma configured with PostgreSQL datasource
- Docker Compose for local database (espetohub-pg)
- Clean Architecture folder structure under src/
- .env protected via .gitignore, .env.example documented
```

---

### 3.4. Consolidação (5 min)

**Checklist da demo (verificar ao vivo na máquina do mentor antes de encerrar):**

- [ ] `npx tsc --noEmit` passa sem erros
- [ ] `npm run lint` passa sem erros
- [ ] `npm run format:check` passa sem erros
- [ ] Commit com mensagem inválida é rejeitado pelo commitlint
- [ ] `docker ps` mostra `espetohub-pg` com status `Up`
- [ ] `git status` — `.env` **não** aparece na listagem
- [ ] `npx prisma studio` abre no browser sem erros
- [ ] `git log --oneline` mostra o commit de setup
- [ ] Mentorado consegue nomear a responsabilidade de cada pasta do `src/`

**Tarefa da semana:**

> Replique o setup completo do zero no seu próprio repositório (`espetohub-api`), seguindo o que foi demonstrado na sessão. Ao final, todos os critérios abaixo devem estar funcionando na **sua máquina**.
>
> Critérios de aceite:
>
> - [ ] `npx tsc --noEmit` passa sem erros
> - [ ] `npm run lint` passa sem erros
> - [ ] `npm run format:check` passa sem erros
> - [ ] `git commit -m "teste"` é rejeitado pelo commitlint (deve retornar erro)
> - [ ] `docker compose up -d` sobe sem erros — `docker ps` mostra `espetohub-pg` com status `Up`
> - [ ] `npx prisma studio` abre no browser sem erros
> - [ ] `.env` não aparece em `git status`
> - [ ] `.env.example` está no repositório com valores fictícios
> - [ ] Estrutura de pastas `src/` criada conforme demonstrado
> - [ ] 1 commit no formato conventional commits no repositório GitHub
>
> Além do setup: leia a seção "Aula 2" em `01-modelagem-banco.md`. Na próxima sessão você deve explicar: (1) o que é multi-tenancy, (2) a diferença entre `ownerId` no `Tenant` e a tabela `Employee`, (3) o que é uma relação N:N e por que usamos tabela pivot com dados extras.

**O que será verificado na próxima revisão:**

> Mentorado demonstra ao vivo — na própria máquina — cada um dos critérios acima. Em seguida, explica verbalmente os três pontos da leitura sem consultar o arquivo.

---

## 4. Roteiro do Mentor

### Sinais de alerta e ações imediatas

| Sinal observado                                                    | Ação do mentor                                                                                                                                                          |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mentorado não consegue responder o que cada pasta do `src/` guarda | Pausar a demo. Voltar ao diagrama de camadas e pedir que ele associe cada camada antes de continuar                                                                     |
| Não sabe responder a pergunta de validação das camadas             | Repetir a explicação do conceito central — não avançar para a demo de implementação                                                                                     |
| Docker não sobe durante a demo (demo do mentor)                    | Verificar se Rancher Desktop está rodando. Fallback: usar Neon.tech (PostgreSQL na nuvem gratuito) e documentar a diferença no `DATABASE_URL`                           |
| `.env` aparece no `git status` durante a demo                      | Parar e transformar isso em ponto de aprendizado — mostrar o risco na prática antes de corrigir                                                                         |
| Prisma Studio não abre durante a demo                              | Verificar `DATABASE_URL`. Rodar `npx prisma db push` para criar o schema vazio. Mostrar o erro e o processo de debug ao mentorado                                       |
| ESLint falha na configuração (`Cannot find module`)                | Verificar se o arquivo é `eslint.config.mjs` (não `.js`). Confirmar que todos os pacotes foram instalados com `npm ls eslint typescript-eslint`                         |
| Husky não bloqueia o commit durante a demo                         | Verificar se `.husky/pre-commit` e `.husky/commit-msg` existem com `ls .husky/`. Confirmar se o script `prepare` foi adicionado ao `package.json` pelo `npx husky init` |

### Perguntas de aprofundamento (se sobrar tempo)

1. "Por que usamos `docker-compose.yml` em vez de `docker run` com os parâmetros na linha de comando?"
   - _Resposta esperada:_ configuração versionada junto ao código — qualquer dev clona e sobe com um comando
2. "O que é `strict: true` no tsconfig e quais erros ele captura que sem ele passariam?"
   - _Resposta esperada:_ null checks, implicit any, function return types — erros que só apareceriam em runtime
3. "Por que separamos `devDependencies` de `dependencies` no package.json?"
   - _Resposta esperada:_ `devDependencies` não vai para produção — TypeScript, Prisma CLI são ferramentas de build, não runtime
4. "Por que usamos `lint-staged` em vez de rodar o lint em todos os arquivos no hook de pre-commit?"
   - _Resposta esperada:_ performance — em projetos grandes, rodar ESLint em todos os arquivos é lento; o `lint-staged` roda apenas nos arquivos staged, que são os únicos que importam para o commit

---

## 5. Entregável da Sessão

**Estado final esperado ao término da sessão (máquina do mentor):**

```
Repositório espetohub-api com:
- TypeScript compilando sem erros (npx tsc --noEmit)
- ESLint e Prettier configurados (npm run lint e npm run format:check passam)
- Husky + commitlint bloqueando commits com mensagem inválida
- Container PostgreSQL rodando localmente (docker ps mostra espetohub-pg Up)
- Prisma Studio abrindo no browser
- Estrutura de pastas src/ da Clean Architecture criada
- .env no .gitignore, .env.example no repositório
- 1 commit no formato conventional commits
```

**Estado esperado na próxima sessão (máquina do mentorado):**

```
O mentorado replica o mesmo resultado acima no próprio repositório
e demonstra ao vivo cada critério do checklist da tarefa da semana.
```

**Comandos de verificação:**

```bash
npx tsc --noEmit                  # deve passar sem erros
npm run lint                      # deve passar sem erros
npm run format:check              # deve passar sem erros
git commit -m "teste"             # deve ser bloqueado pelo commitlint
docker ps                         # deve mostrar espetohub-pg com status Up
npx prisma studio                 # deve abrir no browser sem erros
git log --oneline                 # deve mostrar o commit de setup
git status                        # .env NÃO deve aparecer
```
