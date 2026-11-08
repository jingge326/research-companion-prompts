# research-companion-prompts

Prompt files that make AI agents, such as Claude Code and Codex, better partners for developing research ideas and writing papers. The researcher stays in charge: the agent discusses, checks, drafts, and reviews, and the researcher decides.

Claude Code is supported now. A Codex version is planned.

## Motivation

Agents such as Claude Code can read a manuscript, search the literature, build LaTeX, and edit files, which makes them useful for research. Their default instructions, however, are written for software engineering. In research work, language models also show known weaknesses:

- They tend to agree with the user and give up correct positions when challenged ([Sharma et al., 2023](https://arxiv.org/abs/2310.13548)).
- When they summarize research, they state findings more broadly than the source does ([Peters and Chin-Yee, 2025](https://arxiv.org/abs/2504.00025)).
- Their writing tends to be noun-heavy, vague, and formulaic ([Reinhart et al., 2025](https://arxiv.org/abs/2410.16107); [Gao et al., 2023](https://www.nature.com/articles/s41746-023-00819-6)).
- They correct their own reasoning poorly without outside feedback ([Huang et al., 2023](https://arxiv.org/abs/2310.01798)).

In our own use, agents also made claims about prior work without checking it, and proposed alternatives they did not believe were better. The prompts in this repository replace the coding instructions with instructions for research work, including rules aimed at these problems.

## Contents

| Path | Purpose |
| --- | --- |
| `claude-code/output-styles/research-companion.md` | Output style that turns Claude Code into a research companion |
| `examples/CLAUDE.md` | Example project file that describes one paper to the agent |

## What the agent does

| Area | Behavior |
| --- | --- |
| Discussing ideas | Gives its own judgment. It agrees when you are right, disagrees when you are wrong, and changes its view only for a new reason or new evidence. It offers its own ideas when it believes they are better, and aims high when asked for contributions. It says whether a claim comes from a paper, from data, or is its own guess. |
| Evidence | Searches the literature and the web before making claims about the field, and reads a paper before describing it. It never invents numbers, citations, or agreements. It marks a missing number or source with a visible `[TODO: ...]` in the text. It cites only entries in your bibliography and checks new ones against a primary source. |
| Writing | Writes plain, specific prose for reviewers, matches each claim to its evidence, and avoids templated phrasing |
| Editing | Changes only the text a task covers, keeps wording you have accepted, and proposes large restructuring in chat first. It builds the paper after editing and reports what changed. |
| Review | Reads as a reviewer at the target venue, ranks issues by their effect on the decision, and separates required changes from optional ones. It uses a separate subagent for an independent review. |

## Quick start with Claude Code

Requires Claude Code 2.1.269 or later. Tested with 2.1.316.

1. Clone this repository and copy the style into your Claude Code user folder, `~/.claude`:

   ```bash
   git clone https://github.com/jingge326/research-companion-prompts.git
   cd research-companion-prompts
   mkdir -p ~/.claude/output-styles
   cp claude-code/output-styles/research-companion.md ~/.claude/output-styles/
   ```

2. Copy `examples/CLAUDE.md` to the root of your paper project and fill in your paper's details. The paper project is the folder that holds your manuscript, where you start Claude Code to work on the paper.

3. Start Claude Code in your paper project and turn on the style. In the terminal, run `/output-style research-companion`. In VS Code, type `/`, open "Output styles", and choose `research-companion`. Claude Code saves the choice in the project's `.claude/settings.local.json`, so it applies to this project only.

4. Optional: to let the agent read folders outside the project without asking each time, such as a literature folder, add these keys to the same file:

   ```json
   {
     "outputStyle": "research-companion",
     "permissions": {
       "additionalDirectories": ["/path/to/literature"]
     }
   }
   ```

   If you create this file by hand instead of with step 3, add `.claude/settings.local.json` to the project's `.gitignore`.

To update, run `git pull`, copy the style file again, and restart Claude Code. To turn the style off in a project, switch back to the Default style with `/output-style` or the VS Code menu.

## How it works

Claude Code's default system prompt is written for software work. A custom output style replaces the software engineering section of that prompt with the instructions from the style file. The tool and safety instructions stay, so the agent can still read and edit files, build LaTeX, search the web, and use subagents. A style is active only in the projects where you turn it on, so your other projects keep the default behavior.

The style is the same for every paper. Facts about one paper, such as its venue, stage, and build command, go into the paper project's `CLAUDE.md`, which Claude Code loads at the start of every session in that project.

## Recommended workflow

- Keep writing in its own project, separate from code and experiments. Your coding projects then keep Claude Code's default prompt.
- Start a new session when you switch between writing and coding. Long sessions fill with logs and code, and the paper competes with them for the agent's attention.
- For an independent review, ask the agent to have a fresh subagent review a section. A fresh subagent reads the text without your discussion, so it judges the text as a reviewer would.
- When the paper is ready for submission, change "Stage: drafting" to "Stage: submission" in `CLAUDE.md`. The agent then trims the text to the page limit.
- To have the agent keep a decision log, name a notes file in `CLAUDE.md`. Without one, the style keeps no log. Claude Code's auto memory is separate from this log.

## Customizing

Put each rule where its scope is:

| Rule applies to | File |
| --- | --- |
| Research discussion and paper writing in any paper | The output style |
| All your work, coding included | `~/.claude/CLAUDE.md` |
| One paper | The paper project's `CLAUDE.md` |

When the agent repeats a mistake, add a rule for it, restart Claude Code, and test the change.

## Contributing

Contributions are welcome, from both researchers and developers.

- **Report a failure.** Open an issue with what you asked, what the agent did, what you expected, and the agent and its version.
- **Propose a rule change.** Describe the failure it fixes, follow the guidelines below, and test it after restarting the agent before you open a pull request.
- **Add support for another agent.** Put its files in a folder named after the agent, such as `codex/`, and explain its setup in this README. The Claude Code style is the reference text. A rule change applies to every agent's version, so update them together.

Guidelines for prompt changes:

- Keep instructions short, and give the reason behind a rule. Models follow the intent of a rule better when they know why it exists.
- Say what to do, not only what to avoid.
- Name the goal, not only one failure. "Agree when the researcher is right, disagree when they are wrong" works better than "Do not agree too readily", which can push the agent to disagree for show.
- Use few example phrases, and only to show a pattern. Agents tend to overuse the words a prompt mentions.
- Keep the shared style general. Details about one paper, one field, or one person belong in project or user files.
- Keep personal details and unpublished research out of examples, issues, and pull requests.

## License

MIT. See `LICENSE`.
