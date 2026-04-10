# Repository Guidelines

## Coding Style & Naming Conventions

This project follows a **fail-fast initialization policy**.

All required objects, components, and dependencies are validated **once at startup**.
If any required reference is missing, the application must **throw an explicit error**.

After successful initialization, the codebase **assumes all required objects exist**.
Repeated null / existence checks are intentionally avoided.

Example:

```csharp
public void Awake()
{
        if (player == null)
            throw new MissingReferenceException(
                $"{nameof(Example)}: Player is required but not assigned.");

        if (gameManager == null)
            throw new MissingReferenceException(
                $"{nameof(Example)}: GameManager is required but not assigned.");
}
```

When pointing out issues in code, always include line numbers (e.g., src/main.py:173).

## Execution Policy

- Read-only operations (searching, analyzing, viewing files) do not require confirmation.
- Any file write operation (create/edit/delete) requires prior confirmation.
- Destructive changes (delete/overwrite) must always be confirmed.

在程式裡產生註解時，使用繁體中文，編碼為 UTF-8


無論我用什麼語言，一律用日文跟英文雙版本的內容回答

No matter what language I use, always respond with both Japanese and English versions.

No matter what language I use, always respond in English.

When generating comments in the code, use Traditional Chinese and UTF-8 encoding.

Avoid unnecessary defensive existence/null checks when the invariant is already established by the current control flow.
- Keep checks for factories, async state, user input, and teardown-sensitive paths.
- For internal invariants already proven in the same flow, prefer direct code; use assertions if clarification is needed.

## Handling old functions after refactoring
When refactoring code, do not immediately delete code that appear unused.

Default rule: 
- Mark them with [[deprecated("...")]] first. 
- Explain whether they became unused because of refactoring, API replacement, or architecture changes. 
- If there is a replacement code, include it in the deprecation message.

Example:
cpp
[[deprecated("Unused after PB tree tracking refactor. Use BuildPbTreeFromReaderState() instead.")]]
void BuildLegacyPbTree();
