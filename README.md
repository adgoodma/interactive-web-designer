# Interactive Web Designer

An AI agent skill for designing immersive, accessible interactive websites: scroll storytelling, WebGL and 3D heroes, kinetic typography, transitions, micro-interactions, Liquid Glass interfaces, glass navigation, and Power BI-style HTML reports.

This repository contains instructions and reference templates, not a standalone application or a hosted website. It does not create actual Power BI `.pbix` reports or provide live Power BI data access.

## Contents

| File | Purpose |
|---|---|
| [SKILL.md](SKILL.md) | Skill metadata, triggers, design workflow, and guardrails |
| [references/transitions.md](references/transitions.md) | Transition patterns, tooling, and reduced-motion guidance |
| [references/react-bits.md](references/react-bits.md) | React Bits component catalogue and integration guidance |
| [references/liquid-glass.md](references/liquid-glass.md) | Liquid Glass design system and implementation examples |
| [references/glass-navigation.md](references/glass-navigation.md) | Frosted sidebar specification and single-file template |
| [references/visual-powerbi-report.md](references/visual-powerbi-report.md) | Interactive HTML report specification and template |

Keep `SKILL.md` and the complete `references` folder together. The original internal skill-quality report is intentionally not distributed.

## Download

Once this repository has been created and its files committed, use **Code > Download ZIP**, then extract the archive. Alternatively:

```shell
git clone https://github.com/adgoodma/interactive-web-designer.git
```

While the repository is private, downloads and clones require an account with repository access. A private repository is not a public skill listing, and creating it does not deploy a website.

## Install

### GitHub Copilot

For a project-scoped installation in a Copilot client that supports agent skills, copy this repository's `SKILL.md` and `references` folder into:

```text
your-project/
  .github/
    skills/
      interactive-web-designer/
        SKILL.md
        references/
          ...
```

Follow your client's current agent-skill discovery and enablement instructions. Skill support varies across Copilot products and versions.

### Cowork and other skill-capable clients

Import or install the `interactive-web-designer` folder using the client's supported custom-skill workflow. If the client accepts a skill ZIP, package `SKILL.md` and `references` inside one top-level `interactive-web-designer` folder. This repository does not include an installer or bundled companion skills.

## Host requirements and portability

The supplied skill is preserved from its source. Its workflow refers to host-specific capabilities that may not be available in another client:

- The `html` companion skill handles default single-file HTML delivery into `output/`.
- `app-builder`, `canvas`, `render-ui`, and `powerbi` are optional routing targets for other kinds of work. Document-oriented routing also names `docx`, `pptx`, and `xlsx`.
- `AskUserQuestion`, file search, file creation, and rendered browser previews require equivalent tools in the host.

These companion skills and tools are **not included**. Some reference templates can be used without third-party libraries, but the complete workflow is not guaranteed to run unchanged in every host. If a capability is missing, have the agent identify it and agree on an available equivalent rather than claim it used an unavailable tool.

Generated experiences may use third-party libraries or CDNs. Check their current licences, pin versions, and review network dependencies before deployment. No package installation is required merely to read or install these skill instructions.

## Example prompts

```text
Use interactive-web-designer to plan a premium interactive landing page.
Start by confirming the capabilities, colour palette, and tone.
```

```text
Design a Liquid Glass dashboard with a collapsible glass sidebar.
Use clearly labelled sample data and respect reduced-motion preferences.
```

```text
Create a Power BI-style HTML report with slicers and cross-filtered visuals.
This is an HTML prototype, not a live Power BI report.
```

The skill starts with capability, colour, and tone alignment before building. Review generated work for responsive layout, keyboard access, contrast, reduced motion, performance, data accuracy, and third-party rights before sharing or deploying it.

## Rights and attribution

No repository licence has been selected yet. Access to this repository does not by itself grant a general licence to redistribute or modify its original material. The owner should choose appropriate licence terms before broader distribution.

Third-party libraries, assets, and linked examples retain their own terms. In particular, the skill references React Bits under MIT plus Commons Clause terms and a Figma kit attributed as CC BY 4.0; verify the current upstream terms before reuse. This repository does not relicense third-party material or bundle the linked asset collections.

Product and organisation names are used for identification or design-reference purposes; no endorsement or affiliation is implied.
