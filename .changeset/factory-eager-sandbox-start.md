---
'@mastra/factory': minor
---

**Opt in to eager sandbox boot with `eagerSandboxStart`.** Session sandboxes start lazily on the agent's first command, which puts VM boot, clone, and setup on that command's wall clock. The new `eagerSandboxStart` factory option lets a host prefetch instead: it is evaluated once when a session's sandbox is first constructed, and returning `true` fires `start()` in the background so the boot races the model's own latency. The context tells the policy what kind of session is asking — `getRunRole()` resolves the active board-run role (`'work'`, `'review'`, ...) or `null` for plain chat — so executors can boot eagerly while chat sessions stay lazy:

```ts
new MastraFactory({
  // ...
  eagerSandboxStart: async ctx => (await ctx.getRunRole()) === 'work',
});
```

Passing `true` boots every session sandbox eagerly. The prefetch is fire-and-forget: a failed eager start is logged and the session falls back to the normal lazy start on its first command. Omitting the option (or passing `false`) keeps today's fully lazy behavior.
