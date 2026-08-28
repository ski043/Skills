# Skills

A growing collection of reusable agent skills.

[![skills.sh](https://skills.sh/b/ski043/Skills)](https://skills.sh/ski043/Skills)

## Available skills

### Feature Orchestrator

Feature Orchestrator supports the first half of a two-session engineering workflow:

1. Explore and refine a feature through product and technical discussion.
2. Turn the settled decisions into a copy-ready orchestration prompt for a separate implementation session.

The implementation session owns its plan, coordinates research and implementation agents, verifies the completed feature, runs bounded independent review, and prepares an evidence-backed pull request proposal.

Feature Orchestrator uses GPT-5.6 Sol at Extra High reasoning for the primary implementation agent, implementation sub-agents, and fixes. It uses GPT-5.6 Terra at Extra High reasoning for research, repository exploration, and independent review.

## Install

### skills.sh

Install Feature Orchestrator from your project root:

```bash
npx skills add ski043/Skills --skill feature-orchestrator
```

The skills.sh CLI installs into the current project by default. Add `--global` to make the skill available across your projects:

```bash
npx skills add ski043/Skills --skill feature-orchestrator --global
```

Start a new agent session after installation. To pull later updates, run:

```bash
npx skills update feature-orchestrator
```

Browse the repository on the [skills.sh directory](https://skills.sh/ski043/Skills). Repository pages are indexed after the CLI sees an installation, so a new listing may take a little time to appear.

### Local development

For local development, compatible agent development environments can discover symlinked skills from the shared skills directory:

```bash
git clone https://github.com/ski043/Skills.git
mkdir -p "$HOME/.agents/skills"
ln -s "$PWD/Skills/skills/feature-orchestrator" "$HOME/.agents/skills/feature-orchestrator"
```

## Use

Invoke the skill explicitly:

```text
$feature-orchestrator Help me think through this feature. Do not implement it yet.
```

Feature Orchestrator will stay in the product and technical discussion until you explicitly ask it to compile the implementation prompt.

## Repository structure

Each public skill lives in its own directory under `skills/`:

```text
skills/
└── feature-orchestrator/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        └── execution-prompt-contract.md
```

The root [`skills.sh.json`](skills.sh.json) controls how skills are grouped on this repository's skills.sh page as the collection grows.

## License

Released under the [MIT License](LICENSE).
