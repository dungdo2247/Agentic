# Configure Application Settings Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task adds or updates `appsettings` structure, options classes, or configuration binding.

Typical cases:
- add a new setting under `application`
- add a connection string under `connection`
- add or update strongly typed options
- register options in DI

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/Settings.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md` when configuration tests are in scope

Load `instructions/Application.instructions.md` or `instructions/Infrastructure.instructions.md` if the new option belongs to those layers.

## Related patterns

Use as reference:
- `patterns/SettingsPatterns.md`

## Inputs

- setting name
- setting purpose
- target root section
- options class owner
- how the setting is consumed

## Steps

1. Decide whether the setting belongs under `connection` or `application`
2. Update `appsettings` structure using camelCase keys
3. Add or update the strongly typed options class
4. Bind the section using `IOptions<T>` registration
5. Ensure secrets are not committed as real values
6. Note where the setting should be consumed
7. Suggest verification or tests when configuration behavior matters

## Output

- updated configuration structure
- options class changes
- DI binding changes
- usage notes

## Checklist

- root section is correct
- key casing is camelCase
- strongly typed options are used
- secrets are handled safely
