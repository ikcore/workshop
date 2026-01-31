# Project Guidelines

WorkShop is a cli that runs workshops with AI agents to reach a goal.

## Directory Structure

### `/apps`
Contains the top-level executable applications.
- **`workshop`**: The main application that runs workshops with agents.

### `/modules`
Contains reusable libraries and frameworks used by the applications.
- **`gaise/`**: Generative AI Service modules.
    - `gaise-core`: Core traits and contracts for AI services.
    - `gaise-api` & `gaise-chatbot`: Implementation of AI-related APIs and chatbot logic.
    - `gaise-provider-*`: Specific implementations for different AI providers (OpenAI, Ollama, VertexAI).
- **`spexsy/`**: Specification Execution System. A framework for building dynamic, strongly-typed execution pipelines for agents.

### Workshop Active Directory
Demo directory is `/room`

The working directory structure if
`/agents` - Holds agent data
`/agents/{name}/persona.md` - The persona of the agent
`/organistation` - Holds organisational data
`/organistation/{id}/description.md` - Holds the organisations data
`/workshops/{workshop_id}.json` - The workshop to run

## Documentation Locations

- **Spexsy Framework**: Detailed documentation on concepts (SystemSpec, Agents, PipelineRunner) can be found in `modules/spexsy/README.md`.
- **AI Services (Gaise)**: Contracts and traits are defined in `modules/gaise/gaise-core/src/contracts`.
- **Workspace Configuration**: The root `Cargo.toml` defines the workspace members and default build targets.

## Development Guidelines

- **Crates**: Use only trusted, necessary crates that are unintrusive and not bloaty
- **Workspace Management**: Use `cargo build` at the root to build the default member (`soho-mcp`). Use `-p <package_name>` to build or test specific modules.
- **Spexsy Pipelines**: When building agent-based workflows, refer to the `spexsy` traits. Agents should return `AgentOutcome` to manage pipeline flow.
- **Adding Modules**: New modules should be placed under `/modules` and added to the `members` list in the root `Cargo.toml`.
- **Testing**:
    - Unit tests are typically located in the `src` directory of each module.
    - Integration tests are found in the `tests/` directory (e.g., `modules/spexsy/tests`).

## How It Should Work

The CLI should take a session_id and a workshop_id as arguments.

Based on the workshop_id it should
- parse the workshop configuration
- load the agents and organisation data into respective models
- it should then build a sys prompt based on the overall goal and phase goal
- phases should be executed in order in a loop
- each agent gets a chance to speak per round
- as each agent speaks it gets added to a user prompt '{Name}: {What the agent said} 
- at the end of the phase an llm request should summerize the conversation
- each llm request should be logged in session/{session_id}'/requests
- transcripts should be saved for each agent in session/{session_id}'/{agent_id}/transcript.txt
- phase transcript should be saved in session/{session_id}'/phase_{phase_id}.txt
- phase outcome summery should be saved in session/{session_id}'/phase_{phase_id}_outcome.md
- global transcript should be saved in session/{session_id}'/transcript.txt
- A final detailed outcome should be saved in session/{session_id}'/outcome.md
