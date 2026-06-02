---
name: code-connect-setup
description: Sets up Figma Code Connect so developers see real code snippets in Dev Mode instead of auto-generated guesses. Use this skill whenever someone wants to connect their Figma components to a codebase, set up Code Connect, configure a figma.config.json or .figma.ts file, or troubleshoot Code Connect errors.
---

# Code Connect Setup Skill

This skill walks users through setting up Figma Code Connect conversationally, one step at a time. The goal is that a non-technical person — a designer, a product manager — can get their first component connected without needing a developer to help them.

---

## What Code Connect does

When a developer selects a component in Figma's Dev Mode, Figma normally shows auto-generated code. Code Connect replaces that with the real code snippet from the team's actual codebase — the correct import, the correct props, populated with the values from whichever variant is selected.

A single Figma component can have multiple Code Connect definitions attached to it — one per platform. In Dev Mode, a dropdown appears letting the developer switch between them. Each platform (for example web, iOS, Android) lives in a separate repository with its own `figma.config.json`, all publishing to the same Figma file key. The `label` field in each config is what distinguishes them in Figma's Inspect panel.

---

## Step 0 — Upfront questions

Before asking any questions, open with a single short message that sets the scene. Something like:

"Great, let's get you set up. I'll walk you through everything one step at a time — just answer a few quick questions and I'll take care of the rest. First things first..."

Then ask the questions one at a time in the order below using the `ask_user_input` tool for each one. Do not combine them. Do not mention the Figma plan or any other topic before the opening message.

1. **Claude Code** — use `ask_user_input` with the question:
   "Do you have Claude Code installed on your machine?"
   Options:
   - Yes, it is installed
   - I have access but it is not installed yet
   - No, I do not have Claude Code

   If they are unsure what Claude Code is, explain it briefly before asking: it is a version of Claude that runs directly in your project folder and can run commands for you, so you do not have to type them yourself.

2. **Figma plan** — use `ask_user_input` with the question:
   "Are you on a Figma Organisation or Enterprise plan? Code Connect requires Dev Mode, which is **only available on paid plans**. To check: in Figma, click the grid icon at the top left to go to your home screen, then look for your team name and the plan listed beneath it."
   Options:
   - Free (Starter)
   - Professional
   - Organisation
   - Enterprise

   If they select Organisation or Enterprise, proceed. If they select anything else, let them know they will need to upgrade to Organisation or Enterprise before Code Connect will work, and pause the setup.

3. **Single or multiple codebases** — use `ask_user_input` with the question:
   "Are you connecting one codebase or multiple? For example, web, iOS, and Android."
   Options:
   - Just one codebase
   - Multiple codebases

   If they say multiple, explain that each platform needs its own setup with its own `figma.config.json`, and that the `label` value (for example "React", "SwiftUI", "Compose") is what distinguishes them in Figma's Inspect panel. Confirm which platform they are starting with today, and let them know they will repeat the process for each additional one. Then continue with the single codebase flow for the chosen platform.

4. **Starting point** — use `ask_user_input` with the question:
   "How would you like to get started?"
   Options:
   - Use the starter kit (recommended if you are starting fresh)
   - I have an existing codebase

   If they choose the starter kit, follow the **Boilerplate path** below.
   If they choose existing codebase, follow the **Existing codebase path** below.

5. **Framework** — use `ask_user_input` with the question:
   "What framework is your codebase using?"
   Options:
   - React
   - Vue
   - SwiftUI
   - Angular
   - Plain HTML
   - Something else

   If they pick "Something else", ask them to type it and look it up in the framework reference table below.

6. **Operating system** — use `ask_user_input` with the question:
   "What computer are you on?"
   Options:
   - Mac
   - Windows

   Only ask this if they do not have Claude Code installed.

---

## Boilerplate path

This path is for users who are starting fresh or want a quick working example before connecting a real codebase.

Tell them:
"The starter kit is a ready-made repository with all the config files already in place. You just need to clone it, fill in your Figma details, and you will be ready to publish your first connection."

Give them the starter kit repository URL and walk them through cloning it using the terminal or GitHub Desktop instructions from the terminal section below. Once cloned, skip Step 3 (the files already exist in the repo) and continue from Step 2 — Get the Figma file key.

When they reach the config file step, instead of generating new files, tell them to open `figma.config.json` in the cloned repo and update the values directly.

---

## Existing codebase path

This path is for users who have a real project and want to add Code Connect to it. Continue through all steps below, generating the config files from scratch.

---

## Claude Code vs terminal: branching logic

Based on their answer to question 1, follow the appropriate path for all subsequent steps.

### If they have Claude Code installed

Tell them: open Claude Code in their project folder and continue this conversation there. Claude Code will run all commands on their behalf — they will not need to type anything into a terminal themselves. From this point, issue instructions as actions for Claude Code to carry out rather than commands for the user to type.

### If they have access to Claude Code but it is not installed

Walk them through installing it first. They will need to open a terminal for this one-time step:

- **Mac:** press Command + Space, type "Terminal", press Enter
- **Windows:** press the Windows key, type "cmd", press Enter

Then ask them to run:
```
npm install -g @anthropic-ai/claude-code
```

Once installed, tell them to open Claude Code in their project folder and continue the conversation there.

### If they do not have Claude Code

Open with a reassuring message before anything else:

"Let's get started. Since you don't have Claude Code, you'll be typing a few commands into the terminal yourself — don't worry, I'll give you exactly what to type, one step at a time."

Then use `ask_user_input` with the question:
"Do you already have the code on your computer?"
Options:
- Yes, I have cloned the repository
- No, I need help with that bit

Branch based on their answer, then continue with the terminal instructions below.

**Opening the terminal:**
- **Mac:** press Command + Space, type "Terminal", press Enter
- **Windows:** press the Windows key, type "cmd", press Enter

**Yes, I have cloned the repository:**
Tell them to type `cd` followed by a space, then drag the project folder from Finder (Mac) or File Explorer (Windows) directly onto the terminal window — this pastes the full path automatically. Then press Enter. They can confirm they are in the right place by running `ls` (Mac) or `dir` (Windows) and checking that the file names look familiar.

**No, I need help with that bit:**
First, help them choose where to store the project. Recommend the Documents folder as a sensible default and give them the command to navigate there. Explain that `cd` stands for "change directory" — it is just the terminal's way of moving into a folder:

- **Mac:**
```
cd ~/Documents
```
- **Windows:**
```
cd %USERPROFILE%\Documents
```

Tell them they can choose somewhere else if they prefer — a `Projects` or `Code` folder in their home directory is also common — but Documents is a safe, easy-to-find default.

Once they are in the right place, ask if they have GitHub Desktop installed using `ask_user_input`:
"Do you have GitHub Desktop installed? (This is optional — if not, no worries, we will use a simple command instead.)"
Options:
- Yes
- No

If yes, tell them to open GitHub Desktop, find the repository, and click "Open in Terminal" (Mac) or "Open in Command Prompt" (Windows) from the Repository menu — this opens the terminal already pointed at the right folder.

If no, give them the `git clone` route:
```
git clone https://github.com/your-org/your-repo.git
cd your-repo
```
Ask them to replace the URL with their actual repository URL, which they can copy from the green "Code" button on the GitHub repository page.

---

## Framework reference

Use this to set the correct values in `figma.config.json`:

| Framework       | parser  | label   | language |
|-----------------|---------|---------|----------|
| React (JS)      | react   | React   | jsx      |
| React (TS)      | react   | React   | tsx      |
| Vue             | vue     | Vue     | vue      |
| SwiftUI         | swift   | SwiftUI | swift    |
| Jetpack Compose | compose | Compose | kotlin   |
| Plain HTML      | html    | HTML    | html     |
| Angular         | html    | Angular | html     |

---

## Step 1 — Get the Figma file key

Ask the user to open their Figma design file and paste the URL here. Extract the file key yourself — it is the random string between `/design/` and the next `/`:

```
https://www.figma.com/design/aBcDeFgHiJkLmNoP/My-Design-System
                            ^^^^^^^^^^^^^^^^
                            This is the file key
```

Confirm the extracted key back to the user before proceeding.

---

## Step 2 — Generate a Figma access token

Explain what this is before asking them to do it: a Figma access token is a private key that gives the Code Connect tool permission to read your Figma file and publish connections to it. There are two permissions needed:

- **File content - Read:** so the tool can read your Figma components
- **Code Connect - Write:** so the tool can publish the connections back to Figma

Walk them through generating it:
1. From the Figma file browser, click your profile picture (top left) and go to Settings
2. Click the **Security** tab
3. Scroll down to **Personal access tokens** and click Generate new token
3. Name it `code-connect`
4. Set the scopes as described above
5. Click Generate and copy the token immediately — it will not be shown again

Remind them: this token is a secret, like a password. It should never be shared or committed to version control.

---

## Step 3 — Set up the required files

Use the `create_file` and `present_files` tools to generate the files below with the user's actual values already filled in. Do not use placeholders. Do not show code blocks for the user to copy — generate the files directly and present them for download.

Create all three files, then present them together in a single `present_files` call, followed by this message:

"Here are your three configuration files. Download each one and move them into your project folder — the one you navigated to in the terminal. Once they are in place, let me know and we will move on to the next step."

If file creation tools are not available in the current environment, fall back to showing the code blocks for the user to copy manually.

**`figma.config.json`:**
```json
{
  "codeConnect": {
    "figmaFileKey": "[EXTRACTED_FILE_KEY]",
    "include": ["**/*.figma.ts"],
    "exclude": ["**/_template.figma.ts"],
    "parser": "[PARSER]",
    "label": "[LABEL]",
    "language": "[LANGUAGE]"
  }
}
```

**`tsconfig.json`** (only generate this if the user does not already have one):
```json
{
  "compilerOptions": {
    "types": ["@figma/code-connect/figma-types"]
  }
}
```

**`.env`:**
```
FIGMA_ACCESS_TOKEN=[THEIR_TOKEN]
```

After presenting the files, remind the user of two things:
- The `.env` file contains their access token and should never be committed to version control. Ask them to check it is listed in their `.gitignore` — if not, add it.
- The file key in `figma.config.json` is safe to commit. Only the token needs to stay private.

---

## Step 4 — Install the CLI

If they have an existing `package.json`:
```
npm install @figma/code-connect
```

If they are starting fresh:
```
npm init -y && npm install @figma/code-connect
```

Then add these scripts to `package.json`:
```json
"scripts": {
  "publish": "figma connect publish",
  "unpublish": "figma connect unpublish"
}
```

---

## Step 5 — Connect the first component

This step hooks the component up. Ask them to:

1. In Figma, right-click any component in the left panel (not on the canvas) and choose **Copy link to selection**
2. Paste that URL here

Then ask: "What is this component called in your code? For example, if you use it as `<Button />`, just type `Button`."

Use their answer as the component name throughout the generated file. Generate the `.figma.ts` file with the real URL and real component name already in place. For React it looks like this:

```typescript
// url=https://www.figma.com/design/[FILE_KEY]/[FILE_NAME]?node-id=[NODE_ID]

import figma from 'figma'

export default {
  example: figma.code`<Button />`,
  imports: ['import { Button } from "@your-org/design-system"'],
  id: 'button',
}
```

Two things to tell them:
- The `// url=` line must stay as a comment — do not remove the `//`
- The `import figma from 'figma'` line may appear red in their editor — this is cosmetic and can be ignored. It is handled by the CLI at publish time, not by the project itself.

---

## Step 6 — Publish and confirm

```
npm run publish
```

A successful publish prints a list of component names with Figma links. Ask them to click a link, switch to Dev Mode in Figma (toggle at the bottom of the screen, or press Shift+D), and confirm the code snippet appears in the Inspect panel on the right.

Once they confirm it is working, move on to Step 7.

---

## Step 7 — Make the snippet accurate

Now that the connection is working, refine the snippet so what developers see is actually useful. Do this in two parts:

**Import path:**
Before asking, explain what it is: "The import path is the line a developer types at the top of their code file to use this component. It usually looks something like `import { Button } from '@your-org/design-system'`. If you are not sure, your developer or the codebase's README will know."

Then use `ask_user_input` with the question:
"Do you know the import path for this component?"
Options:
- Yes, I know it
- I am not sure — I will ask a developer
- Skip this for now

If they know it, ask them to paste it and update the `imports` value in the `.figma.ts` file accordingly.

If they are not sure or want to skip, leave the placeholder in and let them know: "No problem — the connection still works without it. When you have the import path, just update the `imports` line in the `.figma.ts` file and run `npm run publish` again."

**Property mapping:**
Offer to map the component's properties so the snippet updates dynamically when different variants are selected in Figma. Explain what this means: if their Button has a Size property with Small, Medium, and Large options, the code snippet will automatically show the right size value when a developer selects each variant.

The three methods:

**`instance.getString('PropertyName')`** — for text content such as labels or placeholders

**`instance.getBoolean('PropertyName')`** — for true/false toggles such as disabled or loading

**`instance.getEnum('PropertyName', { FigmaValue: 'codeValue' })`** — for variants with multiple options such as size or type

Property names must exactly match what appears in the Figma right panel when the component is selected. Ask the user to check Figma and list the properties for the component, then generate the mapping code for them.

Once updated, ask them to run `npm run publish` again to push the refined snippet.

If they are setting up multiple codebases, remind them at the end of Step 7 that they will need to repeat the process from Step 1 for each additional platform, using a different `label` value in `figma.config.json` each time.

---

## Step 8 — Connect more components

Once Step 7 is complete, use `ask_user_input` with the question:
"Your first component is connected and working. What would you like to do next?"
Options:
- Connect more components one at a time
- Paste multiple component URLs in bulk
- I am done for now

**One at a time:**
Loop back through Step 5 for each new component. Generate a new `.figma.ts` file each time and present it for download, with the same folder nudge each time:

- **Starter kit path:** "Download this file and move it into the `components/` folder inside your project — the same place your other `.figma.ts` files live. Then run `npm run publish`."
- **Existing codebase path:** "Move this file into whichever folder in your project contains your other `.figma.ts` files. If you are not sure where that is, check the `include` line in `figma.config.json` — it shows the pattern the CLI uses to find them. Then run `npm run publish`."

**Bulk:**
Tell them: "In Figma, right-click each component you want to connect and choose Copy link to selection. For each one, paste the URL and the name it is called in your code. For example:

```
https://www.figma.com/design/abc123?node-id=12-345 | Button
https://www.figma.com/design/abc123?node-id=12-346 | Input
https://www.figma.com/design/abc123?node-id=12-347 | Modal
```

If you are not sure of the code name, use the Figma component name for now and update it later."

Once they paste the list, generate a `.figma.ts` file for each one using the provided component name. Present them all in a single `present_files` call, then follow up with:

- **Starter kit path:** "Download all of these files and move them into the `components/` folder inside your project — the same place your `Button.figma.ts` file lives. Once they are all in there, run `npm run publish` and everything will be connected in one go."
- **Existing codebase path:** "Move all of these files into whichever folder in your project contains your other `.figma.ts` files. If you are not sure where that is, check the `include` line in `figma.config.json` — it shows the pattern the CLI uses to find them. Once they are all in there, run `npm run publish`."

**Done:**
Wrap up with a brief summary of what was set up — which components are now connected, which platform, and a reminder that they can run `npm run publish` any time they add new `.figma.ts` files in future.

---

## Common errors and fixes

**"Invalid figma node URL: the provided node-id is invalid"**
A `.figma.ts` file with a placeholder URL is being picked up by the CLI. Check that `figma.config.json` has `"exclude": ["**/_template.figma.ts"]` and that every `.figma.ts` file in the project has a real Figma URL in the `// url=` line.

**"Using html parser as no supported framework was found"**
The `parser` key is missing from `figma.config.json`. Add it using the framework reference table above.

**"Cannot find module 'figma'" in the editor**
Run `npm install` first. If the error persists, check that `tsconfig.json` exists in your main project folder with `@figma/code-connect/figma-types` in the types array. If both are in place and the error is still there, it is cosmetic and will not affect publishing.

**`import figma from 'figma'` is underlined in red in your editor**
This is a cosmetic warning and can be ignored. The `figma` import is handled by the CLI at publish time rather than by your project's dependencies, so your editor does not recognise it. If your publish runs successfully, everything is working correctly.

**"Unauthorised"**
The access token in `.env` is wrong or has expired. Generate a new one in Figma Settings and replace the value in `.env`.

**"File not found"**
The `figmaFileKey` in `figma.config.json` is wrong. Double-check it against the Figma URL — it is the random string between `/design/` and the next `/`.

**Snippet does not appear in Figma after publishing**
Confirm the user is in Dev Mode (toggle at the bottom of the screen, or press Shift+D). If it still does not appear, check that the component URL in the `.figma.ts` file matches the exact component they are inspecting in Figma.

---

## What to commit

Safe to commit:
- `figma.config.json`
- `tsconfig.json`
- `package.json`
- `components/*.figma.ts` files
- `.env.example` (a template with an empty value for `FIGMA_ACCESS_TOKEN`)

Never commit:
- `.env`
- `node_modules/`
