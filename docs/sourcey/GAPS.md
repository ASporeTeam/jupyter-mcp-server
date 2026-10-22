# What the published reference does not cover yet

A measurement of the documentation this repository publishes at
<https://jupyter-mcp-server.datalayer.tech/mcp/>, taken from the pinned commit
`ef47e8b9c9bfce1a23265831a1ad80f94d58bb85` by rebuilding the site with the
repository's own `sourcey` version and configuration and comparing every
generated page with the page the site serves (26 of 26 pages byte-identical).

This is a short list of gaps that a reader of the published reference will hit,
with the numbers behind each one, so that a follow-up can be scoped from
evidence instead of from impression.

## 1. The reference documents the transport surface only

At the pinned commit the Python package exports **102 public classes and 149 public
functions (251 public symbols)** across 66 modules — `JupyterMCPServer`,
`JupyterMCPConfig`, `JupyterPasswordAuth`, `CapabilityRegistry`,
`DocumentProvider`, `Transport` and the rest. The published reference documents
**none** of them: it covers the MCP tool and prompt surface (22 tools + 1
prompt) and nothing else.

A maintainer embedding this server in Python gets no API reference for the
objects they are embedding.

## 2. No worked example on any tool page

0 of 22 tool pages carry a worked example. The prose explains non-obvious
precedence ("Given both, the id wins"), but no page shows a request/response
pair a reader can copy. For a protocol surface, that is the single most useful
thing a page can carry.

## 3. No source anchors on the results

A documented result field cannot be traced back to a line in
`jupyter_mcp_server/server.py`. The tool pages link to the repository, but not to
the code that produces the field, so a reader cannot verify a claim about the
behaviour from the page itself.

## 4. The extension-provided tools are not marked as such

`launch_sandbox`, `list_sandboxes`, `use_sandbox` and `terminate_sandbox` are
registered by the extension in
`extensions/sandboxes/jupyter_mcp_sandboxes/extension.py`, while the other 19
tools live in `jupyter_mcp_server/server.py`. The reference renders both groups
identically, so a reader wires the sandbox group in as if it were core and then
hits an error on a stock install.

## 5. No per-tool server-mode column

The server skips `connect_to_jupyter` when it runs as a Jupyter extension
(`jupyter_mcp_server/server.py:1935`). That difference is invisible in the
reference, and it is exactly the case an agent hits first.

## 6. The machine-readable entry point is undiscoverable

`/mcp/llms.txt` (7,583 bytes) and `/mcp/llms-full.txt` (106,191 bytes) are
generated and served, but `/llms.txt` 404s and no page links to them. An agent
that reads the landing page never learns the machine-readable entry point
exists. This is the cheapest fix in the set.

## 7. The published reference is one release behind `main`

The site serves 22 tool pages; the pinned tree registers 23 public
tools/prompts. `docs/sourcey/sourcemap.json` and the `make sync-sourcey` target
are what regenerate it, but CI regenerates on every pull request rather than on
release, which is how the published site ends up behind `main`.

## Reproducing the measurement

The comparison is reproducible with the repository's own pipeline: build the site
from the pinned commit with `npm install && npm run mcp-reference`, then compare
each generated page with the served page (sha256). A parity record for the pinned
commit is available in the discussion of the pull request that added this file.
