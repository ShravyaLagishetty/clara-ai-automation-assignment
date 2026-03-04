 # Clara AI Automation Assignment
 ## Overview

This project implements an automated pipeline that converts business call transcripts into structured AI agent configurations for Clara AI.

The system processes two types of inputs:

Demo Call Transcript → Generates an initial agent configuration (v1)

Onboarding Call Transcript → Updates the configuration and produces a refined version (v2)

The automation is built using n8n workflows running locally via Docker, with all processing done using JavaScript logic and structured JSON outputs.

The system satisfies the assignment requirements by:

Extracting structured business configuration from transcripts

Generating versioned configuration files

Producing an AI agent specification compatible with Retell

Maintaining version updates between demo and onboarding stages



## System Architecture

The automation consists of two pipelines.

```
Demo Call Transcript
│
▼
Pipeline A (Demo → v1 Agent)
│
├── memo.json
└── agent_config.json

Onboarding Call Transcript
│
▼
Pipeline B (Onboarding → v2 Agent Update)
│
├── memo.json (updated)
├── agent_config.json
└── changelog.json
```

## Tools Used

| Tool | Purpose |
|------|--------|
| n8n (self-hosted) | Workflow automation |
| Docker | Local execution environment |
| JavaScript (n8n Code nodes) | Data parsing and transformation |
| JSON files | Storage of structured configuration |
| GitHub | Version control and project submission |

All tools used are free and open-source, satisfying the assignment constraint of avoiding paid APIs.

## Pipeline A: Demo Call → v1 Agent

Pipeline A processes the demo sales call transcript and extracts initial business configuration.

Input
data/demo_calls/demo1.txt
Processing Steps

Load demo call transcript

Parse transcript to extract business details

Generate structured configuration

Create initial agent specification

Extracted Information

Company name

Business services

Emergency handling logic

Business hours

Integration constraints

Unknown/missing information

Output
outputs/accounts/<account_id>/v1/
    memo.json
    agent_config.json
memo.json

Contains the structured business configuration derived from the demo call.

Example structure:

{
  "account_id": "ben_s_electric_solutions",
  "company_name": "Ben's Electric Solutions",
  "services_supported": [...],
  "emergency_definition": [...],
  "integration_constraints": [...],
  "questions_or_unknowns": [...]
}
agent_config.json

Defines the AI voice agent configuration compatible with Retell.

Example structure:

{
  "model": "retell",
  "voice": "professional_female",
  "prompt": "...",
  "routing": {...}
}
## Pipeline B: Onboarding → v2 Agent Update

Pipeline B processes onboarding data and updates the agent configuration.

Input:<br>
data/onboarding_calls/onboarding_call.txt<br>
Extracted Information :

Using regex-based extraction, the system identifies:

Contact name

Business phone

Business email

Partner company

Partner email

Example onboarding messages:

11:18:43 From BP : info@benselectricsolutionsteam.com<br>
11:21:24 From BP : Shelley Manley<br>
11:21:41 From BP : 403-870-8494<br>

Processing Steps

Load onboarding transcript

Extract structured fields using regex

Load existing v1 memo

Merge onboarding information

Generate updated configuration (v2)

Produce a changelog

Output
outputs/accounts/<account_id>/v2/
    memo.json
    agent_config.json
    changelog.json
Versioning Logic

The system maintains versioned agent configurations.

Version	Source
v1	Demo call assumptions
v2	Confirmed onboarding configuration

The changelog records updates made between versions.

Example:

[
  "Primary contact added: Shelley Manley",
  "Business contact email added",
  "Business phone number added",
  "Referral partner added"
]

## Folder Structure
```
clara-automation
│
├── README.md
│
├── workflows
│ ├── demo_call_to_v1.json
│ └── onboarding_to_v2.json
│
├── data
│ ├── demo_calls
│ │ └── demo1.txt
│ │
│ └── onboarding_calls
│ └── onboarding_call.txt
│
├── outputs
│ └── accounts
│ └── ben_s_electric_solutions
│ ├── v1
│ │ ├── memo.json
│ │ └── agent_config.json
│ │
│ └── v2
│ ├── memo.json
│ ├── agent_config.json
│ └── changelog.json
│
└── docker-compose.yml
``` 

## How to Run
1. Start n8n using Docker
docker compose up -d
2. Open n8n
http://localhost:5678
3. Import workflows
   Import:
   workflows/demo_call_to_v1.json<br>
   workflows/onboarding_to_v2.json<br>
4. Run Pipeline A
Demo Call → Generate v1 configuration
5. Run Pipeline B
Onboarding Call → Update to v2<br>
Key Design Decisions<br>
Regex-based extraction<br>

Avoids hard-coding transcript content and ensures the system works across different onboarding logs.

Versioned configuration

Allows incremental updates from demo call assumptions to confirmed onboarding rules.

File-based storage

Ensures the system remains fully local and does not require external databases.

Retell-compatible agent spec

Even without calling the Retell API, the system outputs a valid configuration format.

## Assignment Requirements Coverage
Requirement	Implementation <br>
Demo call → configuration-Pipeline A <br>
Onboarding → updates-Pipeline B<br>
Structured outputs-memo.json<br>
Agent configuration-agent_config.json<br>
Versioning	v1 → v2<br>
Change tracking-changelog.json<br>
Zero-cost tooling - n8n + Docker

## Conclusion

This automation demonstrates how sales and onboarding conversations can be transformed into structured AI agent configurations using workflow automation.

The system supports versioned updates, structured outputs, and a modular design suitable for scaling across multiple customer accounts.