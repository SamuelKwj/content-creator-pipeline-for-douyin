# Desktop workflow prompt productization

Use this when turning a creator-specific `AGENTS.md` / workflow prompt into a generic desktop content workbench prompt.

## Trigger

- User asks to "make this workflow generic" or "put this prompt/process into the project".
- Desktop workbench feels like buttons/keyword routing instead of a real content assistant.
- A creator-specific local workflow file needs to become reusable product behavior.

## Workflow

1. Locate the source workflow.
   - If the requested file path is missing or empty, inspect nearby canonical files such as `AGENTS.md`, `WORKFLOW.md`, `README.md`, or project docs before blocking.
   - Say exactly which file was actually used.

2. Extract the class-level process, not the person's identity.
   Keep:
   - demand classification before production
   - fire/spark capture
   - title options before collection
   - blind scoring boundaries
   - prediction immutability
   - publish/retro/rubric loop
   - "do only the requested step" rule

   Remove/generalize:
   - personal names/nicknames
   - local usernames and absolute paths
   - fixed platform/niche/content type unless they come from config/state
   - specific published works and private examples
   - private account state or calibration numbers

3. Externalize the prompt.
   Prefer a file such as:

   ```text
   content-workbench/prompts/content_creator_workflow.md
   ```

   Then load it from code via a small helper such as `load_workflow_prompt()` / `build_workflow_system_prompt(config)` instead of hard-coding the full system prompt inside the chat provider.

4. Add regression checks before implementation.
   Tests should assert:
   - generic workflow prompt file exists
   - required concepts are present: `需求分类`, `火花/灵感`, `盲打分`, `预测`, `拍摄`, `发布`, `复盘`, `不要一条路走到黑`, `只执行当前一步`
   - personal tokens are absent (`KK`, personal nicknames, local username, absolute user path, source project name)
   - runtime system prompt actually includes the workflow prompt

5. Verify behavior through the live chat API after syntax/tests pass.
   Smoke path:
   - `你好？` stays normal chat
   - `我有个灵感` asks for the observation
   - a substantive observation becomes `spark_candidate` with no artifacts
   - conversation messages persist across turns

## Reply UX rules

A workflow engine should not sound like a CI job.

Bad:

```text
当前步骤已完成：内容评分
下一步：预测
已生成：LLM 输出
```

Good:

```text
我先只做了打分，没有继续预测。
分数短板主要在钩子和具体场景。
你现在可以先改开头，或者让我基于这个分数继续做预测。
```

Every business action should tell the user:

1. I did what
2. The result is what
3. The two realistic next choices are what

## Scoring pitfall

Never let the main chat model invent inline scores. If the user says "打分看看":

- run only scoring, not prediction
- use the blind scoring path
- return the actual score/result in the chat response
- if a file is written, still show the core result; do not only say "written"
