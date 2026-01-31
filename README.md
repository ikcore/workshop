# WorkShop

WorkShop is a CLI tool designed to facilitate collaborative workshops powered by multiple AI agents. It allows you to define organizational contexts, specialized agent personas, and structured workshop phases to reach specific goals.

## Key Features

- **Multi-Agent Collaboration**: Multiple AI agents interact in rounds within defined phases.
- **Provider Agnostic**: Supports OpenAI, Ollama (local), and Google Vertex AI.
- **Structured Phases**: Define complex workflows with ideation, refinement, and conclusion phases.
- **Persona Driven**: Agents act according to detailed Markdown-based personas.
- **Automatic Documentation**: Generates transcripts, summaries, and outcomes for every session.

## Use Cases

- **Strategic Planning**: Gather diverse AI perspectives on business goals.
- **Product Ideation**: Brainstorm and refine new product features or service offerings.
- **Problem Solving**: Analyze complex organizational challenges with specialized agent roles.
- **Content Creation**: Collaborative drafting and refinement of high-level messaging.

## Getting Started

### Installation

Ensure you have [Rust](https://rustup.rs/) installed. Clone the repository and build the project:

```bash
cargo build --release
```

The binary will be available at `target/release/workshop`.

### Configuration & Environment

Depending on the provider you use, set the following environment variables:

**OpenAI:**
```bash
export OPENAI_API_KEY=your_key_here
# Optional: export OPENAI_API_URL=https://api.openai.com/v1
```

**Ollama (Local):**
```bash
# Optional: export OLLAMA_API_URL=http://localhost:11434
```

**Vertex AI:**
```bash
export VERTEXAI_SA_PATH=/path/to/your/service-account.json
# Optional: export VERTEXAI_API_URL=https://us-central1-aiplatform.googleapis.com/v1
```

## Setting up a Workshop

A workshop environment is organized into a directory (e.g., `/room`).

### 1. Folder Structure
```text
/room
  /agents
    /{agent_id}
      persona.md
  /organisation
    /{org_id}
      description.md
  /workshops
    {workshop_id}.json
```

### 2. Define an Organisation
Create `/room/organisation/MyCompany/description.md`:
```markdown
MyCompany is a tech startup focusing on sustainable energy solutions. We value innovation and speed.
```

### 3. Define Agents
Create `/room/agents/TechLead/persona.md`:
```markdown
You are the Tech Lead. You are pragmatic, focused on feasibility, and prefer scalable architectures.
```

### 4. Create a Workshop Configuration
Create `/room/workshops/innovation-session.json`:
```json
{
  "agents": ["TechLead", "ProductManager"],
  "organisation": "MyCompany",
  "overall_goal": "Identify three high-impact AI features for our solar monitoring app.",
  "description": "A high-level ideation session.",
  "phases": [
    {
      "id": "ideation",
      "name": "Ideation Phase",
      "description": "Generate as many ideas as possible.",
      "goal": "List at least 10 potential features.",
      "rounds": 2
    },
    {
      "id": "refinement",
      "name": "Refinement Phase",
      "description": "Narrow down and detail the best ideas.",
      "goal": "Select and detail the top 3 features.",
      "rounds": 2
    }
  ]
}
```

## Running a Workshop

Execute the CLI by providing a `session_id` and a `workshop_id`. You can also specify the model using the format `provider::model_name`.

```bash
./target/release/workshop --session-id "my-first-session" --workshop-id "innovation-session" --model "openai::gpt-4" --workshop-path "./room"
```

### Command Line Arguments

| Argument | Short | Description | Default |
| :--- | :--- | :--- | :--- |
| `--session-id` | `-s` | Unique identifier for the session output | (Required) |
| `--workshop-id` | `-w` | Filename of the workshop JSON (without .json) | (Required) |
| `--model` | `-m` | Model string: `provider::model` | `ollama::llama3` |
| `--workshop-path` | `-p` | Path to the workshop directory | `./room` |

## Outputs

All session data is saved in `{workshop-path}/sessions/{session_id}/`:

- `/requests/*.json`: Full logs of all LLM requests.
- `/{agent_id}/transcript.txt`: Individual transcripts for each agent.
- `phase_{phase_id}.txt`: Raw conversation transcript per phase.
- `phase_{phase_id}_outcome.md`: AI-generated summary of the phase outcome.
- `transcript.txt`: Full global transcript of the entire workshop.
- `outcome.md`: Final workshop summary.
