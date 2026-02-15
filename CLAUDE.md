# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a meal planning repository that implements a HelloFresh-like workflow for Swedish households. The system helps with weekly meal planning through a structured 4-phase process: brainstorming → recipe selection → shopping list generation → meal prep planning.

## Repository Structure

```
YYYY-MM-DD/                      # Date-based meal planning folders
├── 01-brainstorming.md          # Meal preferences + candidate meals
├── 02-receptval.md              # Selected recipes with links/sources
├── recept-*.md                  # Custom recipe files (when applicable)
├── 03-handlingslista.md         # Pooled shopping list (generated on request)
└── 04-meal-prep-plan.md         # Optimized prep timeline (generated on request)

.cursor/
├── rules/meal-planning-workflow.mdc    # Core workflow rules
├── agents/meal-planning-specialist.md  # Agent definition
└── skills/meal-planning-hello-fresh/   # Meal planning skill
```

## Core Workflow & Architecture

### Phase-Gated Process (CRITICAL)

The workflow has **mandatory stop points** between phases. Never proceed to the next phase without explicit user approval:

1. **Phase 1 (Brainstorming)**: Generate meal candidates based on preferences
   - Stop and wait: User selects dishes or provides own recipes

2. **Phase 2 (Recipe Selection)**: Document chosen recipes with links and portion scaling
   - Stop and wait: Ask "Vill du att jag skapar handlingslista nu?"

3. **Phase 3 (Shopping List)**: Generate pooled, consolidated shopping list
   - Stop and wait: Ask "Vill du att jag skapar meal prep-plan nu?"

4. **Phase 4 (Meal Prep)**: Create optimized preparation timeline

### Language & Units

- **Language**: Swedish only
- **Units**: Metric (g, kg, ml, dl, l, msk, tsk, st)
- **Normalization**: Convert to clearer units when appropriate (1000g → 1kg, 10dl → 1l)

### Recipe Sources Priority

**User is a professional chef - prioritize QUALITY over convenience**

1. **Best quality sources** (Swedish or international):
   - Köket.se (high-quality Swedish recipes, chef-tested)
   - Arla (especially for dairy-based dishes)
   - Mitt Kök (authentic Nordic cuisine)
   - Tasteline (professional Swedish chefs)
   - Landleys Kök (high-quality home cooking)
   - International sources when authenticity matters (e.g., Asian cuisine from authentic sources)

2. **Secondary sources**:
   - ICA, Coop (good for basic recipes but not priority)
   - Food blogs (if quality is demonstrably high)

3. **Search strategy**:
   - Compare multiple sources for each recipe
   - Prioritize recipes with professional chef backgrounds
   - Look for techniques and flavor profiles that elevate the dish
   - Balance quality with weekday feasibility (not too advanced)

4. **Always provide clickable links**

### Default Parameters

- **Portions**: 6 per recipe (unless specified otherwise)
- **Meals**: Lunch + dinner
- **Protein focus**: High protein but varied (not low-carb, not vegan)
- **Scaling**: Calculate ingredient scaling factors when portions differ from recipe

### Shopping List Generation (Phase 3)

- **Pool ingredients** across all selected recipes
- **Normalize units** for clarity
- **Use butiksvänliga names** (Swedish grocery store names)
- **Categorize**: Grönsaker, Frukt, Mejeri & Ägg, Kött & Fisk, Skafferi, Kryddor & Såser, Fryst, Bröd, Övrigt
- **Pantry assumptions**: List separately (salt, pepper, oils) - don't assume silently
- **Mark uncertainties**: Use "(verifiera)" instead of guessing

### Custom Recipes

When creating custom recipes, save as `YYYY-MM-DD/recept-<slug>.md` and reference from `02-receptval.md` as "Eget recept: `recept-<slug>.md`"

### Meal Prep Planning (Phase 4)

Optimize for minimal total time by:
- Grouping similar tasks (chop all vegetables at once, cook all rice together)
- Parallelizing independent tasks (oven + stovetop + cold prep)
- Reusing bases/sauces when appropriate without sacrificing variety

## Specialized Agents

The repository includes specialized agents for each phase of the workflow:

1. **brainstorming-agent** (`.cursor/agents/brainstorming-agent.md`):
   - Gathers user preferences (protein focus, time constraints, equipment)
   - Generates 10-20 meal candidates with motivations and tags
   - Creates `01-brainstorming.md`
   - Stops and waits for user to select dishes

2. **recipe-selection-agent** (`.cursor/agents/recipe-selection-agent.md`):
   - Finds BEST QUALITY recipe links (Köket, Tasteline, Arla, international sources)
   - Compares multiple sources to find superior recipes
   - Creates custom recipes when needed (saved as `recept-*.md`)
   - Calculates portion scaling factors
   - Creates `02-receptval.md`
   - Stops and asks: "Vill du att jag skapar handlingslista nu?"

3. **shopping-list-agent** (`.cursor/agents/shopping-list-agent.md`):
   - Pools ingredients across all recipes
   - Normalizes units (1000g → 1kg, 10dl → 1l)
   - Categorizes by grocery store section
   - Separates pantry assumptions (salt, oil, etc.)
   - Creates `03-handlingslista.md`
   - Stops and asks: "Vill du att jag skapar meal prep-plan nu?"

4. **meal-prep-optimizer** (`.cursor/agents/meal-prep-optimizer.md`):
   - Creates time-optimized preparation timeline
   - Parallelizes tasks (oven + stovetop + cold prep)
   - Batches similar operations (chop all vegetables at once)
   - Includes storage instructions and shelf life
   - Creates `04-meal-prep-plan.md`
   - Final phase (no stop point after)

5. **meal-planning-specialist** (`.cursor/agents/meal-planning-specialist.md`):
   - General-purpose agent that can handle all 4 phases
   - Use when you want one agent for the complete workflow

## Key Files Reference

- `.cursor/rules/meal-planning-workflow.mdc`: Core workflow rules enforced in Cursor
- `.cursor/skills/meal-planning-hello-fresh/SKILL.md`: Complete skill definition
- `.cursor/skills/meal-planning-hello-fresh/reference.md`: Recipe source priorities, unit conversions, category definitions
- `.cursor/agents/`: Specialized agents for each workflow phase (see above)

## Working with Date Folders

When starting a new week:
1. Create folder: `YYYY-MM-DD/` (use the Monday of that week)
2. Start with Phase 1 brainstorming
3. Follow the phase-gated workflow strictly
4. Reference previous weeks' folders for inspiration but start fresh each time
