# CLAUDE.md

Contexto para o Claude Code operando **dentro deste repositório**
(`otcv8-agents`).

## O que é este repositório

É uma coleção de **subagentes especializados** voltados ao desenvolvimento do
[OTCv8/otcv8-dev](https://github.com/OTCv8/otcv8-dev). Aqui **não** existe
código de produção do OTCv8 — apenas:

- Definições de agentes em `.claude/agents/*.md`
- Possíveis skills, prompts e snippets de configuração
- Documentação sobre como usar/estender esses agentes

Não tente buildar, rodar testes ou rodar o cliente OTCv8 a partir daqui. Não
há `CMakeLists.txt`, `init.lua` nem dependências (vcpkg/Boost/LuaJIT).

## Tarefas típicas neste repo

- Criar um novo agente em `.claude/agents/<nome>.md`.
- Editar/melhorar o system prompt de um agente existente.
- Atualizar README.md ou este CLAUDE.md.
- Ajustar `.claude/settings.json` (permissões, hooks) quando solicitado.

## Convenções para definir agentes

Arquivo: `.claude/agents/<nome>.md` com frontmatter YAML + corpo (system
prompt em Markdown).

```markdown
---
name: otcv8-<area>
description: Frase em terceira pessoa descrevendo quando o agente deve ser usado. Seja específico — o despachante usa este texto para escolher o agente.
tools: Read, Grep, Glob   # opcional; omitir herda todas
model: sonnet              # opcional
---

System prompt aqui. Defina:
- Escopo (o que o agente cobre e o que NÃO cobre)
- Convenções do OTCv8 que ele deve respeitar
- Saída esperada (formato, nível de detalhe)
- Restrições (ex.: nunca editar arquivos em src/ sem confirmar)
```

Regras:

- **Nome**: prefixo `otcv8-` + área (ex.: `otcv8-lua`, `otcv8-cmake`,
  `otcv8-otui`, `otcv8-cpp-engine`).
- **Tools**: minimize. Agentes de pesquisa/revisão geralmente só precisam de
  `Read`, `Grep`, `Glob`. Só conceda `Edit`/`Write`/`Bash` se o agente
  realmente precisa modificar arquivos ou rodar comandos.
- **Description**: deve deixar claro *quando* usar; isso é o que orienta a
  delegação automática.
- **System prompt**: deve referenciar diretórios reais do `otcv8-dev` (`src/`,
  `modules/`, `mods/`, `layouts/`, `data/`, `tools/`, `android/`, `vc16/`).

## Conhecimento de domínio sobre o OTCv8

Quando estiver escrevendo ou revisando agentes, leve em conta:

- **Linguagens**: C++ (≈44%), Lua (≈36%), C (≈15%), CMake (≈4%).
- **Engine** em C++ no diretório `src/`, com binding para Lua via LuaJIT.
- **Módulos Lua** ficam em `modules/`; mods opcionais ficam em `mods/`
  (exemplo de referência: `mods/game_healthbars`).
- **Layouts UI** em formato OTUI (`.otui`) em `layouts/`.
- **Build** via CMake; dependências via vcpkg em commits **pinados** (Windows
  e Linux usam commits diferentes — não troque sem motivo).
- **Plataformas alvo**: Windows (VS 2019), Linux (GCC ≥9, Boost ≥1.67,
  libzip-dev, physfs ≥3) e Android (NDK r21b).
- **Regra de ouro do upstream**: features customizadas precisam ser
  **opcionais e configuráveis** — agentes de revisão devem checar isso.
- Repositório de binários prontos é separado: `OTCv8/otclientv8`.

## O que NÃO fazer

- Não criar arquivos de código C++/Lua/OTUI aqui — eles pertencem ao
  `otcv8-dev`.
- Não escrever documentação extra (`docs/`, READMEs por agente, etc.) sem
  pedido explícito. Mantenha o repo enxuto.
- Não inventar caminhos do OTCv8 que você não verificou; quando em dúvida,
  cite o repositório upstream em vez de afirmar que um arquivo existe.
- Não adicionar permissões amplas em `.claude/settings.json` por
  conveniência. Permissões granulares > wildcard.

## Branch de trabalho

O agente raiz já recebe instruções específicas de branch via configuração da
sessão. Respeite a branch designada e não force-push.
