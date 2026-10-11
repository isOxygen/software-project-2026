---
name: Mohammadhossein Zare
neptun: DJSAMS
id: 2026_SD_03
project_outline: "[[00-project/01-project-outline]]"
phase: 1
status: draft-for-supervisor-review
---

# Study-material-grounded Exercise Generation and Practice Application

> **Phase 1 — Functional Specification**  
> This document defines what the system must do and how the learner interacts with it.  
> Technical architecture and implementation choices are intentionally deferred to Phase 2.

## Quick read

The application is a **source-grounded AI study platform** organized around persistent **Study Workspaces**.

A workspace contains the learner's sources, sessions, question sets, attempts, plans, notes, mind maps, and memory. AI can operate on these objects directly, subject to permissions.

Above the workspaces, a **Global Study Coordinator** helps manage the learner's overall study environment: it can organize workspaces, coordinate plans and deadlines, search across permitted material, move/reference files, resume or create sessions, and delegate work into workspace sessions.

The same system state is accessible from **desktop and mobile**.


### Why this structure

- **Study Workspace:** keeps one subject/course focused and persistent. Its sources, sessions, questions, plans, attempts, memory, notes, and mind maps stay together, so the learner can continue over time without rebuilding context in every chat.
- **Global Study Coordinator:** solves the cross-subject problem. It can understand the learner's overall study situation, organize workspaces, manage files, coordinate plans and deadlines, resume or create sessions, and delegate work without loading every workspace into one huge conversation.
- **Why both:** the workspace provides **depth and isolation** for one subject, while the coordinator provides **overview and coordination** across the learner's studies. Together they support both focused studying and overall study management.

---

# Phase 1 — Functional Specification

## 1. Actors and permissions

### 1.1 Primary actor

**Learner**

The learner owns the application state and can:

- create and manage Study Workspaces;
- create, resume, fork, rename, archive, and review sessions;
- manage sources and files;
- create, answer, retry, and review question sets;
- create and manage plans;
- use the Global Study Coordinator;
- configure AI/model behavior;
- configure memory, web, skill, MCP/tool, editing, and deletion permissions;
- inspect and manage AI-created content.

No teacher/admin role is required for the initial version.

### 1.2 AI roles

The application exposes two functional AI scopes.

#### Global Study Coordinator

The Coordinator works across the learner's permitted study environment. It can:

- create/manage workspaces;
- create, resume, or fork workspace sessions;
- search across permitted workspace/session state;
- manage cross-workspace planning;
- manage files and Global Library items;
- delegate tasks into workspace sessions;
- perform bulk organization and maintenance actions.

#### Workspace AI

The Workspace AI focuses on one Study Workspace. It can:

- teach and answer questions;
- manage question sets;
- edit plans;
- work with sources;
- create mind maps and notes;
- manage workspace memory;
- use permitted skills, tools, MCPs, and web search.

It may access selected global capabilities or other workspaces only when permission allows it.

### 1.3 Permission hierarchy

Permissions are separate from the session operating mode.

The main default is defined at **workspace level** and can be overridden for the current session:

```text
Workspace permission profile
          ↓
Session override from the chat bar
```

The workspace can define fine-grained permissions such as:

- read files/objects;
- create;
- edit;
- archive/delete;
- manage memory;
- use global memory;
- search the web;
- use MCPs/tools;
- use skills;
- modify sources;
- modify questions;
- modify plans;
- modify mind maps/notes;
- delegate actions;
- access other workspaces.

A workspace may expose simple profiles such as:

- **Read-only**
- **Ask / Controlled**
- **Full Access**
- **Custom**

The learner can change the active session profile directly from the chat bar. A session override applies only to that session unless the learner explicitly changes the workspace default.

Significant AI changes should remain recoverable through revision history, undo, archive, or equivalent behavior.

---

## 2. Core application model

### 2.1 Application-level objects

```text
Application
├── Global Study Coordinator
├── Global Library
├── Global Memory
├── Study Calendar / cross-workspace planning
├── Global AI / skill / MCP / web settings
└── Study Workspaces
```

### 2.2 Study Workspace

A **Study Workspace** is the main persistent container for one subject, course, or bounded learning goal.

A workspace may contain:

- workspace identity and instructions;
- source files;
- sessions and forks;
- named session question sets and optional Question Bank collections;
- attempts/results linked to their questions and sets;
- plans and plan history;
- notes;
- mind maps;
- workspace memory;
- settings and permissions;
- AI/model preferences.

A workspace is not tied to one conversation or one AI provider.

### 2.3 Persistent object principle

Questions, plans, files, mind maps, notes, memory, and attempts are first-class objects.

They are not required to exist only as copied text inside conversation history.

Each important object should have a stable internal identity so the system can reference it reliably even if:

- its display name changes;
- another object has the same display name;
- it is edited;
- it appears in multiple scopes;
- a conversation is forked.

### 2.4 Functional requirement index

This table is the compact acceptance-oriented index for the detailed behavior defined in later sections. `Must` items form the core implementation target; `Should` items are important parts of the full design that may be staged if Semester-2 scope requires it.

| ID | Priority | Requirement | Acceptance criterion |
|---|---|---|---|
| FR-01 | Must | Persistent Study Workspaces | A learner can create/reopen a workspace and its sources, sessions, questions, attempts, plans, notes, mind maps, and settings remain available. |
| FR-02 | Must | Multiple sessions and forks | A learner can resume sessions and fork from a chosen point without changing the original branch. |
| FR-03 | Must | Source/file management | AI and learner can manage permitted files/sources without requiring duplicate uploads, and same-named files remain distinguishable. |
| FR-04 | Must | Session question sets | AI and learner can create, solve, revise, organise and reference named question sets in sessions independently of Question Bank membership, with stable IDs and revision history. |
| FR-05 | Must | MCQ practice lifecycle | Learner can answer in chat or a focused view, pause/resume/retry, and see deterministic scores and prior attempts attached to the same questions/sets. |
| FR-06 | Must | Short-answer practice lifecycle | Text responses and supported attachments may be saved/submitted; criterion-based AI feedback, uncertainty and previous attempts stay linked to each question, not mixed with optional AI solutions. |
| FR-07 | Must | Source traceability/change awareness | Questions can retain source references; when relevant source content changes, dependent questions can be identified as needing re-check/update. |
| FR-08 | Must | Chat/object linking | A learner can solve, edit and reference a question from chat or Questions; both views access the same object and may return to its originating session. |
| FR-09 | Must | Plans and history | Multiple plans can be created, edited, completed, archived, reopened, and accessed from later sessions. |
| FR-10 | Must | Permission hierarchy | Workspace defaults and session overrides govern AI access/actions, including full-access, controlled, read-only, and custom behavior. |
| FR-11 | Must | AI/model portability | Different scopes may use different models/providers and the active model can change mid-session without losing application-owned state. |
| FR-12 | Must | Context-aware retrieval | AI can use compact awareness/indexes and retrieve deeper permitted content only when needed instead of loading all history every turn. |
| FR-13 | Must | Desktop/mobile continuity | Core flows operate on the same state across responsive desktop and mobile interfaces. |
| FR-14 | Must | Assigned-brief evaluation | The system supports evaluation of full-document vs retrieved-context generation and random vs constrained set assembly. |
| FR-15 | Should | Global Study Coordinator | Coordinator can manage workspaces/sessions/files and coordinate study activity across permitted workspaces. |
| FR-16 | Should | Memory scopes | Global/workspace memory is inspectable, editable, controllable, and selectively retrievable. |
| FR-17 | Should | Interactive mind maps | AI can create interactive mind maps that may remain local, be saved, referenced, and exported. |
| FR-18 | Should | Skills/tools/web configuration | Capabilities can be configured globally and enabled/restricted per workspace/session. |
| FR-19 | Should | Cross-workspace planning/calendar | Coordinator can use permitted high-level workspace state to coordinate deadlines and study plans across subjects. |
| FR-20 | Must | Context-efficient stored-object rendering | The UI renders existing questions by stable reference; retrieval, targeted edits and concise tool responses avoid repeating unchanged content. |
| FR-21 | Must | Optional Question Bank curation | Learner or explicitly instructed AI can add a whole named set or selected questions to the bank while retaining set identity, original session links and other non-selected sets outside the bank. |
| FR-22 | Must | Precise AI question edits | Permitted AI actions can change a field, portion, question or selected set without replacing unaffected content; revision conflicts and invalid edits are rejected with actionable errors. |
| FR-23 | Should | Learning from attempt history | AI can review relevant per-question attempts and feedback to identify possible weak topics and suggest further teaching or practice, without treating scores as proof of mastery. |

---

## 3. Global Study Coordinator

### 3.1 Purpose

The Global Study Coordinator is not primarily a subject tutor.

Its purpose is to help the learner **organize, coordinate, understand, and operate the entire study environment**.

### 3.2 Coordinator capabilities

Subject to permissions, the Coordinator can use any application capability exposed through the harness. The following are representative capabilities, not an artificial limit:

- create, rename, configure, archive, and manage workspaces;
- create, resume, and fork its own sessions;
- create, resume, or fork a session inside a workspace;
- inspect lightweight state across permitted workspaces;
- search across permitted sessions, files, questions, plans, notes, mind maps, and memories;
- open deeper content only when needed;
- add, move, reference, copy, rename, edit, archive, or organize files;
- place uploaded material into the correct workspace;
- add material from the Global Library into a workspace;
- move or expose workspace material to the Global Library where permitted;
- create and manage cross-workspace study plans;
- coordinate deadlines, exams, assignments, and curriculum progress;
- identify conflicts between multiple subject plans;
- delegate work to a workspace AI/session;
- perform bulk operations across the study environment;
- change settings where permission allows it.

### 3.3 Global context awareness

The Coordinator should maintain a lightweight awareness of the shape and current state of the learner's study environment without loading complete histories.

Example:

```text
Mathematics
- 7 sessions
- latest session: "Gauss-Jordan review"
- active plan: "Midterm catch-up"
- recent activity
- unresolved items

Physics
- 4 sessions
- latest session: "Kinematics practice"
- next deadline
```

This awareness may include compact metadata such as:

- workspace names;
- session titles;
- session counts;
- approximate turn counts;
- latest session/activity;
- active plan names/status;
- deadlines;
- object counts;
- recent summaries or indexes.

When deeper information is needed, the Coordinator should search/retrieve the relevant content rather than automatically loading every workspace or session.

### 3.4 Delegation

The Coordinator may instruct a workspace/session to perform work.

Example:

```text
Coordinator:
"Prepare a new Chapter 3 practice set in Mathematics."

→ Mathematics workspace/session performs the requested action.
→ The resulting question set remains a workspace object.
→ Coordinator receives enough result state to know the task completed.
```

The functional requirement is delegation and coordination. The exact technical mechanism belongs to Phase 2.

---

## 4. Sessions and conversation forks

### 4.1 Multiple sessions

Both the Coordinator and each Study Workspace support multiple persistent sessions.

A learner can:

- create a new session;
- resume an existing session;
- rename a session;
- search sessions;
- archive sessions;
- view recent sessions and session metadata.

### 4.2 Forking

A session can be forked from a selected point in the conversation.

```text
A → B → C → D → E
        │
        └── fork
             ↓
        C → F → G → H
```

The original branch stays intact.

The fork records:

- its parent session;
- its fork point;
- enough shared history to continue coherently.

### 4.3 Shared workspace state

Forking a conversation does **not** automatically duplicate every workspace object.

Questions, plans, files, memory, and other workspace objects remain the same objects unless the user or AI explicitly creates a copy/new object.

### 4.4 Jumping between objects and chat

Stored objects may retain a link to the conversation/message where they were created or discussed.

Examples:

- **View in chat** from a question;
- **View current question set** from a chat message;
- jump to the message where a plan was created;
- jump to a mind map's originating response.

This supports navigation through long study histories.

---

## 5. AI/model flexibility

### 5.1 Model selection scopes

The system supports configurable AI/model selection at different scopes:

- global application default;
- Global Study Coordinator;
- Study Workspace;
- individual session.

A more specific setting may override a broader default.

### 5.2 Different AI per scope

The Coordinator, one workspace, and another workspace may use different AI providers/models.

Different sessions in the same workspace may also use different models.

### 5.3 Mid-conversation switching

The learner may change the active AI/model during an existing conversation.

Switching models must not destroy:

- session history;
- workspace objects;
- references;
- memory;
- plans;
- attempts;
- file identities.

The harness owns persistent state; the selected model consumes the relevant state.

### 5.4 Session model awareness

A session should retain enough metadata to know:

- which model/provider was last active;
- model changes during the session where useful;
- current model;
- relevant capability limitations.

When resuming or creating a session, the system may:

- reuse the previous model;
- use a configured workspace/session default;
- allow the user to choose;
- automatically choose an available model according to configured rules/capabilities.

Exact routing logic is a Phase-2 concern.

### 5.5 Capability mismatch

If a selected AI lacks a required capability, such as image understanding or a particular tool interface, the system should communicate the limitation and allow an appropriate model/tool choice rather than silently failing.


### 5.6 Model capability awareness

The harness should know relevant capabilities of available models, such as:

- supported context capacity;
- input/output modalities;
- tool/function support;
- structured-output capability;
- provider-specific restrictions.

If the user switches from a larger-context model to a smaller-context model, the system must adapt the active model context appropriately without losing canonical session or workspace state. The exact compaction/retrieval strategy is a Phase-2 decision.

---

## 6. Sources, files, and the Global Library

### 6.1 Source scopes

Material may exist as:

- conversation-local attachment;
- workspace source;
- Global Library item.

A conversation-local item may later be added to the workspace.

A Global Library item may be made available to selected workspaces.

### 6.2 Global Library

The Global Library stores reusable material outside any single workspace.

It may contain:

- source files;
- notes;
- exported/importable study artifacts;
- mind maps;
- reusable question sets where appropriate.

Access to global content can be controlled per workspace.

Cross-workspace/global access is disabled or restricted by default and explicitly enabled where useful.

### 6.3 File identity

File identity must not depend only on filename.

Two files may have the same display name.

A file can have compact metadata such as:

```text
id
name
scope
origin
created/updated
version
relation
```

This lets the AI distinguish:

- local files;
- global files;
- referenced files;
- copied files;
- inherited/origin-linked files;
- different files with the same name.

### 6.4 File operations

Where permitted, AI can:

- read files;
- create files;
- edit files;
- rename files;
- move files;
- reference files into another scope;
- make independent copies;
- archive/delete files;
- organize folders/collections.

The system should avoid forcing the user to re-upload the same file merely because it is needed in another workspace.

### 6.5 Workspace portability and sharing

The learner should be able to export a workspace in a portable form and later import it again.

Where practical, an export should preserve the meaningful workspace structure, such as:

- sources/files;
- questions and question sets;
- attempts/results;
- plans and history;
- notes;
- mind maps;
- relevant metadata and references.

This also enables sharing a prepared workspace with another learner without manually recreating its structure.

### 6.6 Protected objects

The learner may mark selected objects as protected/locked.

Protected objects cannot be edited, replaced, archived, or deleted automatically by AI until the protection is explicitly removed or an allowed action is confirmed according to the current permission policy.

### 6.7 External knowledge and source differences

Workspace sources are the normal study basis. Web/tool knowledge may supplement them when enabled. If an external result materially disagrees with study material, the AI should handle the difference transparently with the learner rather than silently replacing either source.

---

## 7. Questions and question sets

### 7.1 Session question sets and the optional Question Bank

AI or the learner can create one or more **named Question Sets** in a study session. Each set has a stable ID, title, workspace, originating session and an ordered membership of identifiable questions. Sets from different sessions stay separate unless the learner explicitly combines them.

A generated question or set is usable **immediately**, inside its chat or a dedicated question/practice view. It can be opened, answered, discussed, referenced, edited, retried and revisited without first entering the Question Bank. It is saved as an application-owned study object, not just copied message text; it remains accessible from its session later.

The **Question Bank** is an optional, organised selection for easier future access, **not** the automatic destination of every generated question. The learner can:

- add a complete named set to the bank;
- select only certain questions from a set, preserving their relation to the original set and session;
- keep separate sets separate, including when many sets originate in one session;
- browse sets from sessions that have not been collected in the bank;
- remove a set or question from the bank without deleting the original question, session or its attempts;
- use the same actions by directly interacting with the UI or by explicitly instructing AI, within active permissions.

**Adding to the bank is the learner's selection/acceptance for bank inclusion.** It can be performed in one step for a whole set or a selection, without a mandatory per-question approval screen, review queue or extra approval clicks. A general AI permission profile alone does **not** automatically collect every generated set in the bank. If permission policy requires confirmation for an AI-initiated action, that policy still applies.

Bank collections refer to the canonical questions rather than silently creating unrelated copies. A curated subset keeps its own bank entry/selection and links to the source set and session. Titles and ordering remain clear and editable. AI and learner may perform bulk question/set edits, reordering, hint changes, and archive/removal actions subject to permissions.

### 7.2 Initial question types and minimal answer data

The first required types are **Multiple-choice (MCQ)** and **Short-answer / QA**; other question forms can be added later without forcing them into the first implementation.

- **MCQ:** save the options and correct answer(s), so scoring can be deterministic. A full explanation or worked solution is optional.
- **Short-answer:** save a compact reference answer or expected result and assessment criteria, with suitable supporting material for evaluation; do not routinely generate long worked solutions.
- **On demand:** the learner may solve the question, ask AI to solve it, request hints, or ask for a detailed explanation. Optional AI solutions are saved separately from the reference answer and learner attempts.
- **Visibility:** an answer key may be hidden during practice without deleting it from storage. A saved question need not have a learner attempt yet.

Study-material-based questions retain a traceable reference to the supporting source/version. Ordinary tutor conversation need not cite a source on every turn.

### 7.3 Question identity, precise edits and revision

Each question should retain:

- stable question ID and question revision;
- containing/originating set and session IDs, with bank membership tracked separately;
- source references and source versions when applicable;
- origin, timestamp and relevant permissions/lock state;
- answer-key/criteria revision where scoring depends on it.

The learner and permitted AI can edit a **part of a field, a full field, a whole question or a selected group of questions**. A compact editing interface, analogous to a targeted patch, should not require resending or rewriting unchanged questions. It is an operation on canonical structured data, not fragile blind text replacement.

The application must resolve exact IDs and expected revisions, validate field/type constraints and permissions, and apply changes atomically. On an invalid target, conflicting revision or bad input, it should reject the change and return a short actionable error so AI can correct the request. Significant edits must remain recoverable. An edit that changes possible answers must trigger consistency checks on stored correct answers and evaluation criteria.

Earlier attempts and solutions remain linked to the **question revision they used**, even if the current question changes. Rendering an old chat message may show that it refers to an earlier revision and offer the current question.

### 7.4 Hints

Questions may contain:

- no hint;
- one hint;
- multiple hints.

Whether hints are visible depends on the question-set rules or current session workflow.

### 7.5 MCQ attempts

For MCQ sets, the learner can:

- answer questions;
- pause;
- leave and resume later;
- submit;
- see score/percentage;
- see correct/incorrect results according to the question-set or assessment rules;
- retry the set;
- review previous attempts.

MCQ correctness should be evaluated deterministically from the stored answer key and question revision. The learner may answer in chat or the focused practice view against the same stored question; no AI call is required for each selection.

### 7.6 MCQ post-attempt AI interaction

After a set is submitted, the system may provide the AI with a compact, permission-checked representation of the linked attempts (rather than full chat history):

- incorrect questions;
- selected answers;
- correct answers;
- hints used;
- attempt summary;
- relevant source/question references.

The AI can then:

- explain weaknesses;
- teach the misunderstood material;
- recommend review;
- create another targeted question set;
- update a plan if permitted.

### 7.7 Short-answer submissions

For short-answer sets, the learner can:

- answer one or more questions inside chat or a focused question/practice view;
- save an unfinished response, submit only completed answers, or continue later;
- submit **text and/or attached files** using a common submission model;
- request a separate AI explanation or worked solution, with no requirement to generate it alongside every question.

The first implementation targets text responses and supported text-extractable sources/attachments. Storing an attachment does not imply that every file format, image, handwriting or equation is automatically understood or assessable; unsupported evaluation paths must be made clear.

AI evaluates submitted answers against saved criteria and available relevant sources, cites evidence when needed and can report uncertainty. Learner answers and AI-produced worked solutions are distinct records. An AI solution never overwrites a learner submission.

### 7.8 Attempts, AI solutions and learning progress

Each learner attempt is associated with an exact question ID **and revision**, and (where applicable) the set and practice run through which it was answered. It may keep response text/attachment references, submission/completion state, time, hint use, score and feedback.

- **Repeated attempts** add history; they do not replace an earlier attempt.
- **AI solutions/explanations** are optional, independently saved records associated with the same question/revision and relevant session. Multiple solutions or different methods can coexist with learner attempts and the compact reference answer.
- **Question and set views** show the relevant attempts, results and AI solutions in context; a separate standalone progress module is not required.
- **Cross-session discussion** can reference exact questions, attempts or AI solutions without manually copying them; the user can compare methods or ask follow-up questions.
- **AI progress assistance** may inspect authorised, relevant attempt summaries and weak-topic patterns to suggest lessons, plans or practice. It must distinguish observed attempts from unverified claims about learning mastery.

The same saved attempt history is available when opening a question from its original session or from the Question Bank. Removing an item from the bank does not erase the attempts or session history.

### 7.9 Source dependencies and changes

Questions generated from study material should retain enough provenance to identify the relevant source material/version.

If a relevant source changes, the application can flag affected questions, bank entries and related answer keys for re-check or regeneration. It must not silently assume an earlier reference answer is still correct.

AI may suggest or make permitted, versioned edits to affected questions; historical question revisions and attempts remain intact. Source changes do not automatically delete questions or silently collect unselected questions into the bank. Where a selected bank question becomes outdated, its status remains visible until the issue is addressed.

If a requested question set cannot be assembled from suitable existing questions, the system should either generate appropriate new questions or clearly explain/adapt the request rather than silently returning an incomplete result.

## 8. Chat ↔ object interaction

### 8.1 Questions are interactive in chat

When AI creates one or several named question sets, chat should show readable compact cards or an expandable grid rather than flooding the conversation with full question text.

Example:

```text
Week 3 — Linear Algebra · 12 questions
[Open] [Practise] [Add to Question Bank]
```

Opening the card can expose individual questions, answer controls, explanations, source references, relevant earlier attempts and editing actions. The learner can solve and discuss a question **without leaving the conversation**; a focused full-screen practice view remains available when useful.

The same canonical stored object appears in chat, the originating session, Question Set views and (if selected) the Question Bank. The user should never have to choose where an edit "really" belongs. The current object and relevant history stay in sync across views.

A named set does **not** have to be transferred to the Question Bank to be usable or to persist.

### 8.2 Object references in chat

The user can reference exact questions, named sets, source passages, previous learner attempts, or optional AI solutions across permitted sessions without manually copying their text.

Examples:

```text
@question
@question-set
@plan
@mindmap
@memory
@source
@session
@file
@mcp-or-tool
```

The UI may provide autocomplete/search. Mentioning an MCP/tool identifies or targets an available capability; use still follows the current permission policy.

### 8.3 Actions in chat

Slash actions may expose commands, skills, and quick resource actions such as:

```text
/generate-questions
/create-plan
/review
/practice
/use-skill
/file
/source
/compact
/search
```

The final command set is a UX decision. In general, `@` addresses an entity/capability and `/` invokes an action, skill, or quick resource workflow. AI should have permission-checked tools for the same question operations available in the UI: open/reference, answer or generate a solution, edit precisely, organise a set, select items for the Question Bank, and remove bank membership. A learner's explicit instruction to add questions to the bank counts as selection; ordinary question generation does not.

### 8.4 Context- and token-efficient object interaction

The application can render canonical questions and other saved objects from stable references; AI does not need to regenerate their text simply to display them. When AI needs to inspect or change an object, it receives a compact index or selected relevant fields/versions first and fetches more detail only when needed.

Changes should use a small targeted edit/patch interface with stable IDs and version checks instead of full-object rewrites. Tool responses should be concise: affected IDs, changed revisions, success/failure and actionable validation errors. Both **input context** and **output/tool-feedback tokens** should be limited without concealing failures or needed evidence.

### 8.5 Tutoring and study plans

The learner can ask AI to **teach, demonstrate, explain and answer follow-up questions** as many times as needed before choosing to practise. AI can support the learner's own study plan or suggest adjustments based on permitted materials and observed attempts. Learning is not required to begin or end with generating questions.

## 9. Plans and planning history

### 9.1 Persistent plans

A workspace can contain multiple plans.

Examples:

- exam preparation plan;
- chapter-learning plan;
- catch-up plan;
- weekly study plan.

### 9.2 Plan lifecycle

Plans may be:

- active;
- completed;
- archived;
- resumed/reopened;
- duplicated.

The user can review plan history.

### 9.3 AI plan management

Subject to permissions, AI can:

- create plans;
- edit titles/descriptions;
- create/edit/reorder plan items;
- mark progress;
- close/archive a plan;
- resume an older plan;
- adapt a plan when circumstances change.

Plans remain available across sessions.

### 9.4 Cross-workspace planning

The Global Coordinator may combine high-level state from multiple workspaces to create or update a cross-subject schedule.

It can consider:

- exams;
- deadlines;
- curriculum progress;
- active plans;
- available study time;
- competing workload.

The system may include an internal study calendar.

External calendar integration is optional/future unless later selected for implementation.

---

## 10. Mind maps

### 10.1 Purpose

A Mind Map is an AI-generated visual learning artifact used to:

- summarize;
- explain relationships;
- organize a topic;
- teach complex material;
- provide a navigable overview.

It is not required to be the system's internal knowledge graph.

### 10.2 Scope

A mind map may be:

- temporary/conversation-local;
- saved to the workspace;
- placed in the Global Library where permitted;
- deliberately added as a study source.

### 10.3 Interaction

A saved mind map should be:

- interactive;
- viewable later;
- editable/regenerable where appropriate;
- linkable from chat;
- exportable as an image or PDF where practical.

A structured/text representation may be retained internally so the same map can be rendered consistently across desktop/mobile and exported formats.

---

## 11. Memory

### 11.1 Memory scopes

The application may maintain:

- **Global Memory**
- **Workspace Memory**

Workspace settings determine whether Global Memory is available inside that workspace.

A session may further restrict memory access.

### 11.2 Automatic and explicit memory

AI may remember useful durable information automatically according to configured behavior.

The user can also explicitly request:

- remember this;
- update this memory;
- forget/delete this;
- show relevant memory.

### 11.3 Memory control

The learner should be able to:

- inspect memory;
- edit memory;
- delete memory;
- control memory availability by workspace/session.

### 11.4 Context-efficient memory access

The AI should not require the entire memory store in every turn.

It should be possible to maintain compact indexes/descriptions and retrieve detailed memory only when relevant.

### 11.5 Relationship to structured state

Questions, plans, attempts, files, etc. remain structured objects even if related information is also useful as memory.

The specification does not impose a rigid rule that memory may never overlap structured state; duplication may be useful in some situations.

The system should avoid unnecessary duplication while preserving useful context.

---

## 12. Session operating mode

The session operating mode controls **how the AI is allowed to behave**, while permissions control **which capabilities it may use**.

### 12.1 Default / Act mode

In the normal operating mode, the AI can converse and perform actions that are permitted by the active session permission profile.

Examples include:

- creating or editing study objects;
- managing files;
- using allowed web/MCP/skills;
- updating plans or memory;
- delegating work;
- reorganizing workspace content.

### 12.2 Plan mode

Plan mode is a strict non-mutating mode.

The AI may:

- inspect;
- read;
- search;
- analyze;
- investigate;
- propose changes;
- produce a plan.

It must **not modify persistent application state** while Plan mode is active.

This restriction remains in force even if the permission profile is **Full Access**. The operating mode must be changed before execution or mutation is allowed.

### 12.3 Mode and permission independence

Examples:

```text
Plan + Full Access
→ may inspect everything allowed, but must not modify state.

Default + Full Access
→ may perform permitted actions without additional approval.

Default + Read-only
→ may reason and converse normally, but cannot modify state.
```

Study activities such as **learning, practice, assessment, and review are not system operating modes**. They are ordinary user intents or workflows that the AI can follow within the active operating mode and permissions.

---

## 13. Context awareness and context management

### 13.1 Context visibility

Where the selected provider exposes usable information, the UI may show:

- current context usage;
- current model;
- active sources/objects;
- whether compaction occurred;
- whether web/tools are available.

If an exact token count is unavailable, the UI should not invent false precision.

### 13.2 Selective retrieval

The system should avoid loading all available workspace/global information every turn.

Instead it should:

1. maintain compact indexes/metadata;
2. determine what appears relevant;
3. retrieve deeper information when necessary;
4. preserve access to the original/full information.

### 13.3 Reversible context reduction

Context-management operations should not permanently destroy canonical study data.

Information removed from the model's immediate active context should remain recoverable from persistent storage where applicable.

### 13.4 Optional lightweight decision/routing layer

The system may later use a fast, low-cost semantic decision/classification layer to support the main AI.

Possible uses include:

- deciding what context is relevant;
- deciding what deeper material should be retrieved;
- helping route skills/tools;
- helping select a suitable AI/model from the models the user has allowed;
- identifying candidate memory;
- supporting context reduction/compaction.

This layer is **optional** and has no authority to bypass policy.

It must not:

- switch a session from Plan mode to Default/Act mode;
- grant permissions;
- bypass workspace/session restrictions;
- authorize destructive actions.

Critical permission and safety rules remain deterministic application logic.

The exact technology, thresholds, evaluation, fallback behavior, and implementation belong to Phase 2.

---

## 14. Skills, MCPs/tools, and web access

### 14.1 Global availability

Skills, MCPs/tools, AI providers, and web capabilities can be registered/configured globally.

### 14.2 Workspace availability

Each workspace can define which global capabilities are available inside it.

### 14.3 Session overrides

A session can further restrict or expand allowed capabilities according to permission rules.

### 14.4 AI use

AI may invoke available capabilities when allowed by:

- user request;
- configured permissions;
- workspace/session policy;
- capability requirements.

The functional specification does not require users to understand the underlying protocol of every MCP/tool.

---

## 15. Notes and editable artifacts
The application may support persistent notes/files that AI can create and edit where permitted.

Examples:

- Markdown notes;
- text notes;
- study summaries;
- structured reference files.

Notes can:

- belong to a workspace;
- originate in a session;
- be placed in the Global Library;
- be referenced in chat;
- retain edit/version history where important.

---

## 16. Desktop and mobile behavior

### 16.1 Shared state

Desktop and mobile operate on the same underlying objects and state.

### 16.2 Session controls

The chat bar or session header should provide direct access to the current:

- operating mode;
- permission profile;
- active AI/model.

These controls affect the current session without requiring the learner to navigate through global settings.

### 16.3 Responsive interaction

The interface may use different layouts by device size while preserving the same capabilities.

Examples:

- desktop may show chat and an object panel side-by-side;
- mobile may open Questions, Plans, Sources, or Mind Maps as full-screen views/sheets.

### 16.4 Continuity

A user can begin work on one device and continue on another without losing:

- session position;
- question progress;
- plan state;
- workspace state.

### 16.5 Initial wireframe direction

These are layout-level sketches only; they define navigation/flow, not final visual design.

**Desktop**

```text
┌───────────────┬──────────────────────────────┬──────────────────┐
│ Coordinator   │ Session / active study view  │ Context / object │
│ Workspaces    │                              │ panel            │
│ Library       │ Chat, questions, mind map,   │ Sources          │
│ Calendar      │ plan, or selected artifact   │ Questions        │
│ Settings      │                              │ Plan / Memory    │
└───────────────┴──────────────────────────────┴──────────────────┘
```

**Mobile**

```text
┌──────────────────────────────┐
│ Workspace / Session          │
├──────────────────────────────┤
│ Active chat or study view    │
│                              │
│ Questions / plans / sources  │
│ open as dedicated views      │
├──────────────────────────────┤
│ Navigation / chat composer   │
└──────────────────────────────┘
```

The same objects and actions remain available; only information density and navigation presentation change.

---

## 17. Functional research/evaluation requirements from the assigned brief

The expanded study-harness direction must retain the original project's evaluation goals.

### 17.1 Generation context comparison

Compare question generation using:

1. fuller source/document context;
2. retrieved/selected relevant excerpts.

Evaluation may consider:

- question quality;
- source traceability;
- relevance;
- latency;
- correction effort;
- reproducibility.

### 17.2 Question-set assembly comparison

Compare:

1. random question selection;
2. constrained selection using factors such as topic coverage, repetition, requested length, and previous attempts.

Evaluation may consider:

- coverage;
- usefulness;
- repetition;
- learner experience;
- consistency.

---

## 18. Non-functional requirements

### NFR-01 — Usability

The main learning flows should be understandable without requiring the learner to manage technical context manually.

**Verification:** usability walkthrough of workspace creation, source use, question practice, plan management, and session resume/fork.

### NFR-02 — Responsiveness

Common navigation and deterministic actions should feel immediate where technically practical.

AI-dependent operations should show clear progress/state.

**Verification:** measure representative UI and AI workflow latency during implementation.

### NFR-03 — Data continuity

Closing a session/device must not lose persistent workspace objects or saved attempts/plans.

**Verification:** resume tests across sessions/devices.

### NFR-04 — Traceability

Important AI-generated study objects should retain identity, source/origin, and revision information where relevant.

**Verification:** inspect question/file/plan history after edits.

### NFR-05 — Recoverability

Significant AI edits or destructive operations should be recoverable or protected by permissions.

**Verification:** edit/archive/undo/history tests.

### NFR-06 — Context efficiency

The application should avoid unnecessary input-context loading, full-object rewrites, excessive model output and verbose tool-feedback responses. Targeted object retrieval and version-checked edits must not compromise correctness.

**Verification:** compare input and output tokens, latency, validity of small edits and recovery from invalid/conflicting tool calls versus workflows that repeat full objects or chat history.

### NFR-07 — Model portability

Changing AI/model should not invalidate application-owned study state.

**Verification:** switch models within a session and confirm objects/history remain intact.

### NFR-08 — Responsive access

Core workflows must remain usable on desktop and mobile.

**Verification:** functional walkthrough on representative desktop and mobile layouts.

### NFR-09 — Permission enforcement

Configured workspace/session restrictions and global capability availability must be respected by AI actions.

**Verification:** attempt permitted and denied operations at each scope.

### NFR-10 — Accessibility and information clarity

The interface should use clear hierarchy, compact navigation, readable states, and keyboard/touch-friendly interaction so large study histories do not become visually overwhelming.

**Verification:** walkthrough of core flows on desktop/mobile using keyboard and touch-oriented navigation where applicable.

### NFR-11 — Scope isolation

Content from a restricted workspace, memory scope, or library scope must not be exposed to another scope unless the configured permissions allow it.

**Verification:** cross-workspace access tests with allowed and denied configurations.

---

## 19. Business rules and constraints

### BR-01
The learner owns the study environment and controls permissions.

### BR-02
Workspace/session state must not depend on one specific AI provider.

### BR-03
MCQ scoring should use deterministic stored answers rather than unnecessary AI calls.

### BR-04
AI short-answer evaluation must be allowed to express uncertainty.

### BR-05
Conversation forks preserve the original conversation branch.

### BR-06
Forking does not implicitly duplicate every workspace object.

### BR-07
Same-named files/questions/plans are distinguishable through stable internal identity.

### BR-08
External knowledge conflicts with study sources should be surfaced rather than silently hidden.

### BR-09
Global/cross-workspace access follows explicit permissions.

### BR-10
Canonical stored content must not be permanently lost merely because model context is compacted.

### BR-11
Stored objects may be referenced/rendered without forcing the AI to regenerate their full text.

### BR-12
AI/model switching must preserve application state.


### BR-13
Operating mode and permission profile are independent controls.

### BR-14
Plan mode must not mutate persistent state, even when the active permission profile is Full Access.

### BR-15
An optional semantic decision/routing layer cannot grant permissions or bypass the active operating mode.

### BR-16
Session-created questions and titled sets are usable and persistent independently of Question Bank membership. No automatic bank transfer occurs through generation or practice.

### BR-17
The Question Bank is a learner-selected collection. The learner's add action or explicit instruction to AI is the acceptance for inclusion, including multi-question selection; no separate mandatory per-question approval screen is required. Active tool permissions remain enforced.

### BR-18
Chat and Question Bank must refer to shared canonical question identity; bank removal does not automatically delete the source set, question or attempt history.

### BR-19
Learner attempts and optional AI solutions must not overwrite one another; records retain their question revision and remain navigable from the relevant question/set.

### BR-20
AI edits must validate exact targets, expected revisions, active permissions and scoring consistency before committing partial or bulk changes.

---

## 20. Main user journeys

### Journey A — Start a new subject

1. Learner or Coordinator creates a Study Workspace.
2. Learner adds/selects course material.
3. AI helps the learner understand a topic through explanations and follow-up discussion.
4. Learner follows an existing study plan or asks AI to suggest one.
5. Learner may generate a named question set and practise when ready; it remains in the session whether or not it is added to the bank.

### Journey B — Practice with MCQs

1. Learner opens a named question set inside chat or from the Question Bank.
2. Answers some or all questions, pauses and resumes later if needed.
3. Submits responses; the application scores against stored keys deterministically.
4. Each attempt is linked to the questions and their revisions; retries preserve previous attempts.
5. Learner can request AI explanation or compare a prior AI solution without overwriting their own answer.
6. AI may receive a compact attempt summary to suggest further teaching or targeted practice.

### Journey C — Short-answer practice

1. Learner opens a question in chat or its focused view, answers in text or uses a supported attachment.
2. Learner saves progress, submits part of the set or returns later.
3. AI evaluates against the saved reference criteria and relevant source context, expressing uncertainty where appropriate.
4. Feedback remains attached to that particular attempt and question revision.
5. Learner may request an optional AI worked solution and compare approaches in later sessions.

### Journey D — Ask about a question

1. Learner references a saved question, option, attempt or solution from a chat or set view, including an earlier session.
2. Harness retrieves the exact permitted object/revision and only necessary source or attempt context.
3. AI explains or teaches without needing the learner to copy the complete old conversation.
4. AI can make precise, permission-checked edits to the question when requested.

### Journey E — Fork an investigation

1. Learner reaches a useful point in a long session.
2. Selects **Fork from here**.
3. New session branch is created.
4. Original remains unchanged.
5. Fork continues using shared workspace objects.

### Journey F — Global catch-up planning

1. Learner asks Coordinator: "What should I study this week?"
2. Coordinator sees compact state for all permitted workspaces.
3. It retrieves deeper details only where needed.
4. It considers deadlines, active plans, progress, and workload.
5. It creates/updates a cross-workspace plan/calendar.
6. Learner can open the relevant workspace/session directly.

### Journey G — Coordinator organizes uploaded material

1. Learner uploads several files to the Coordinator.
2. Coordinator determines or asks where they belong.
3. It creates a workspace if needed.
4. Files are moved/referenced/copied according to the requested organization.
5. Workspace AI can immediately use them.

### Journey H — Change AI mid-session

1. Learner changes the active model/provider.
2. Session and workspace state remain intact.
3. Harness supplies relevant context to the new model.
4. Conversation continues without recreating questions/plans/files.

### Journey I — Curate a Question Bank

1. AI generates **Set A** and **Set B** in one study session; both remain independently named and usable there.
2. Learner practises, discusses or edits their questions directly in chat.
3. Learner selects all of Set A and three chosen questions from Set B for the bank, either with UI controls or an explicit instruction to AI.
4. Bank entries preserve their sets/selected-subset identity and original session links without creating disconnected question copies.
5. Learner can browse uncollected session sets and collect more questions later; prior learner attempts and AI solutions remain linked.

---

## 21. Open questions and risks

The main functional interaction direction is specified for supervisor review. The technical decisions remain provisional, and the following risks should be controlled during implementation:

- **Scope size:** the full vision is intentionally broader than the minimum assigned exercise application; Semester-2 implementation must prioritize a coherent core rather than shallowly implementing every extension.
- **AI variability:** model quality and provider capabilities differ, so application-owned state and deterministic rules must not depend on one model behaving perfectly.
- **Autonomous edits:** high AI autonomy requires permissions, revision history, and recoverability so convenience does not make study content fragile.
- **Context/retrieval mistakes:** selective retrieval can omit relevant material; the system needs traceability and recoverable access to original content.
- **Cross-workspace access:** global coordination is powerful but must respect explicit isolation and memory/library permissions.
- **Source evolution:** source updates can invalidate dependent questions or explanations and therefore require dependency/change awareness.
- **Bank selection versus academic review:** selecting a question for the Question Bank serves as learner acceptance for inclusion; the supervisor should confirm whether this satisfies the assigned brief's use of "approve" without a separate quality-check gate.
- **Precise edit contracts:** the patch interface, validation/repair feedback and version conflicts must be tested against actual chosen runtime APIs.

---

## 22. Supervisor review focus

The current direction is complete enough for review. There are no blocking functional questions at this stage. Supervisor feedback is requested on overall direction, scope priority, and any suggested adjustments, especially around:

- which advanced capabilities should be mandatory in the first Semester-2 implementation versus demonstrated as extensibility;
- how broad the first Global Coordinator implementation should be while keeping evaluation manageable;
- which mind-map interactions/exports should be required initially;
- which model/provider integrations should be demonstrated first;
- how much of the cross-workspace calendar/planning flow should be implemented versus left as a later extension.

The purpose of supervisor feedback is to validate the direction and suggest scope adjustments; no specific decision is being requested from the supervisor at this stage.

---

# Phase 2 — Initial Technical Specification Proposal

> **Reserved for Phase 2.**

The technical phase will define and justify topics such as:

- frontend/backend technologies;
- persistence/database design;
- file/object storage;
- retrieval and source-grounding implementation;
- memory implementation;
- context-window and compaction strategy;
- optional lightweight semantic classification/routing;
- model/provider abstraction and switching;
- skill/MCP/tool integration;
- revision/versioning mechanisms;
- authentication/security;
- deployment architecture;
- evaluation instrumentation;
- fallback and failure-handling strategies.

No technical choice in this section is considered decided yet.