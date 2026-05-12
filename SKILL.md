---
name: vs-vcxproj-filter-maintenance
description: Maintain Visual Studio C++ project structure for this project. Use when adding, renaming, or removing C/C++ files, when Solution Explorer filters disappear, or when .vcxproj and .vcxproj.filters entries are out of sync.
---

# Visual Studio Project Structure Skill

Keep Visual Studio project structure stable by treating `.vcxproj` and `.vcxproj.filters` as a pair.

## Define File Roles

- Use `XML_Test_2_11.vcxproj` as the build truth.
- Use `XML_Test_2_11.vcxproj.filters` as Solution Explorer grouping metadata.
- Expect filter groups to disappear when file entries mismatch between the two files.

## Add Files Safely

1. Add files from Solution Explorer (`Add -> Existing Item` or `Add -> New Item`).
2. Verify the file exists in `XML_Test_2_11.vcxproj` under `ClCompile` or `ClInclude`.
3. Verify the same file exists in `XML_Test_2_11.vcxproj.filters`.
4. Assign a `<Filter>` value in `.filters` for organized tree placement.

## Rename Files Safely

1. Rename from Solution Explorer first.
2. Confirm old path is removed in both `.vcxproj` and `.filters`.
3. Confirm new path is added in both `.vcxproj` and `.filters`.
4. Rebuild once to refresh IDE state.

## Remove Files Safely

1. Remove from Solution Explorer.
2. Confirm removal in both `.vcxproj` and `.filters`.
3. Rebuild to ensure no stale references remain.

## Run Sync Check

Use this check after any project-structure edit:

```powershell
rg -n "Libtess2.*Tesselator|ClCompile Include|ClInclude Include" XML_Test_2_11.vcxproj XML_Test_2_11.vcxproj.filters
```

Ensure every project file path in `.vcxproj` has a matching `.filters` entry.

## Recover Missing Filters

1. Close Visual Studio.
2. Delete `.vs` cache folder in the project root.
3. Reopen the solution.
4. If filters are still wrong, repair `XML_Test_2_11.vcxproj.filters` entries to match `.vcxproj`.

## Prevent Common Regressions

- Do not edit only one of `.vcxproj` or `.filters` for file path changes.
- Avoid external rename/move in file explorer without updating project files.
- Keep both files under version control and review diffs together.


## 2. C++ Naming Conventions

### File naming
- Lowercase filenames with underscores as separators. Use `.cpp` for sources and `.h` for headers.

### Types (classes, structs, enums)
- Use CamelCase for type names, e.g. `MyObject`, `UrlTableErrors`.

### Functions
- Use lowerCamelCase for function names, e.g. `openFile()`, `runProcess()`.
- 為每個函式撰寫清楚且簡潔的註解。

### Variables
- Local variables: `lower_snake_case`.
- Struct data members: `lower_snake_case`.
- Class data members: `m_` + `lower_snake_case` (example: `size_t m_count_errors;`).
- Global variables: `g_` + `lower_snake_case` (example: `size_t g_run_count;`).

### Constants
- Use `k` prefix followed by CamelCase, e.g. `kMaxBufferSize` or `kAndroid8_0_0`. Choose one of the two styles (`kMaxBufferSize` preferred) and apply it consistently across the repo.

### Namespaces
- Use `lower_snake_case`, descriptive and hierarchical.

### Class design
- Data members must be private. `protected` allowed only for well-justified virtual extension points.
- Prefer composition over inheritance. No raw owning pointers as class members; use smart pointers for ownership.
- Initialize members at declaration where possible; maintain invariants in constructors.

---

## 3. Language & Modern C++ requirements
- Target: **C++17**.
- Prefer `enum class`, `constexpr`, and `auto` where they improve clarity.
- Range-based for loops encouraged.
- Ownership via RAII. Use `std::unique_ptr` for exclusive ownership and `std::shared_ptr` only when shared ownership is required.
- Raw pointers are allowed only for non-owning references.
- Avoid C API memory functions (`malloc`, `free`) and `printf` for logging; use project logging utilities.

---

## 4. Header structure and include rules
- Use `#pragma once` or a conventional include guard in every header.
- Include ordering: 1) C headers 2) C++ standard headers 3) third-party headers 4) project headers.
- Prefer forward declarations in headers to reduce coupling and build times.
---

## 5. Function documentation template (required)
When Copilot or contributors generate a new function, include this header-style comment above the declaration/definition:

```cpp
// Purpose: Short description of what the function does.
// Inputs: List parameters and their meaning.
// Outputs: Describe return value and ownership semantics.
// Errors: How errors are reported (exceptions, std::optional, std::expected, error types).
// Complexity: Time and memory complexity, e.g. O(n) time, O(1) extra memory.
// Example Usage: Minimal usage snippet.
```

Example:

```cpp
// Purpose: Read file contents into a string.
// Inputs: `const std::string& path` - UTF-8 path to file
// Outputs: `std::optional<std::string>` - file contents or `std::nullopt` on error
// Errors: Returns `std::nullopt` when file cannot be opened/read
// Complexity: O(n) time to read file of n bytes
// Example Usage:
//   auto contents = readFileToString(path);
//   if (!contents) { /* handle error */ }
std::optional<std::string> readFileToString(const std::string& path);
```

---

## 6. Error handling and logging
- Prefer expressive types: `std::optional`, `std::expected<T, E>` (if available), or `Result<T, E>`-style types for recoverable errors.
- Use exceptions for unrecoverable or exceptional failure modes when the platform permits exceptions and the project follows that policy.
- Avoid returning legacy integer error codes unless interfacing with C APIs.
- Use the project logging facility instead of `std::cout`.

Guideline summary:
- Local/fallible operations: return `std::optional` or `std::expected`.
- Operations where failure is exceptional: throw exceptions (document behavior).

---

## 7. Commenting, public API docs, and Doxygen
- All public APIs (headers) must include a Doxygen-style comment covering purpose, parameters, return value, constraints, and edge cases.
- Keep comments concise and accurate; prioritize code readability.

---

## 8. Concurrency
- Use standard synchronization primitives (`std::mutex`, `std::lock_guard`, `std::unique_lock`).
- Avoid custom spinlocks except when justified with benchmarks and peer review.
- Document thread-safety guarantees in public APIs.

---

## 9. Architectural rules and file layout
- Maintain layering: core modules must not depend on UI/platform modules.
- One primary class per header/source pair. Avoid aggregating unrelated classes in the same file.

---

## 11. Performance expectations
- Note time and memory complexity in public APIs (see function template above).
- Avoid excessive heap allocations and unnecessary copies; prefer move semantics when appropriate.
- 
---

## 12. Line endings and formatting
- Use CRLF line endings.
