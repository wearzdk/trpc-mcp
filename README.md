# tRPC <-> MCP

Serve tRPC routes via MCP.

## Usage

### 2. Add to meta

```ts
import { initTRPC } from "@trpc/server";
import { type McpMeta } from "trpc-mcp";

const t = initTRPC.meta<McpMeta>().create();
```

### 3. Enable for routes

```ts
export const appRouter = t.router({
  sayHello: t.procedure
    .meta({ mcp: { enabled: true, description: 'Greet the user' } })
    .input(z.object({ name: z.string() }))
    .output(z.object({ greeting: z.string() }))
    .query(({ input }) => {
      return { greeting: `Hello ${input.name}!` };
    });
});
```

### 4. Serve

```ts
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { createMcpServer } from "trpc-mcp";

const mcpServer = createMcpServer(
  { name: "trpc-mcp-example", version: "0.0.1" },
  appRouter
);

const transport = new StdioServerTransport();
await mcpServer.connect(transport);
```
