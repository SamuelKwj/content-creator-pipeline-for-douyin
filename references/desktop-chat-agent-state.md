# Desktop workbench: chat-driven content agent state

Use this when implementing the standalone desktop/content workbench mode, especially when the user says the UI still feels like “button input parameters” instead of a real content assistant.

## Core lesson

A chat-driven content assistant is not achieved by only changing button labels or adding a better LLM prompt. The backend needs a small deterministic session state around the LLM so business flow can progress while the user is chatting naturally.

Minimum state:

```json
{
  "profile": {
    "platform": "",
    "track": "",
    "persona": "",
    "content_type": ""
  },
  "collecting_spark": false,
  "pending_spark": {
    "content": "",
    "title_options": [],
    "created_at": ""
  }
}
```

## Recommended routing sequence

1. Classify explicit production commands first: scoring, prediction, script, review, full package.
2. Recognize profile updates, e.g. “我是做抖音个人IP的”, and store them. Do not create artifacts.
3. Empty collect requests, e.g. “收录这个灵感” or a collect button, should enter `collect_guidance`, set `collecting_spark=true`, and ask for the observation. Do not create artifacts.
4. If `collecting_spark=true`, the next meaningful user observation should become `spark_candidate` even if it is phrased conversationally, e.g. “就是，感觉现在生产内容的速度空前……”. Generate 3-4 title directions and ask for confirmation. Do not write files yet.
5. Only after explicit confirmation, e.g. “确认收录 / 就这个 / 可以收录”, write the spark artifacts/inbox entry and clear `collecting_spark` and `pending_spark`.
6. Ordinary chat such as “你好 / 你是谁 / 谢谢” should remain chat and create no artifacts.

## UI rules

- “收录一个灵感” buttons should not send empty templates like `收录这个灵感：` or `固化这个灵感：`.
- Prefer: `我想收录一个灵感，请引导我说清楚。`
- Selecting a material/card should not auto-prefix `固化这个灵感：`; prefer `基于这个火花继续聊：`.
- Show the stage clearly (`chat`, `profile_update`, `collect_guidance`, `spark_candidate`, `spark_solidify`) and distinguish model replies from deterministic workflow replies.

## LLM system prompt requirements

The chat model should be prompted as a content creation assistant, not a model-test bot:

- Role: “你是内容创作助理，不是闲聊测试机器人。”
- Full workflow boundary: 火花/灵感 → 标题选项 → 用户选择标题 → 盲打分 → 收录 inbox.jsonl → 深挖成稿 → 盲打分 → 预测 → 抖音审稿 → 话题建议 → 发布时间建议 → 拍摄登记 → 发布登记 → T+3 复盘 → rubric 升级。
- Do not run the whole workflow just because the user asks “这个选题怎么样”. First classify the real need.
- Ordinary chat should not write files.
- When a user clearly expresses a content observation, organize it as a candidate spark and ask for confirmation/next step.

## Regression tests to add

API-level tests are usually enough:

```text
你好
=> stage chat, artifacts 0

我是做抖音个人IP的
=> stage profile_update, artifacts 0

收录这个灵感
=> stage collect_guidance, artifacts 0

就是，感觉现在生产内容的速度空前，但是我能如此，别人自然也可以如此，那么内容生成不就廉价了？
=> stage spark_candidate, artifacts 0, asks for 确认收录

确认收录
=> stage spark_solidify, artifacts > 0
```

Also test that a selected material label containing “火花：...” does not force `spark_solidify` unless the user explicitly confirms collection.

## Common failure mode

If ordinary HTTP/API probes against current source pass but the browser still shows old behavior, check for old desktop executables or multiple listeners on the same port. Kill only the processes listening on the workbench port and restart the source service; do not kill all Python processes globally.
