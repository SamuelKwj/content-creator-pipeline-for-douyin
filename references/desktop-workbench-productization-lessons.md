# Desktop Workbench Productization Lessons

Use this when turning a local content-creation workflow into a desktop/browser workbench, especially when users complain that the product still feels like buttons and parameters instead of a real chat assistant.

## Durable product rules

1. Chat is the primary interface.
   - Quick buttons may seed a conversation, but must not send empty command templates like `收录这个灵感：`.
   - Buttons should send natural guidance prompts, e.g. `我想收录一个灵感，请引导我说清楚。`.

2. Normal chat must not create artifacts.
   - Casual messages return `stage=chat`, `artifacts=[]`, and no inbox writes.
   - Incidental labels inserted by the UI, such as `火花：...`, must not trigger solidification.

3. Use a small persistent state machine before introducing a full workflow engine.
   - `profile`: platform / content type / niche / persona.
   - `collecting_spark`: user has asked to collect an idea and the next substantive message should be treated as a candidate.
   - `pending_spark`: candidate content + title options waiting for user confirmation.
   - Only explicit confirmation should persist files or inbox records.

4. Make workflow execution conversational.
   - After each business action, show: what was done, the actual result, and two next choices.
   - Do not return only mechanical text like `当前步骤已完成 / 下一步：预测 / 已生成：LLM 输出`.
   - If scoring, the score result must be visible in the chat; do not only say a file was written.

5. Do not auto-run the whole pipeline.
   - A request like “这个选题怎么样” is selection/validation only.
   - “打分看看” means score only, not score + predict.
   - Each step should stop and offer the next step unless the user explicitly asked for a full package.

6. Conversation persistence is product-critical.
   - Add list/create/load/delete conversation APIs.
   - Send `conversation_id` with every chat request.
   - Store both user and assistant turns.
   - Pass recent turns to the LLM so follow-up questions are not treated as new sessions.

7. Composer should support continuous dialogue.
   - Sending a message must not call `blur()` or force a compact input state.
   - Keep the input expanded and focused for follow-up conversation.

8. Left sidebar information architecture.
   - Put conversation management with the spark board in the left rail/sidebar.
   - The center column should stay focused on the active guided conversation.
   - Avoid a separate conversation-management panel above the chat; it makes the chat feel secondary.

## Prompt productization pattern

Move the workflow/system prompt out of code:

- `prompts/content_creator_workflow.md`: generic workflow prompt.
- `build_workflow_system_prompt(config)`: loads the prompt and appends current creator config.

The prompt must be generic:

- No hard-coded creator name, nickname, private path, existing作品, or platform assumptions.
- Creator identity comes from config, state, or the current conversation.
- Include demand classification, “do not run the whole pipeline”, “only execute the current step”, blind scoring, prediction immutability, and conversational response style.

## Regression checks to add

Backend/API:

- Casual chat returns `stage == "chat"`, has no artifacts, writes no inbox records.
- Empty collect intent returns guidance and sets collecting state.
- Next substantive free-form message after collect guidance returns `spark_candidate` without artifacts.
- Confirmation returns solidification/persistence and clears pending state.
- Conversation create/list/load/delete works.
- A second message in the same conversation receives previous turns as context.

Frontend/static:

- `conversationList`, `newConversation`, `deleteConversation`, `activeConversationId`, and `loadConversations` exist.
- Chat requests include active `conversation_id`.
- Composer does not call `blur()` after send.
- Conversation management lives in the left sidebar with the spark board.
- No center-column `conversation-panel` remains.

Prompt:

- Generic prompt file exists.
- Contains key terms: `需求分类`, `火花/灵感`, `盲打分`, `预测`, `拍摄`, `发布`, `复盘`, `不要一条路走到黑`, `只执行当前一步`.
- Does not contain creator-specific names, private paths, or project-specific personal labels.
- The backend system prompt actually loads this file.

## Windows/local dev notes

- When a path contains Chinese characters, prefer shell commands of the form: `cd '/c/Users/<user>/Documents/<project>' && ...` rather than using it as a tool workdir if the tool rejects non-ASCII workdirs.
- Before claiming the browser is on the new version, verify the port listener and restart the current source service if an old packaged executable or older Python process is still serving the same port.
