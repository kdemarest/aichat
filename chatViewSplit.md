## Recommended split for chat-view.js

### 1) chat-view.js (keep thin)
- Obsidian view lifecycle only: onOpen/onClose, mount/unmount, command wiring.
- No heavy formatting, no tool-result transformation.

### 2) src/ui/chat-controller.js
- Orchestrates turn events between AgentLoop and UI.
- Handles send/abort/clear/export actions.
- Owns chat history mutations and state transitions.

### 3) src/ui/message-renderer.js
- Pure rendering transforms for assistant/user/tool messages.
- Converts tool results into display-ready blocks.
- No Obsidian APIs, no network calls.

### 4) src/ui/tool-result-formatters.js
- One formatter per tool family (search, snippets, edit, file ops, etc.).
- Central place for labels/headings/status text.

### 5) src/ui/cfc-status.js
- All CFC clear/remove reason mapping in one table:
  - internal code -> user label -> optional debug detail
- Prevents obtuse reason strings leaking into chat copy.

### 6) src/ui/actions/ (folder)
- clear-chat.js
- export-chat.js
- open-file-from-result.js
- Each action as a small unit, dependency-injected.

### 7) src/ui/view-model.js
- Derives UI state (busy, canSend, canAbort, counters, badges).
- Pure and testable.

## Suggested migration order (safe, incremental)
- Step 1: Extract CFC reason mapping first (low risk, immediate readability).
- Step 2: Extract tool-result formatters (big size reduction in chat-view.js).
- Step 3: Extract action handlers.
- Step 4: Add chat-controller and leave chat-view.js as a shell.
- Step 5: Move remaining pure helpers to view-model/message-renderer.

## Guardrails
- Keep public globals unchanged (Obsidian entry wiring stable).
- No behavior changes during split; copy-then-switch per section.
- Add tiny snapshot-style tests for formatter output so labels don’t regress.
