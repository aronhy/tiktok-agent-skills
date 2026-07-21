# TikTok Agent Adapters and Release Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Package the same nine portable TikTok Skills for Codex, WorkBuddy, and the standalone CLI, then add reproducible npm, documentation, and three-platform release gates without publishing anything.

**Architecture:** Treat the repository-root `skillpack.json` and `skills/` tree as the only Skill source. A generic bundle service computes dependency closure and hashes, while thin Codex and WorkBuddy adapters only choose a destination or export directory. The CLI tarball stages those unchanged assets beside the bundled executable and CI verifies their byte identity before release preparation.

**Tech Stack:** Node.js 20/22, TypeScript, npm workspaces, tsup 8.5.1, Vitest, SHA-256 from `node:crypto`, GitHub Actions, Markdown Agent Skills.

## Global Constraints

- Do not edit, regenerate, or translate `SKILL.md` or `references/` during installation.
- Codex, WorkBuddy, and CLI must resolve byte-identical Skill files and the same `skillpack.json` version.
- Adapters may create local files only in an explicit or verified destination; they must not configure credentials or contact a remote service.
- WorkBuddy paths that are not verified by a supported version or explicitly supplied by the user must fail with an actionable message.
- The npm package is `@aronhy/tiktok-agent`, the binary is `tiktok-agent`, and the first version is `0.1.0`.
- The release target is Node.js 20 and 22 on macOS, Windows, and Linux.
- No TikTok publish, comment, message, follow, like, advertising, account-setting, or other remote write capability may be added.
- No API Key, Token, Cookie, browser Profile, `.env`, local MCP config, customer data, or run Artifact may enter Git or the npm tarball.
- Use forward slashes in Skill references and platform-aware Node path APIs in executable code.
- This plan prepares a releasable tarball and CI checks; it does not run `npm publish`, push Git commits, or create a GitHub release.
- Do not begin this plan until the Organic Skills plan completion gate has passed: the repository-root `skillpack.json` must contain exactly nine validated Skills, `FileSkillCatalog.open()` must load all nine from the repository, and the 201-case routing suite must pass. This plan consumes that immutable, completed manifest; it must not create, repair, or reinterpret it.
- This plan consumes the final Core-and-Connectors public boundary: `AgentError` and shared contracts are imported from `@aronhy/tiktok-agent-core`; `createProgram` is exported by `packages/cli/src/program.ts`; `packages/cli/src/bin.ts` remains the executable entrypoint; and `packages/cli/tsup.config.ts` is the single CLI bundle configuration. Do not introduce a second Runtime, error type, program factory, or inline `tsup` command.
- The root Vitest configuration must include `packages/*/test/**/*.test.ts`, `adapters/*/test/**/*.test.ts`, and `tests/**/*.test.ts`. Every test added by this plan lives in one of those paths, so `npm test` and CI execute it without relying on an ad-hoc filename filter.

---

## File Map

**Create:**

- `packages/core/src/skill-bundle.ts` — dependency-closure, portable-file enumeration, hashing, and copy service.
- `packages/core/test/skill-bundle.test.ts` — closure, byte identity, traversal, overwrite, rollback, and source-immutability tests.
- `adapters/codex/package.json` — private workspace metadata.
- `adapters/codex/tsconfig.json` — adapter TypeScript configuration.
- `adapters/codex/src/index.ts` — Codex target resolution and installation entry point.
- `adapters/codex/test/index.test.ts` — temporary `CODEX_HOME` installation and rollback tests.
- `adapters/workbuddy/package.json` — private workspace metadata.
- `adapters/workbuddy/tsconfig.json` — adapter TypeScript configuration.
- `adapters/workbuddy/src/index.ts` — WorkBuddy export and explicit-target installation entry point.
- `adapters/workbuddy/test/index.test.ts` — export, target validation, unsupported-path, and rollback tests.
- `tests/adapter-conformance/skill-hashes.test.ts` — three-host byte-identity contract.
- `packages/cli/scripts/stage-assets.mjs` — copy immutable Skill assets into the publish staging tree.
- `packages/cli/scripts/verify-tarball.mjs` — inspect and smoke-test a packed tarball.
- `packages/cli/test/verify-tarball.test.ts` — incomplete-package and forbidden-content tests.
- `scripts/check-repository.mjs` — repository secret, placeholder, link, and forbidden-artifact gate.
- `tests/check-repository.test.ts` — repository-checker fixtures and redaction tests.
- `.github/workflows/ci.yml` — Node 20/22 and three-OS verification matrix.

**Modify:**

- `package.json` — include `adapters/*` workspaces and release verification scripts.
- `vitest.config.ts` — include package, adapter, and repository test roots described above.
- `packages/core/src/index.ts` — export the portable bundle service.
- `packages/cli/package.json` — stage assets, declare publish files, and expose adapter dependencies.
- `packages/cli/tsup.config.ts` — extend the existing connector-owned CLI bundle configuration with every private workspace dependency and retain required runtime assets.
- `packages/cli/src/commands/adapter.ts` — expose Codex install and WorkBuddy export/install commands.
- `packages/cli/src/program.ts` — register the adapter command group.
- `.gitignore` — ignore local run, browser, build, coverage, pack, and test artifacts.
- `README.md` — Chinese-first project introduction, nine Skills, CLI, Codex, WorkBuddy, security, and development workflow.
- `mcp.example.json` — retain placeholder credentials and document only supported read-only KSS servers.

---

### Task 1: Add the Portable Skill Bundle Service and Codex Adapter

**Files:**

- Create: `packages/core/src/skill-bundle.ts`
- Create: `packages/core/test/skill-bundle.test.ts`
- Create: `adapters/codex/package.json`
- Create: `adapters/codex/tsconfig.json`
- Create: `adapters/codex/src/index.ts`
- Create: `adapters/codex/test/index.test.ts`
- Modify: `package.json`
- Modify: `vitest.config.ts`
- Modify: `packages/core/src/index.ts`

**Interfaces:**

- Consumes: `SkillCatalog`, the validated `skillpack.json`, a packaged `skillsRoot`, selected Skill names, and a destination directory.
- Produces: `collectSkillBundle(catalog, names): Promise<SkillBundleEntry[]>`, `installSelectedSkills(options): Promise<InstallReceipt>`, `copySkillBundle(options): Promise<InstallReceipt>`, `hashSkillTree(root): Promise<Record<string, string>>`, and `installForCodex(options): Promise<InstallReceipt>`.

- [ ] **Step 1: Write failing bundle and Codex tests**

Create tests that use real temporary directories and real files rather than mocked filesystem calls:

```ts
import { mkdtemp, readFile, writeFile, mkdir } from "node:fs/promises";
import { tmpdir } from "node:os";
import path from "node:path";
import { describe, expect, test } from "vitest";
import { copySkillBundle, hashSkillTree } from "../src/skill-bundle.js";

test("copies the dependency closure without changing bytes", async () => {
  const root = await mkdtemp(path.join(tmpdir(), "skill-source-"));
  const target = await mkdtemp(path.join(tmpdir(), "codex-home-"));
  await mkdir(path.join(root, "skills", "planner", "references"), { recursive: true });
  await writeFile(path.join(root, "skills", "planner", "SKILL.md"), "planner\n");
  await writeFile(path.join(root, "skills", "planner", "references", "workflow.md"), "flow\n");

  await copySkillBundle({
    entries: [{ name: "planner", relativePath: "skills/planner" }],
    sourceRoot: root,
    destinationRoot: path.join(target, "skills"),
    overwrite: false,
  });

  expect(await hashSkillTree(path.join(root, "skills", "planner"))).toEqual(
    await hashSkillTree(path.join(target, "skills", "planner")),
  );
});

```

Put the `CODEX_HOME`-specific test in `adapters/codex/test/index.test.ts`, importing `installForCodex` from `../src/index.js`. Also assert that duplicate destinations fail without `overwrite`, `../` paths are rejected, a manifest cycle fails before copying, installation never changes source bytes, and a forced replacement that fails during verification or swap restores the previous named Skill byte-for-byte.

- [ ] **Step 2: Run the focused tests and verify RED**

Run:

```bash
npm test -- packages/core/test/skill-bundle.test.ts adapters/codex/test/index.test.ts
```

Expected: FAIL because the bundle service and Codex adapter modules do not exist.

- [ ] **Step 3: Implement bundle types and deterministic copying**

Implement these exact public contracts:

```ts
export interface SkillBundleEntry {
  name: string;
  relativePath: string;
}

export interface CopySkillBundleOptions {
  entries: SkillBundleEntry[];
  sourceRoot: string;
  destinationRoot: string;
  overwrite: boolean;
}

export interface InstallReceipt {
  destinationRoot: string;
  installed: Array<{ name: string; hashes: Record<string, string> }>;
}

export async function hashSkillTree(root: string): Promise<Record<string, string>>;
export async function copySkillBundle(options: CopySkillBundleOptions): Promise<InstallReceipt>;
```

Enumerate files with `fs.readdir({ recursive: true, withFileTypes: true })`, sort normalized relative paths, hash bytes with SHA-256, reject symbolic links and paths outside `sourceRoot`, copy into a temporary sibling directory, compare source/destination hashes, and atomically rename only after verification. On existing targets, fail unless `overwrite` is true; when overwriting, replace only the named Skill directory and never delete the whole destination root. A forced replacement must first rename the current named Skill to a unique sibling backup, rename the verified temporary directory into its exact destination, and remove the backup only after the new tree hashes match. If any verification or rename fails after the backup move, atomically rename that backup back to the original destination and surface the original failure; never leave an empty or partially copied named Skill.

- [ ] **Step 4: Implement the Codex adapter**

Use a thin host-specific wrapper:

```ts
export interface CodexInstallOptions {
  codexHome?: string;
  names: string[];
  overwrite: boolean;
}

export async function installForCodex(
  options: CodexInstallOptions,
): Promise<InstallReceipt> {
  const codexHome = options.codexHome ?? process.env.CODEX_HOME ?? path.join(os.homedir(), ".codex");
  return installSelectedSkills({
    host: "codex",
    destinationRoot: path.join(codexHome, "skills"),
    names: options.names,
    overwrite: options.overwrite,
  });
}
```

`installSelectedSkills` must load the packaged manifest, resolve the dependency closure, and call `copySkillBundle`; the adapter must not parse or rewrite Skill Markdown.

Create `adapters/codex/package.json` with this public-package shape (the package remains private, but its exports are what the CLI consumes):

```json
{
  "name": "@aronhy/tiktok-agent-adapter-codex",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "types": "./dist/index.d.ts",
  "exports": { ".": { "types": "./dist/index.d.ts", "import": "./dist/index.js" } },
  "dependencies": { "@aronhy/tiktok-agent-core": "0.1.0" }
}
```

Give it build/typecheck/test scripts. Its `tsconfig.json` extends `../../tsconfig.base.json`, compiles `src` to `dist`, and excludes `test`. The root workspace list is exactly `["packages/*", "adapters/*"]`; update `vitest.config.ts` to the three stated include globs before running the focused test. The Codex package owns only host target resolution and imports all shared bundle/error types from the core package root.

- [ ] **Step 5: Run focused and full tests**

Run:

```bash
npm test -- packages/core/test/skill-bundle.test.ts adapters/codex/test/index.test.ts
npm run typecheck
npm test
```

Expected: all commands exit 0; bundle tests show byte-identical copies and Codex tests install only the selected dependency closure.

- [ ] **Step 6: Commit**

```bash
git add package.json vitest.config.ts packages/core/src/skill-bundle.ts packages/core/test/skill-bundle.test.ts packages/core/src/index.ts adapters/codex
git commit -m "feat(adapters): install portable skills for Codex"
```

### Task 2: Add the WorkBuddy Export and Explicit-Target Adapter

**Files:**

- Create: `adapters/workbuddy/package.json`
- Create: `adapters/workbuddy/tsconfig.json`
- Create: `adapters/workbuddy/src/index.ts`
- Create: `adapters/workbuddy/test/index.test.ts`
- Create: `tests/adapter-conformance/skill-hashes.test.ts`
- Modify: `package.json`

**Interfaces:**

- Consumes: selected Skill names, packaged assets, an export directory, or an explicit verified WorkBuddy target.
- Produces: `exportForWorkBuddy(options): Promise<InstallReceipt>`, `installForWorkBuddy(options): Promise<InstallReceipt>`, and host-conformance hash evidence.

- [ ] **Step 1: Write failing WorkBuddy and conformance tests**

```ts
import { mkdtemp } from "node:fs/promises";
import { tmpdir } from "node:os";
import path from "node:path";
import { expect, test } from "vitest";
import { exportForWorkBuddy, installForWorkBuddy } from "./index.js";

test("exports a portable directory when no verified product path exists", async () => {
  const output = await mkdtemp(path.join(tmpdir(), "workbuddy-export-"));
  const receipt = await exportForWorkBuddy({
    output,
    names: ["tiktok-growth-plan"],
    overwrite: false,
  });
  expect(receipt.installed.map((item) => item.name).sort()).toEqual([
    "tiktok-account-audit",
    "tiktok-category-strategy",
    "tiktok-growth-plan",
  ]);
});

test("refuses an inferred unverified WorkBuddy path", async () => {
  await expect(
    installForWorkBuddy({ names: ["tiktok-growth-plan"], overwrite: false }),
  ).rejects.toMatchObject({ code: "INPUT_MISSING" });
});
```

The conformance test must install/export all nine Skills into three temporary roots representing CLI assets, Codex, and WorkBuddy, then assert equal relative-file maps and SHA-256 values.

- [ ] **Step 2: Run the tests and verify RED**

Run:

```bash
npm test -- adapters/workbuddy/test/index.test.ts tests/adapter-conformance/skill-hashes.test.ts
```

Expected: FAIL because the WorkBuddy adapter does not exist.

- [ ] **Step 3: Implement explicit export and target validation**

Implement:

```ts
export interface WorkBuddyExportOptions {
  output: string;
  names: string[];
  overwrite: boolean;
}

export interface WorkBuddyInstallOptions {
  target?: string;
  names: string[];
  overwrite: boolean;
}

export async function installForWorkBuddy(
  options: WorkBuddyInstallOptions,
): Promise<InstallReceipt> {
  const target = options.target ?? process.env.WORKBUDDY_SKILLS_DIR;
  if (!target) {
    throw new AgentError("INPUT_MISSING", "Provide --target or WORKBUDDY_SKILLS_DIR, or use the export command.", 2, ["workbuddyTarget"]);
  }
  return installSelectedSkills({
    host: "workbuddy",
    destinationRoot: target,
    names: options.names,
    overwrite: options.overwrite,
  });
}
```

`exportForWorkBuddy` creates a portable directory containing `skillpack.json` plus the unchanged dependency closure. Do not guess a product path, inspect private application data, or add WorkBuddy-only instructions to the Skills.

Create `adapters/workbuddy/package.json` with the same public-package shape, replacing the name with `@aronhy/tiktok-agent-adapter-workbuddy`; it has the same core dependency, version, ESM type, declaration export map, and build/typecheck/test scripts as the Codex adapter. Its `tsconfig.json` has the same `../../tsconfig.base.json`/`src`→`dist` convention and excludes `test`. The adapter imports `AgentError` from `@aronhy/tiktok-agent-core`; construct missing-target errors as `new AgentError("INPUT_MISSING", message, { missing: ["workbuddyTarget"] })`. Exit-code conversion belongs only to the connector-owned CLI error mapper.

- [ ] **Step 4: Run adapter and conformance tests**

Run:

```bash
npm test -- adapters/workbuddy/test/index.test.ts tests/adapter-conformance/skill-hashes.test.ts
npm run typecheck
```

Expected: all commands exit 0; unsupported implicit installs fail deterministically, all three host trees have identical hashes, and a simulated forced-replacement failure restores the prior WorkBuddy target tree byte-for-byte.

- [ ] **Step 5: Commit**

```bash
git add package.json adapters/workbuddy tests/adapter-conformance
git commit -m "feat(adapters): export portable skills for WorkBuddy"
```

### Task 3: Wire Adapter Commands into the CLI

**Files:**

- Create: `packages/cli/src/commands/adapter.ts`
- Create: `packages/cli/test/adapter.test.ts`
- Modify: `packages/cli/src/program.ts`
- Modify: `packages/cli/package.json`

**Interfaces:**

- Consumes: the Codex and WorkBuddy adapter functions from Tasks 1 and 2, the connector-owned `createProgram` dependency-injection seam, and the connector-owned `AgentError` → stable CLI-output mapper.
- Produces: `tiktok-agent adapter codex install`, `tiktok-agent adapter workbuddy export`, and `tiktok-agent adapter workbuddy install` without duplicating copy logic.

- [ ] **Step 1: Write failing CLI command tests**

Use injected adapter functions so tests exercise argument mapping, not filesystem mocks:

```ts
test("maps WorkBuddy export arguments without exposing secrets", async () => {
  const calls: unknown[] = [];
  const program = createProgram({
    adapters: {
      exportForWorkBuddy: async (options) => {
        calls.push(options);
        return { destinationRoot: options.output, installed: [] };
      },
    },
  });
  await program.parseAsync([
    "node",
    "tiktok-agent",
    "adapter",
    "workbuddy",
    "export",
    "--output",
    "/tmp/workbuddy-skills",
    "--skill",
    "tiktok-growth-plan",
  ]);
  expect(calls).toEqual([{
    output: "/tmp/workbuddy-skills",
    names: ["tiktok-growth-plan"],
    overwrite: false,
  }]);
});
```

Also test that `--force` maps only to local overwrite, missing WorkBuddy target exits 2 with `INPUT_MISSING`, and help never displays environment values.

- [ ] **Step 2: Run the test and verify RED**

Run:

```bash
npm test -- packages/cli/test/adapter.test.ts
```

Expected: FAIL because the adapter command group is not registered.

- [ ] **Step 3: Implement the command group**

Register three commands with Commander. Each action constructs the exact adapter options and prints only destination, installed Skill names, and hashes. Reject credential-looking flags; do not add MCP configuration mutation.

```ts
const codex = adapter.command("codex");
codex
  .command("install")
  .option("--skill <name>", "Skill to install", collect, [])
  .option("--codex-home <path>")
  .option("--force")
  .action(runCodexInstall);

const workbuddy = adapter.command("workbuddy");
workbuddy
  .command("export")
  .requiredOption("--output <path>")
  .option("--skill <name>", "Skill to export", collect, [])
  .option("--force")
  .action(runWorkBuddyExport);
```

When no `--skill` is provided, select all nine manifest entries. Use the existing CLI error mapper and output formatter; do not add an alternate `createCli`, `Runtime`, `AgentError`, or binary entrypoint. `packages/cli/package.json` must declare exact `0.1.0` dependencies on `@aronhy/tiktok-agent-adapter-codex` and `@aronhy/tiktok-agent-adapter-workbuddy`, and the CLI imports those packages through their public package exports rather than a relative `adapters/` path.

- [ ] **Step 4: Run CLI tests and smoke help**

Run:

```bash
npm test -- packages/cli/test/adapter.test.ts
npm run build
node packages/cli/dist/bin.js adapter --help
node packages/cli/dist/bin.js adapter workbuddy export --help
```

Expected: tests pass; help lists only local install/export options and exits 0.

- [ ] **Step 5: Commit**

```bash
git add packages/cli/src/commands/adapter.ts packages/cli/test/adapter.test.ts packages/cli/src/program.ts packages/cli/package.json
git commit -m "feat(cli): expose portable skill adapters"
```

### Task 4: Stage Immutable Assets and Verify the npm Tarball

**Files:**

- Create: `packages/cli/scripts/stage-assets.mjs`
- Create: `packages/cli/scripts/verify-tarball.mjs`
- Create: `packages/cli/test/verify-tarball.test.ts`
- Modify: `packages/cli/package.json`
- Modify: `packages/cli/tsup.config.ts`
- Modify: `package.json`

**Interfaces:**

- Consumes: built CLI output, repository `skills/`, `skillpack.json`, `mcp.example.json`, `README.md`, and `LICENSE`.
- Produces: an npm tarball containing the executable and unchanged nine-Skill asset tree, with no unresolved private workspace import.

- [ ] **Step 1: Write a failing tarball verification script test**

Create a Vitest test that runs the verifier against an intentionally incomplete fixture tarball and expects these exact machine codes: `PACK_SKILL_COUNT`, `PACK_INTERNAL_IMPORT`, `PACK_SECRET_FILE`, and `PACK_SMOKE_FAILED`. The verifier test imports the script by its public function boundary or launches Node; it must not require an undeclared archive parser. Then run:

```bash
npm test -- packages/cli/test/verify-tarball.test.ts
```

Expected: FAIL because the staging and verification scripts do not exist.

- [ ] **Step 2: Implement deterministic asset staging**

`stage-assets.mjs` must:

1. Remove only `packages/cli/dist/assets` after verifying its exact resolved path.
2. Copy `skillpack.json`, all nine selected `skills/<name>` directories, `mcp.example.json`, root `README.md`, and `LICENSE`.
3. Reject symbolic links, `.env`, `mcp.local.json`, Cookie/Profile names, and files not reachable from the manifest.
4. Compare SHA-256 maps between source and staged Skill trees.
5. Write `dist/assets/build-manifest.json` with package version, skillpack version, file hashes, and no absolute paths.

- [ ] **Step 3: Configure the bundled CLI package**

Use the existing connector-owned `packages/cli/tsup.config.ts` with Node 20 ESM output and a shebang. Bundle every private workspace using `noExternal`; the final list includes `@aronhy/tiktok-agent-core`, `@aronhy/tiktok-agent-provider-openai`, `@aronhy/tiktok-agent-safety-policy`, `@aronhy/tiktok-agent-artifacts`, `@aronhy/tiktok-agent-mcp-client`, `@aronhy/tiktok-agent-browser`, `@aronhy/tiktok-agent-adapter-codex`, and `@aronhy/tiktok-agent-adapter-workbuddy`. Keep `playwright@1.61.1` and `@modelcontextprotocol/sdk@1.29.0` as normal published dependencies. Set:

```json
{
  "name": "@aronhy/tiktok-agent",
  "version": "0.1.0",
  "type": "module",
  "bin": { "tiktok-agent": "./dist/bin.js" },
  "files": ["dist"],
  "engines": { "node": ">=20" },
  "dependencies": {
    "@aronhy/tiktok-agent-adapter-codex": "0.1.0",
    "@aronhy/tiktok-agent-adapter-workbuddy": "0.1.0"
  },
  "scripts": {
    "build": "tsup && node scripts/stage-assets.mjs",
    "prepack": "npm run build"
  }
}
```

The runtime locates `dist/assets/skillpack.json` from `import.meta.url`; it must not depend on repository cwd.

- [ ] **Step 4: Implement tarball verification and clean-install smoke**

`verify-tarball.mjs` must open the `.tgz` with the explicitly declared root development dependency `tar` (add `"tar": "^7.4.3"` to root `devDependencies`; do not rely on a globally installed command or an undeclared tar library), reject forbidden names and secret-looking values, verify nine Skills and build hashes, install the tarball into a fresh temporary npm project with scripts disabled, and run:

```bash
node node_modules/@aronhy/tiktok-agent/dist/bin.js skills list --format json
node node_modules/@aronhy/tiktok-agent/dist/bin.js doctor --offline --format json
```

Assert nine Skill names, no unresolved `@aronhy/tiktok-agent-` import, no credential values, and stable success JSON. The internal-import scan must inspect every staged JavaScript and declaration file in the tarball and reject the full private prefix, including the core, provider, policy, artifacts, MCP, browser, and both adapter package names.

- [ ] **Step 5: Run pack verification**

Run:

```bash
npm run build
npm pack --workspace @aronhy/tiktok-agent
node packages/cli/scripts/verify-tarball.mjs aronhy-tiktok-agent-0.1.0.tgz
npm test
npm run typecheck
```

Expected: all commands exit 0; the verifier reports nine Skills, no forbidden files, no private workspace imports, and two successful clean-install smoke commands.

- [ ] **Step 6: Commit**

```bash
git add package.json packages/cli/package.json packages/cli/tsup.config.ts packages/cli/scripts packages/cli/test/verify-tarball.test.ts
git commit -m "build: package standalone TikTok agent CLI"
```

### Task 5: Add Chinese Documentation, Repository Gates, and Three-Platform CI

**Files:**

- Create: `scripts/check-repository.mjs`
- Create: `tests/check-repository.test.ts`
- Create: `.github/workflows/ci.yml`
- Modify: `.gitignore`
- Modify: `README.md`
- Modify: `mcp.example.json`
- Modify: `package.json`

**Interfaces:**

- Consumes: every implementation and test from the preceding plans.
- Produces: one `npm run verify` release gate, Chinese-first onboarding for all three hosts, and CI evidence on six OS/Node combinations.

- [ ] **Step 1: Write the repository checker tests first**

Create fixtures containing a broken Markdown link, a token prefix assembled as `"gh" + "p_"`, a non-empty secret assignment assembled from `"API_KEY" + "=value"`, a missing Skill reference, and a forbidden Profile file. Assert `scripts/check-repository.mjs` returns a distinct finding for each, while the literal environment reference `${KSS_MCP_KEY}` and empty `.env.example` assignments remain allowed.

Run:

```bash
npm test -- tests/check-repository.test.ts
```

Expected: FAIL because the checker does not exist.

- [ ] **Step 2: Implement the repository checker**

The checker must scan tracked files plus the candidate npm staging tree for:

- token signatures and non-empty secret assignments;
- `.env`, `mcp.local.json`, Cookie, Profile, run Artifact, result, coverage, and browser-report files;
- unfinished-work markers assembled from `"TO" + "DO"`, `"TB" + "D"`, and `"FIX" + "ME"`; invalid local Markdown links; Windows-style Skill links; missing `skillpack.json` targets; duplicate Skill names; and dependency cycles;
- trailing whitespace and unparseable JSON/YAML frontmatter.

Print stable JSON findings and exit 1 when any finding exists. Never print a matched secret value; report only rule ID, path, and line.

- [ ] **Step 3: Update `.gitignore` and Chinese-first README**

Add exact ignore entries for:

```text
.worktrees/
node_modules/
dist/
coverage/
.tiktok-agent/
test-results/
playwright-report/
*.tgz
*.tsbuildinfo
npm-debug.log*
```

Rewrite README sections in this order: result-first project summary; read-only boundary; nine-Skill table; standalone CLI quick start; model configuration with environment-variable names only; KSS MCP setup; optional browser login; structured and chat examples; Codex installation; WorkBuddy export/import; output and source ledger; privacy/security; development/verification; repository map; limitations; license. Do not claim npm is published until an actual release exists.

- [ ] **Step 4: Add one release gate and CI matrix**

Root scripts must expose:

```json
{
  "verify": "npm run check:repo && npm run typecheck && npm test && npm run build && npm run pack:verify"
}
```

Create `.github/workflows/ci.yml` using `actions/checkout` and `actions/setup-node`, with matrix:

```yaml
strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]
    node: [20, 22]
```

Each job runs `npm ci`, `npm run check:repo`, `npm run typecheck`, `npm test`, `npm run build`, and `npm run pack:verify`. Before `npm test`, CI prints the discovered test list or runs the three explicit test-root globs so a missing adapter/repository include fails the job. Browser Fixture tests install Chromium with the platform-appropriate Playwright command and never access real TikTok or real credentials.

- [ ] **Step 5: Run the full release gate from a clean dependency tree**

Run:

```bash
npm ci
npm run verify
git diff --check
git status --short
```

Expected: install succeeds from `package-lock.json`; repository check, typecheck, all tests, build, tarball validation, and smoke tests exit 0; `git diff --check` emits nothing; status lists only the intended Task 5 files before commit.

- [ ] **Step 6: Commit**

```bash
git add .github/workflows/ci.yml .gitignore README.md mcp.example.json package.json scripts/check-repository.mjs tests/check-repository.test.ts
git commit -m "ci: verify portable TikTok agent release"
```

---

## Plan Completion Gate

Before this plan is complete, run:

```bash
npm run verify
npm pack --workspace @aronhy/tiktok-agent --dry-run
git diff --check HEAD~5..HEAD
git status -sb
```

Required result: verification exits 0; the dry-run package includes the CLI and all nine Skill assets; the secret scan has no matches; the commit range has no whitespace errors; the working tree is clean on the implementation branch. Do not publish or push as part of this plan.
