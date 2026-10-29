# Human commit style

## Subject line

- Imperative mood: `Add`, `Fix`, `Refactor`, `Remove`, `Rename`, `Document`.
- 72 characters or fewer, no trailing period.
- Describe the change, not the process. `Add connector token settings` beats
  `Updated some files`.
- Scope is optional but useful in larger repos: `auth: reject expired tokens`.

## Body

Add a body only when the subject cannot carry the reason. One blank line after
the subject, wrap at 72 columns, explain *why* rather than restating the diff.

```text
Bound token login to a 60 second window

Tokens were previously valid until the session expired, which left leaked
links usable for hours. Signing the timestamp keeps the window short.
```

## Good examples

```text
Add project_connection_receiver module
Fix crash when connector URL is empty
Refactor settings sync into a single helper
Remove unused stage push cron
```

## Bad examples and why

```text
Update files
```
Too vague; says nothing about the change.

```text
Generated with OpenCode
```
Tool signature, forbidden.

```text
Add module (as requested by the user)
```
Mentions the request instead of the change.

```text
🤖 Add module

Co-Authored-By: Claude <noreply@anthropic.com>
```
AI attribution and emoji, forbidden.

## Message mechanics

Multiple paragraphs without an editor:

```sh
git commit -m "Add connector token settings" \
  -m "The token is stored in ir.config_parameter so it survives module upgrades."
```

Amending only the message:

```sh
git commit --amend
```

Amending and restoring the configured human identity:

```sh
git commit --amend --reset-author --no-edit
```
