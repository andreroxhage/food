# Meal Planning with AI Agents

A HelloFresh-inspired meal planning system for Swedish households, powered by Claude Code's multi-agent architecture. Plan your week's meals through a structured 4-phase workflow with specialized AI agents that research recipes in parallel, generate shopping lists, and create optimized prep plans.

## How It Works

```
You: "Planera mat för veckan — vi vill ha hög protein, snabba vardagsrätter"
                              ↓
         ┌──────────────────────────────────────┐
         │     Phase 1: Brainstorming Agent      │
         │   Generates 10-20 meal candidates     │
         └──────────────┬───────────────────────┘
                        ↓ You pick dishes
    ┌───────────────────────────────────────────────┐
    │        Phase 2: Parallel Recipe Research       │
    │  ┌─────────┐ ┌─────────┐ ┌─────────┐         │
    │  │Researcher│ │Researcher│ │Researcher│  × N   │
    │  │ Dish #1  │ │ Dish #2  │ │ Dish #3  │        │
    │  └─────────┘ └─────────┘ └─────────┘         │
    │  Each compares 3-5 sources for best quality   │
    │  + Recipe Creator for custom dishes           │
    └───────────────────┬───────────────────────────┘
                        ↓ You approve
         ┌──────────────────────────────────────┐
         │   Phase 3: Shopping List Generator    │
         │  Pools & normalizes all ingredients   │
         └──────────────┬───────────────────────┘
                        ↓ You approve
         ┌──────────────────────────────────────┐
         │     Phase 4: Meal Prep Optimizer      │
         │  Time-optimized cooking timeline      │
         └──────────────────────────────────────┘
```

Each phase has a **mandatory stop point** — the system never proceeds without your explicit approval.

## Features

- **Multi-agent orchestration** — Specialized agents for each phase, with parallel recipe research
- **Quality-first recipe sourcing** — Compares multiple sources (Köket, Tasteline, Arla, international) to find the best recipe, not just the first one
- **Custom recipe creation** — AI writes professional-quality recipes when no good source exists
- **Smart shopping lists** — Pools ingredients across all recipes, normalizes units, categorizes by store section
- **Optimized meal prep** — Parallelizes cooking tasks (oven + stovetop + cold prep) to minimize total time
- **Swedish-first** — All output in Swedish with metric units and Swedish grocery store names

## Getting Started

### Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- Clone this repository

### Usage

**Interactive (recommended):**
```bash
cd food
claude
# Then say: "Planera mat för veckan" or use /meal-planning-hello-fresh
```

**With orchestrator agent:**
```bash
claude --agent meal-planning-orchestrator
```

**Create a custom recipe:**
```
/create-recipe kycklingfajitas 6
```

## Project Structure

```
.claude/
├── agents/                          # Specialized AI agents
│   ├── meal-planning-orchestrator   # Coordinates the full workflow
│   ├── brainstorming-agent          # Phase 1: meal ideation
│   ├── recipe-researcher            # Phase 2: parallel recipe search
│   ├── recipe-creator               # Phase 2: custom recipe writing
│   ├── shopping-list-generator      # Phase 3: pooled shopping list
│   ├── meal-prep-optimizer          # Phase 4: prep timeline
│   └── codebase-workflow-analyzer   # Meta: improve the workflow itself
├── skills/
│   ├── meal-planning-hello-fresh/   # Main workflow skill
│   │   ├── SKILL.md                 # Orchestration instructions
│   │   ├── reference.md             # Sources, units, categories
│   │   └── examples.md              # Output format examples
│   └── create-recipe/               # /create-recipe command
│       └── SKILL.md
YYYY-MM-DD/                          # Weekly meal plans (date folders)
├── 01-brainstorming.md
├── 02-receptval.md
├── recept-*.md                      # Custom recipes
├── 03-handlingslista.md
└── 04-meal-prep-plan.md
```

## Agent Architecture

| Agent | Phase | Model | Purpose | Runs in Parallel? |
|---|---|---|---|---|
| `brainstorming-agent` | 1 | Sonnet | Generate meal candidates | No |
| `recipe-researcher` | 2 | Sonnet | Find best recipe per dish | **Yes (one per dish)** |
| `recipe-creator` | 2 | Inherit | Write custom recipes | Per recipe |
| `shopping-list-generator` | 3 | Sonnet | Pool ingredients | No |
| `meal-prep-optimizer` | 4 | Inherit | Optimize prep timeline | No |

The **key innovation** is Phase 2: instead of searching for recipes sequentially, the orchestrator spawns one `recipe-researcher` agent per dish, all running in parallel. Each researcher independently compares 3-5 sources to find the highest quality recipe.

## Customization

### Defaults

- **Portions**: 6 per recipe
- **Meals**: Lunch + dinner
- **Protein**: High protein, varied (not low-carb, not vegan)
- **Language**: Swedish

Override any default by telling the system your preferences in Phase 1.

### Recipe Sources (Priority Order)

1. Köket.se, Tasteline, Arla, Landleys Kök, Mitt Kök
2. International sources for authentic cuisine (Serious Eats, Woks of Life, etc.)
3. ICA, Coop (secondary)

### Extending the System

Use the `codebase-workflow-analyzer` agent to design new agents:
```
Use the codebase-workflow-analyzer to create a budget optimizer agent
```

## Example Output

See [2026-02-23/](2026-02-23/) for a complete example of a weekly meal plan with all 4 phases.

## License

MIT
