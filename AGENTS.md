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
