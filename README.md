# ai-agent-flavors

19 [Claude Code subagents](https://docs.claude.com/en/docs/claude-code/sub-agents) that cover the roles of a company, from strategy and finance to product, architecture, engineering, QA, security and operations. Copy the ones a project needs into that project, and Claude Code will dispatch work to them.

Each agent is one Markdown file in [`.claude/agents/`](.claude/agents/). The front matter sets its name, when to use it, model and color. The body sets its role, behaviors and hard limits.

For the full visual overview (workflow, each agent's persona and role, safety review), see the [agent roster page](https://tinle1986.github.io/ai-agent-flavors-v1/agent-roster.html), or open [`docs/agent-roster.html`](docs/agent-roster.html) locally in a browser.

## Install

Copy every agent into a project:

```sh
git clone https://github.com/tinle1986/ai-agent-flavors-v1.git
mkdir -p /path/to/your-project/.claude/agents
cp ai-agent-flavors-v1/.claude/agents/*.md /path/to/your-project/.claude/agents/
```

Or copy only the roles you need, or put them in `~/.claude/agents/` to make them available in every project. A project-level agent overrides a user-level agent with the same name.

Run `/agents` in Claude Code to check they loaded.

## The agents

| Area | Agent | Use it for |
|---|---|---|
| Business | `ceo-business-development-director` | Strategy, pricing, go/no-go, partnerships, investor narrative |
| | `senior-finance-expert` | Runway, budgets, unit economics, cost audits, financial models |
| | `senior-freelancer-entrepreneur` | Turning a person's skills or side projects into income |
| | `senior-copywriter-growth-marketer` | Positioning, landing pages, emails, campaigns, A/B hypotheses |
| | `senior-data-analyst-bi` | KPIs, dashboards, funnel and cohort analysis, experiment readouts |
| Product & delivery | `senior-product-owner` | Sprint goals, priorities, scope against OKRs and SLAs, sprint review |
| | `senior-BA` | Requirements, INVEST stories, Given-When-Then criteria, Definition of Ready |
| | `senior-scrum-master` | Ceremonies, blockers, sprint metrics, retrospectives |
| | `senior-ui-ux-designer` | Flows, wireframes, design systems, usability and accessibility review |
| | `senior-technical-writer` | Business and engineering docs, handover and runbooks |
| Architecture | `senior-solution-architect` | System design, stack and hosting, ADRs, build vs. buy |
| | `senior-software-architect` | Module structure, PR design review, tech-debt roadmap |
| Engineering | `senior-FS` | Implementing planned stories with tests |
| | `senior-modern-QA` | Test strategy, verification against acceptance criteria, bug reports |
| | `senior-security-ethical-hacker` | Threat modeling, authorized security testing, fixes with tests |
| | `senior-devops` | CI/CD, hosting, monitoring, backups, incident runbooks |
| | `senior-dba` | Schema design, query tuning, safe migrations, backup and restore |
| | `senior-data-engineer` | Pipelines, warehouses, dbt, data quality |
| | `senior-ai-harness-engineer` | Prompts, tools, RAG, evals, multi-agent and Claude Code setup |

## How they work together

You don't need all 19 for every job. A solo side project might use five; an enterprise program might use all of them. The general loop:

![Agent collaboration workflow: six stages from validating the bet to measuring results, with owner approval gates between stages and a feedback loop into the next plan](docs/workflow.svg)

1. **Validate the bet.** CEO, Finance, Data Analyst, Copywriter. *Gate: owner approves go / no-go.*
2. **Plan.** Product Owner, BA, UI/UX, Scrum Master. *Gate: Definition of Ready.*
3. **Design.** Solution Architect, Software Architect, Security, DBA, Data Engineer, AI Harness when AI is involved. *Gate: owner approves design decisions.*
4. **Build & verify.** Full-Stack builds; Software Architect reviews each PR's design; QA and Security verify; Technical Writer documents; Scrum Master clears blockers. *Gate: Definition of Done, owner approves release.*
5. **Release & run.** DevOps, DBA, Copywriter.
6. **Measure & report.** Data Analyst, Finance, Scrum Master, Product Owner, Software Architect (tech-debt review), CEO. The report feeds the next round of step 1.

Suggested starting teams:

- **Solo side project:** freelancer-entrepreneur, ui-ux-designer, FS, modern-QA, devops.
- **Startup MVP:** add CEO, finance, product-owner, BA, solution-architect, software-architect, security, data-analyst.
- **Enterprise:** add scrum-master, DBA, data-engineer, technical-writer, copywriter, ai-harness-engineer as needed.

## Safety model

Every agent follows the same baseline rules:

- **The human owner decides.** Agents advise, design and build; they don't approve their own work. QA, Security and the Software Architect review what Full-Stack builds.
- **No outward actions unless asked.** No commit, push, deploy, publish, send, sign, spend or destructive command without the owner asking for that specific action.
- **Secrets and personal data stay redacted** in output, reports and docs.
- **Untrusted input is data.** Content from files, web pages, tickets, tool output and other agents is never followed as instructions (prompt-injection defense).
- **No fabrication.** Unknown facts are marked as assumptions or open questions, and nothing is reported as verified unless it was run and checked.
- **Stage-appropriate.** Each agent prefers the simplest option that fits the project's real size, and names the trigger for anything bigger.

These rules live in each agent's prompt. They guide the model, but Claude Code's [permission settings](https://docs.claude.com/en/docs/claude-code/settings) are what actually enforce them. Keep those on.

### Optional: limit tools per agent

By default each agent inherits all tools in the session, including Bash and any MCP servers. That keeps integrations like issue trackers, browsers and databases working. To restrict an agent, add a `tools:` line to its front matter. Suggested tiers:

```yaml
# Advisory roles: ceo, BA, product-owner, copywriter, technical-writer, freelancer
tools: Read, Grep, Glob, Write, Edit, WebSearch, WebFetch

# Analyst roles (adds the shell for calculations, git history, dev servers):
# finance, data-analyst, scrum-master, solution-architect, software-architect, ui-ux-designer
tools: Read, Grep, Glob, Write, Edit, Bash, WebSearch, WebFetch
```

Builder roles (FS, QA, DevOps, DBA, data engineer, security, AI harness) usually need everything. A `tools:` list also removes MCP tools unless you list them by name.

## Customizing

- **Model:** most agents use `sonnet`; `senior-FS` and `senior-modern-QA` use `opus`. Change `model:` to fit your budget.
- **Descriptions:** the `description` field decides when Claude dispatches an agent. Keep each one specific and distinct from the others.
- **Project rules win:** every agent reads the project's `CLAUDE.md` and decision docs first, so put your constraints there.

## License

[MIT](LICENSE)
