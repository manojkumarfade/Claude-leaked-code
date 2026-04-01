# Claude Code (Unofficial Source Extraction)

> **This is NOT an official Anthropic repository.**

This repository contains the extracted TypeScript source code of [Anthropic's Claude Code](https://www.anthropic.com/) CLI tool — Anthropic's official CLI that lets you interact with Claude directly from the terminal to perform software engineering tasks like editing files, running commands, searching codebases, managing git workflows, and more.

The source was obtained by unpacking the source map (`cli.js.map`) bundled with the officially published npm package.

- **npm package:** [@anthropic-ai/claude-code v2.1.88](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88)
- **Official homepage:** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

## How It Leaked [READ THE FULL ARTICLE](https://techstartups.com/2026/03/31/anthropics-claude-source-code-leak-goes-viral-again-after-full-source-hits-npm-registry-revealing-hidden-capybara-models-and-ai-pet/) 
<img width="838" height="727" alt="image" src="https://github.com/user-attachments/assets/bcd19233-7300-4559-b1bf-2ee2f234d0ec" />


The source code leak was discovered by [Chaofan Shou (@Fried_rice)](https://x.com/Fried_rice) and posted publicly on March 31, 2026:

> *"Claude code source code has been leaked via a map file in their npm registry!"*
>
> — [@Fried_rice](https://x.com/Fried_rice), March 31, 2026

The published npm package (`@anthropic-ai/claude-code`) included a source map file (`cli.js.map`) containing the full, unobfuscated TypeScript source code. The `sourcesContent` field of the source map held every original `.ts`/`.tsx` file that was bundled into `cli.js`, making the entire codebase trivially extractable.

## Why does this exist?

Anthropic publishes Claude Code as a bundled JavaScript CLI on npm. The published package includes a source map file (`cli.js.map`) that contains the original TypeScript source. This repository simply extracts and preserves that source for easier reading and reference.

## [HOW DOES CALUDE WORK ACTUALLY?](https://www.mintlify.com/VineeTagarwaL-code/claude-code/concepts/how-it-works)->[VineeTagarwal](https://github.com/VineeTagarwaL-code)
Based on everything explored in the source code, here's the full technical recipe behind Claude Code's memory architecture:

[shared by claude code]

Claude Code’s memory system is actually insanely well-designed. It isn't like  “store everything” but constrained, structured and self-healing memory.

The architecture is doing a few very non-obvious things:

> Memory = index, not storage
+ MEMORY.md is always loaded, but it’s just pointers (~150 chars/line)
+ actual knowledge lives outside, fetched only when needed

> 3-layer design (bandwidth aware)
 + index (always)
 + topic files (on-demand)
+ transcripts (never read, only grep’d)

> Strict write discipline
 +  write to file → then update index
 + never dump content into the index
 +  prevents entropy / context pollution

> Background “memory rewriting” (autoDream)
 +  merges, dedupes, removes contradictions
 +  converts vague → absolute
 +  aggressively prunes
 +  memory is continuously edited, not appended

> Staleness is first-class
 + if memory ≠ reality → memory is wrong
 +  code-derived facts are never stored
 +  index is forcibly truncated

> Isolation matters
 + consolidation runs in a forked subagent
 + limited tools → prevents corruption of main context

> Retrieval is skeptical, not blind
 +  memory is a hint, not truth
 +  model must verify before using

> What they don’t store is the real insight
 +  no debugging logs, no code structure, no PR history
 +  if it’s derivable, don’t persist it
<img width="1353" height="811" alt="image" src="https://github.com/user-attachments/assets/0b559b8a-9553-4cfd-9dfd-537f49c9f56b" />


## How to get it yourself

### Clone this repository

```bash
git clone git@github.com:chatgptprojects/claude-code.git
cd claude-code
```

### Or extract it yourself from npm

1. **Install the package:**

```bash
mkdir claude-code-extract && cd claude-code-extract
npm pack @anthropic-ai/claude-code@2.1.88
tar -xzf anthropic-ai-claude-code-2.1.88.tgz
cd package
```

2. **Run the unpack script:**

Create a file called `unpack.mjs`:

```js
import { readFileSync, writeFileSync, mkdirSync } from "fs";
import { dirname, join } from "path";

const mapFile = join(import.meta.dirname, "cli.js.map");
const outDir = join(import.meta.dirname, "unpacked");

console.log("Reading source map...");
const map = JSON.parse(readFileSync(mapFile, "utf-8"));

const sources = map.sources || [];
const contents = map.sourcesContent || [];

console.log(`Found ${sources.length} source files.`);

let written = 0;
let skipped = 0;

for (let i = 0; i < sources.length; i++) {
  const src = sources[i];
  const content = contents[i];

  if (content == null) {
    skipped++;
    continue;
  }

  const outPath = join(outDir, src.replace(/^\.\.\//g, ""));
  mkdirSync(dirname(outPath), { recursive: true });
  writeFileSync(outPath, content);
  written++;
}

console.log(`Done! Wrote ${written} files to ${outDir}`);
if (skipped > 0) console.log(`Skipped ${skipped} files with no content.`);
```

3. **Run it:**

```bash
node unpack.mjs
```

The extracted source will be in the `unpacked/` directory.

## Project Structure

```
src/
├── cli/           # CLI entrypoint and argument parsing
├── commands/      # Command implementations
├── components/    # UI components (Ink/React)
├── constants/     # App constants and configuration
├── context/       # Context management
├── hooks/         # React hooks
├── ink/           # Terminal UI (Ink framework)
├── services/      # Core services
├── skills/        # Skill definitions
├── tools/         # Tool implementations (file editing, search, etc.)
├── types/         # TypeScript type definitions
├── utils/         # Utility functions
├── main.tsx       # Main application entry
├── query.ts       # Query handling
└── ...
```

## Disclaimer

All code in this repository is the intellectual property of [Anthropic](https://www.anthropic.com/). This repository is provided for **educational and reference purposes only**. Please refer to Anthropic's [license terms](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88) for usage restrictions.

This is **not** affiliated with, endorsed by, or supported by Anthropic.
