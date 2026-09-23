# Self-Learning AI Agent

A learning-oriented AI-agent project for experimenting with autonomous task execution, tool use, feedback loops, and incremental improvement.

## Project status

This repository is an experimental project. Interfaces and implementation details may change as the agent architecture evolves.

## Goals

- Explore agent loops that can plan, act, observe, and revise.
- Keep experiments reproducible and easy to inspect.
- Separate model-facing prompts from application logic where practical.
- Make failures and tool results visible during development.
- Prefer small, testable changes over opaque autonomous behavior.

## Getting started

1. Clone the repository.
2. Install the dependencies used by the current project files.
3. Configure required environment variables using a local `.env` file or your shell environment.
4. Run the project's entry point or development command documented by the implementation.

Do not commit API keys, tokens, credentials, or other secrets.

## Contributing

For changes that affect agent behavior, include:

- what behavior changed;
- why the change is needed;
- how the change was tested;
- any new environment variables or external services required.

Keep pull requests focused. If an experiment is intentionally incomplete, document its limitations rather than presenting it as production-ready.

## Safety and reproducibility

Agent experiments can produce side effects when tools have write access. Test new capabilities with the least privilege possible and use isolated development resources where practical.

When reporting results, record the model/provider, relevant configuration, and a representative example or test so another contributor can reproduce the behavior.

## License

See the repository's license file for the applicable terms.
