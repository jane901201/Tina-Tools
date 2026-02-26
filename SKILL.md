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
