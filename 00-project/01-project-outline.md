---
name: Mohammadhossein Zare
neptun: DJSAMS
id: 2026_SD_03
github: https://github.com/isOxygen/software-project-2026
trello: TBD
---

# Study-material-grounded Exercise Generation and Practice Application

> **Working direction:** a source-grounded AI study platform powered by a dedicated study harness.  
> Designed for both desktop and mobile use.

## Project summary

The project extends the assigned exercise-generation and practice application into a persistent AI-assisted study environment. Instead of treating uploaded material, questions, plans, and learning history as temporary chat content, the system organizes them as durable study objects inside **Study Workspaces**.

Each workspace can contain sources, conversations, question sets, attempts, plans, notes, mind maps, memory, and configurable AI/tool access. A **Global Study Coordinator** sits above individual workspaces and helps the learner organize multiple subjects, coordinate plans and deadlines, search across permitted study state, move or reference files, and delegate work into workspace sessions.

The goal is to preserve the simplicity of conversational AI while adding the continuity, structure, source grounding, context control, and study-specific behavior needed for serious long-term learning.

## Objectives

- Provide a source-grounded environment for learning, practice, and self-assessment.
- Preserve study state across sessions instead of relying on one long conversation.
- Make questions, attempts, plans, files, notes, memory, and mind maps persistent and manageable.
- Allow AI to work directly with stored study objects instead of repeatedly copying or regenerating them in chat.
- Support multiple AI providers/models and allow the active AI to differ by coordinator, workspace, or session.
- Allow model switching during an existing conversation without losing application state.
- Keep context use efficient and transparent by retrieving only relevant information when deeper detail is needed.
- Support the same underlying study state from desktop and mobile interfaces.
- Retain the original project focus on exercise generation, source traceability, practice, and evaluation.
- Track dependencies between study sources and generated questions so source changes can be detected and affected material can be reviewed or regenerated.

## Target user

The primary user is an **individual learner or student** studying one or more subjects over time.

Teacher, classroom, team, and institution administration are outside the initial implementation scope.

## Success criteria

A successful first implementation should allow a learner to:

1. Create or use a Study Workspace and add study material to it.
2. Start multiple study sessions, resume them later, and fork a conversation without losing the original branch.
3. Generate and manage MCQ and short-answer question sets.
4. Pause, resume, finish, retry, and review question attempts.
5. Receive deterministic MCQ scoring and AI-assisted short-answer feedback.
6. Ask the AI about an exact question, answer, option, source, plan item, or other study object without manually copying it into chat.
7. Maintain multiple study plans and their history across sessions.
8. Generate interactive mind maps and optionally save them to a workspace or library.
9. Preserve useful global/workspace memory while keeping access configurable and inspectable.
10. Use the same study state from desktop and mobile.
11. Change AI/model where permitted without breaking workspace state or conversation history.
12. Use a Global Study Coordinator to organize multiple workspaces and cross-subject planning.

## Scope

### In scope

- Study Workspaces as the primary subject/course container.
- Global Study Coordinator.
- Global Library and workspace-specific files/sources.
- Multiple conversations and forks.
- Persistent workspace memory with configurable access to global memory.
- Question sets, MCQ questions, short-answer questions, hints, attempts, scores, and history.
- Multiple plans with active/completed/archived history.
- Notes and interactive mind maps.
- Source-aware AI interaction.
- References from chat to stored objects and from stored objects back to originating chat messages.
- Configurable web, skill, MCP/tool, memory, editing, and deletion permissions.
- Flexible AI/model selection at global, coordinator, workspace, and session level.
- Mid-conversation AI/model switching.
- Context awareness and selective retrieval across permitted sessions/workspaces.
- Desktop and mobile interfaces.
- Import/export of useful study artifacts where practical.
- Comparison of full-document versus retrieved-context question generation.
- Comparison of random versus constrained question-set assembly.

### Initial implementation boundary

The first implementation should remain testable and defensible while preserving the full direction:

- one individual learner;
- text-based study material first;
- MCQ and short-answer questions first;
- core workspace/session/question/plan flows before broader integrations;
- limited but functional global coordination;
- advanced capabilities can be added incrementally without changing the core object model.

### Out of scope for the initial implementation

- multi-user collaboration, teachers, classrooms, or institution administration;
- every possible question type;
- full handwritten/image/oral assessment workflows;
- mandatory multi-agent orchestration;
- support for every AI provider or external integration;
- unrestricted autonomous actions without permission controls.

## Constraints and design principles

- **Source grounding:** workspace study sources are the normal basis for learning and assessment. If external evidence conflicts with them, the AI should surface the conflict rather than silently overwrite either side.
- **Persistent state:** conversations are not the only source of truth. Important study objects survive session changes and forks.
- **User control:** AI may operate with high autonomy, while each workspace defines its default permissions and individual sessions can temporarily override them. Global capabilities such as models, skills, MCPs/tools, and web access are configured separately.
- **Traceability:** questions, attempts, files, plans, and AI-generated artifacts should retain enough identity/history to explain where they came from and how they changed. Source-dependent questions should be identifiable when their underlying material changes.
- **Context efficiency:** avoid unnecessarily loading or regenerating already stored information. Prefer compact metadata, object references, and selective retrieval.
- **Reversibility:** significant AI edits should be recoverable through history, revision, undo, archive, or equivalent behavior.
- **Model independence:** study state belongs to the harness, not to one AI provider.
- **Responsive access:** the same underlying functionality and data remain usable from desktop and mobile.

## Notes

The official assigned project title is retained. The stronger **AI study harness** direction is an extension of the same core problem rather than a replacement topic: source-grounded exercise generation and practice remain central, while persistent workspaces, sessions, planning, memory, and coordination make the system suitable for long-term learning.

The detailed functional behavior is defined in `02-specification.md`. Technical architecture, frameworks, storage, retrieval implementation, model routing, and optimization mechanisms are intentionally deferred to Phase 2.