# Pokédoro

> Pomodoro gamificado com Pokémon — repositório `pokedoro`.

O **Pokédoro** é uma SPA que combina a Técnica Pomodoro com um sistema de progressão de Pokémon. Escolha um Pokémon inicial, conclua ciclos de foco e descanso para ganhar XP, suba de nível, evolua seu companheiro e acompanhe todo o histórico de tarefas.

O projeto roda inteiramente no navegador e usa `localStorage` para persistir o progresso — não é necessário criar conta nem configurar um backend.

## Funcionalidades

- **Timer Pomodoro completo** com ciclos de foco, descanso curto e descanso longo.
- **Ciclos automáticos**: o app calcula e indica o próximo ciclo (foco ou pausa) a cada tarefa concluída.
- **Escolha do Pokémon inicial**: Bulbasaur, Charmander ou Squirtle.
- **Progressão e evolução**: cada ciclo concluído concede XP, e o Pokémon evolui ao atingir os níveis 16 e 32.
- **Histórico de tarefas** com status (completa, interrompida, em progresso ou abandonada), tipo de ciclo, duração e data, com ordenação por coluna.
- **Configurações personalizáveis** de tempo de foco, descanso curto e descanso longo.
- **Troca de Pokémon** com confirmação, resetando o progresso atual.
- **Tema claro e escuro** com preferência salva no navegador.
- **Alertas e confirmações** via toasts (`react-toastify`).
- **Som ao finalizar um ciclo**, reproduzido por um elemento `Audio`.
- **Progresso persistente** no `localStorage`, incluindo tarefas, Pokémon e tema.
- **Timer baseado em Web Worker**, que recalcula o tempo restante com `Date.now()` e reduz a dependência do loop da thread principal.

## Como funciona a mecânica

### Ciclos

Cada ciclo recebe um tipo de acordo com a posição atual:

| Ciclos | Tipo | Duração padrão |
| --- | --- | --- |
| 1, 3, 5, 7 | Foco | 25 min |
| 2, 4, 6 | Descanso curto | 5 min |
| 8 | Descanso longo | 30 min |

Ao concluir o ciclo 8, a contagem reinicia no ciclo 1. Os tempos padrão podem ser alterados em **Configurações**:

- Foco: 1 a 99 minutos
- Descanso curto: 1 a 30 minutos
- Descanso longo: 1 a 60 minutos

### XP e evolução

- Cada ciclo concluído concede **443 XP**.
- A cada **100 XP** o Pokémon sobe um nível.
- As evoluções acontecem nos níveis **16** e **32**.

| Inicial | Nível 16 | Nível 32 |
| --- | --- | --- |
| Bulbasaur | Ivysaur | Venusaur |
| Charmander | Charmeleon | Charizard |
| Squirtle | Wartortle | Blastoise |

> Interromper uma tarefa não concede XP. O XP é creditado apenas quando o timer chega a zero.

## Stack

| Tecnologia | Uso no projeto |
| --- | --- |
| [React 19](https://react.dev/) | Interface e composição de componentes |
| [TypeScript](https://www.typescriptlang.org/) | Tipagem de modelos, contextos e ações |
| [Vite 7](https://vite.dev/) | Dev server, build e HMR |
| [React Router 7](https://reactrouter.com/) | Rotas da SPA |
| [React Toastify](https://fkhadra.github.io/react-toastify/) | Notificações e diálogos de confirmação |
| [Lucide React](https://lucide.dev/) | Ícones |
| [date-fns](https://date-fns.org/) | Formatação de datas |
| CSS Modules + CSS global | Estilos por componente e temas claro/escuro |
| Web Worker + `localStorage` | Timer em segundo plano e persistência local |

## Estrutura do projeto

```text
src/
├── adapters/          # Adaptador de mensagens (toasts e diálogos)
├── assets/            # Áudios e imagens importados pelo app
├── components/        # Componentes reutilizáveis (CountDown, MainForm, Menu, etc.)
├── contexts/
│   ├── PokemonContext/  # Estado, provider e hook do Pokémon
│   └── TaskContext/     # Estado global de tarefas, reducer e ações
├── models/            # Tipos de domínio (Task, Pokemon, estados)
├── pages/             # Home, ChoosePokemon, History, Settings, AboutPomodoro, NotFound
├── routes/            # Configuração das rotas
├── styles/            # CSS global e variáveis de tema
├── templates/         # Layout base (MainTemplate)
├── utils/             # Formatação, ciclos, status, áudio e regras de Pokémon
├── workes/            # TimerWorkerManager e o Web Worker do timer
├── App.tsx            # Providers globais da aplicação
└── main.tsx           # Ponto de entrada
```

## Rodando localmente

### Pré-requisitos

- [Node.js](https://nodejs.org/) **20.19+** ou **22.12+**
- npm (incluído com o Node.js)

### Instalação

```bash
git clone https://github.com/Matheus-Perpetuo/pokedoro.git
cd pokedoro
npm install
```

### Desenvolvimento

```bash
npm run dev
```

Acesse `http://localhost:5173`.

### Scripts disponíveis

| Script | Descrição |
| --- | --- |
| `npm run dev` | Inicia o servidor de desenvolvimento com HMR |
| `npm run build` | Executa a checagem de tipos e gera o build de produção em `dist/` |
| `npm run preview` | Serve localmente o build de produção |
| `npm run lint` | Executa o ESLint em todo o projeto |

## Rotas

| Rota | Página |
| --- | --- |
| `/` | Home com timer, Pokémon e formulário de tarefas |
| `/choose-pokemon` | Escolha do Pokémon inicial |
| `/history/` | Histórico de tarefas |
| `/settings/` | Configurações de tempo e reset do Pokémon |
| `/about-pomodoro/` | Explicação da Técnica Pomodoro |
| `*` | Página 404 |

## Persistência

Os dados ficam no `localStorage` do navegador:

| Chave | Conteúdo |
| --- | --- |
| `state` | Tarefas, ciclo atual e configurações de tempo |
| `pokemon` | Pokémon atual, nível, XP e sprite |
| `theme` | Tema claro ou escuro |

Ao recarregar a página, o histórico e o progresso são mantidos, mas uma tarefa em andamento não é retomada: o estado do timer ativo é limpo na inicialização.

Os sprites animados dos Pokémon são carregados de URLs externas do repositório de sprites da PokeAPI, então é necessário ter conexão com a internet para exibi-los.

## Build e deploy

```bash
npm run build
npm run preview
```

O build de produção é gerado em `dist/`. O projeto inclui um `vercel.json` com rewrite para `index.html`, necessário para o funcionamento das rotas da SPA em deploy na [Vercel](https://vercel.com/).

## Contribuindo

1. Faça um fork do projeto.
2. Crie uma branch para sua alteração: `git checkout -b minha-feature`.
3. Commit as mudanças: `git commit -m "feat: minha feature"`.
4. Envie a branch: `git push origin minha-feature`.
5. Abra um Pull Request.

Sugestões, correções e melhorias são bem-vindas.

---

Feito com React, TypeScript e muita vontade de evoluir. 🍅⚡
