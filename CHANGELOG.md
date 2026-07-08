# Changelog

## [0.2.0] - 2026-07-08

### Added

- **意外退出之 stderr 診斷機制**
  - 當 MCP 伺服器在 `initialize` 或握手階段無預警退出，或 `StdioClient` 讀取 `stdout` 到達 EOF 時，會自動提供最後 10 行的 `stderr` 輸出，方便呼叫端（如 `ask-bridge`）和使用者排查錯誤。

### Changed

- **文件優化**
  - 調整 `README.md` 佈局及語言切換連結。

---

## [0.1.1] - 2026-06-09

### Changed

- **多語言文件**
  - 將專案主 `README.md` 本地化為繁體中文（zh-TW），並將英文版文件移至 `README.en.md`。
- **CI/CD**
  - 修正 `npm-publish` 工作流的觸發條件。

---

## [0.1.0] - 2026-06-09

### Added

- **Rust 完整重構**
  - 將 `mcp-cli` 從 TypeScript 完整重構成純 Rust 實作，大幅提升啟動與執行效能，並大幅降低記憶體與磁碟佔用。
- **Rust Library 公開 API**
  - 新增 `src/lib.rs` 與 `src/api.rs`（高階 `McpClient` API），讓本專案不僅是 CLI 工具，亦可作為 Rust crate 依賴套件。
- **常駐連線與多平台守護行程支援**
  - 支援 Windows daemon transport，在 Windows 上也能使用 connection pool 保持 MCP 連線溫熱。
- **安裝與發布工具鏈**
  - 新增 `Makefile`，支援本機一鍵安裝 `make install`（安裝至 `~/.local/bin`）與解除安裝 `make uninstall`。
  - 新增 `@willh/mcp-cli` npm wrapper，於 `postinstall` 階段自動根據使用者平台從 GitHub Release 下載同版本原生 binary，同時補全 `npm-publishing` 發布指南。

---

## [0.3.0] - 2026-01-22 (TypeScript Legacy)

### Added

- **Server Instructions Support** - Display MCP server instructions in output
  - `mcp-cli` (list all): Shows first line of instructions per server
  - `mcp-cli info <server>`: Shows full instructions under "Instructions:" heading

- **Tool Filtering** - Restrict tools per server via config
  - `allowedTools`: Glob patterns for tools to allow (e.g., `["read_*", "list_*"]`)
  - `disabledTools`: Glob patterns for tools to exclude (e.g., `["delete_*"]`)
  - `disabledTools` takes precedence over `allowedTools`
  - Filtering applies globally to all CLI operations (info, grep, call)

- **Connection Daemon** - Lazy-spawn connection pooling
  - Per-server daemon keeps MCP connections warm
  - 60s idle timeout (configurable via `MCP_DAEMON_TIMEOUT`)
  - Automatic config hash invalidation
  - `MCP_NO_DAEMON=1` to disable

- **3-Subcommand Architecture** - `info`, `grep`, `call`
  - Flexible format support: `server tool` and `server/tool`
  - `call` outputs raw text content (CLI-friendly, pipe to grep/head/etc.)
  - `info`/`grep` output human-readable format

- **Improved Error Messages for LLMs**
  - AMBIGUOUS_COMMAND: Shows both `call` and `info` options
  - UNKNOWN_SUBCOMMAND: Smart mapping (run→call, list→info, search→grep)
  - MISSING_ARGUMENT: Shows available servers list
  - INVALID_JSON: Schema hint with example

- **Advanced Chaining Examples** - New documentation section
  - Search and read pipelines with jq
  - Multi-file processing with loops
  - Conditional execution with `jq -e`
  - Multi-server aggregation
  - Error handling patterns

- **Generate System Instructions Script** - `scripts/generate-system-instructions.ts`

### Changed

- **CLI Command Structure**
  - `mcp-cli` (no args) lists all servers
  - `mcp-cli info <server>` requires a server argument

- **Grep Output Format**
  - Output now uses space-separated format: `<server> <tool> <description>`
  - Descriptions are always shown when available
  - Pattern now matches tool name only (not server name or description)

### Removed

- **Backward Compatibility Syntax** - `mcp-cli server/tool [args]` now errors with helpful message
- **`--json` and `--raw` options** - Output format now automatic based on command
