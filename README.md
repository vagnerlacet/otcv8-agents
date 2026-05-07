# otcv8-agents

Coleção de **subagentes especializados do Claude Code** para trabalhar no
[OTCv8/otcv8-dev](https://github.com/OTCv8/otcv8-dev) — o cliente de jogo
open-source escrito em C++ + Lua, baseado em CMake e voltado para Windows,
Linux e Android.

O objetivo deste repositório **não é** hospedar código do OTCv8, e sim manter
agentes, skills, prompts e configurações reutilizáveis que aceleram o
desenvolvimento, revisão e manutenção daquele projeto.

## Para que servem os agentes

O OTCv8 mistura várias camadas técnicas (engine em C++, scripts em Lua,
layouts OTUI, build via CMake/vcpkg, três plataformas alvo). Cada agente é
focado em uma dessas camadas para responder e agir com mais precisão do que
um agente genérico.

Áreas planejadas para cobertura:

- **Engine C++ (`src/`)** — análise de código, refatoração, integração com
  Lua, cuidados com ABI e portabilidade.
- **Módulos Lua (`modules/`, `mods/`)** — convenções do framework de módulos
  do OTClient, ciclo de vida (`onLoad`/`onUnload`), uso de `g_ui`, sinais.
- **UI / OTUI (`layouts/`, `*.otui`)** — sintaxe OTUI, anchors, estilos,
  binding com controllers Lua.
- **Build & toolchain (CMake, vcpkg, NDK)** — diagnosticar falhas de build
  nas três plataformas, alinhar com os commits de vcpkg pinados pelo projeto.
- **Protocolo & rede** — pacotes Tibia/OT, compatibilidade de versões.
- **Revisão de PRs para o upstream** — checar se mudanças seguem a regra de
  "features customizadas devem ser opcionais e configuráveis".

## Estrutura

```
.
├── README.md          # este arquivo
├── CLAUDE.md          # contexto para o Claude Code operar neste repo
└── .claude/
    └── agents/        # definições de subagentes (um .md por agente)
```

Cada agente vive em `.claude/agents/<nome>.md` no formato padrão do Claude
Code (frontmatter YAML com `name`, `description`, `tools` opcionais, seguido
do system prompt).

## Como usar

1. Clone este repositório ao lado do seu checkout do `otcv8-dev`, **ou** copie
   o diretório `.claude/agents/` para dentro do seu projeto OTCv8.
2. Abra o Claude Code no diretório do OTCv8.
3. Os agentes ficam disponíveis automaticamente para o Claude delegar via
   `Agent`, ou você pode invocá-los explicitamente:
   `> use o agente otcv8-lua para revisar modules/game_healthbars`.

> Os agentes assumem que o working directory é uma cópia do `otcv8-dev` (ou
> compatível). Caminhos como `src/`, `modules/`, `mods/`, `layouts/` e
> `CMakeLists.txt` são referenciados diretamente.

## Contribuindo com um novo agente

1. Crie `.claude/agents/<nome>.md`.
2. Frontmatter mínimo:
   ```yaml
   ---
   name: otcv8-<area>
   description: Quando usar este agente (frase clara, em terceira pessoa).
   ---
   ```
3. No corpo, escreva o system prompt: escopo, convenções específicas do
   OTCv8 que ele deve seguir, ferramentas que pode usar, e o que **não**
   deve fazer.
4. Sempre que possível, restrinja `tools:` ao mínimo necessário (ex.: um
   agente de revisão raramente precisa de `Write` ou `Bash`).

## Referências

- Upstream: <https://github.com/OTCv8/otcv8-dev>
- Binários prontos: <https://github.com/OTCv8/otclientv8>
- Documentação Claude Code (subagentes):
  <https://docs.claude.com/en/docs/claude-code/sub-agents>
