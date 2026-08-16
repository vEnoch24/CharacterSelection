# Character Selection UI (Reworked)

A polished, reusable Unity character-selection UI template originally authored by miscky and reworked by vEnoch24. This package provides a flexible character selection screen with animated buttons, category tabs, ability/biography panels, sound hooks, and easy-to-use prefabs and scripts so you can drop a working character-selection UI into your game or prototype.

This README documents:
- what the package provides,
- required Unity setup and dependencies,
- how to use and customize the prefabs and scripts,
- the public API of the main components,
- troubleshooting, and recommended best practices.

Original author: miscky  
Reworked & maintained by: vEnoch24

Table of contents
- What this is
- Features
- Requirements
- Project layout
- Quick start (open & run)
- Setup: inspector wiring & recommended Animator states
- API reference (scripts & important public fields/methods/events)
- Prefabs & scenes
- Audio, fonts & TextMeshPro
- Customization & theming
- Recommendations (Git LFS, secrets, build)
- Troubleshooting & FAQ
- Contributing, license & credits
- Changelog / TODOs

---

## What this is
A ready-made, visually appealing character-selection UI for Unity projects with:
- animated selection buttons,
- category/tabbed windows,
- animated transition effects,
- support for preview icons, full character icons, and text content (name/type/bio/abilities),
- optional sound feedback on hover/click/select,
- inspector-friendly public fields and UnityEvents for hooking game logic.

### Stack
- Language(s): C#
- Target runtime: Unity (recommended LTS versions; see Requirements)
- Notable packages used: TextMeshPro (UnityPackage / Package Manager), Unity UI (uGUI)

---

## Features
- Character grid with animated hover/press/select states.
- Character details panel (name, type, bio, up to 3 abilities).
- Smooth animated transitions between characters and between windows/tabs.
- Category buttons to group characters.
- Configurable audio for hover/click/select (optional).
- Prefabs and a demo scene to get you started quickly.
- Easy-to-hook UnityEvents for in-game logic on selection & click.

---

## Requirements
- Unity 2019.4 LTS or later recommended (works with most 2019+ releases).
- TextMeshPro package (used by UI text components).
- Animator support (uGUI + Animator components on button and window prefabs).
- AudioSource if using sound features.

---

## Project layout (top-level)
```
Animations/         # Animation assets & meta folders
Editor/             # Editor assets/meta (if present)
Fonts/              # Font assets
Prefabs/            # Ready-to-use prefabs (Character Select, Category, Countdown, etc.)
Scenes/             # Demo.unity scene (Demo)
Scripts/            # Main C# scripts (CharacterSelectManager, CharacterSelectButton, WindowManager, CategoryButton, UIGradient, CountdownBar)
Textures/           # Icon & texture assets
README.md           # This file
.gitattributes      # repository attributes (exists)
```

Key scripts (in Scripts/):
- CharacterSelectManager.cs — orchestrates the details panel and selection cooldown.
- CharacterSelectButton.cs — per-character interactive button (hover/click/selection).
- WindowManager.cs — tab/window manager for switching UI windows.
- CategoryButton.cs — small icon button for category selection.
- UIGradient.cs, CountdownBar.cs, other UI helpers.

Scenes:
- Scenes/Demo.unity — a demo scene wired to show the character selection UI.

Prefabs:
- Prefabs/Character Select.prefab — main prefab containing a selection grid and details window.
- Prefabs/Category (Only Icon).prefab — category icon prefab.
- Prefabs/Countdown (Hexagon).prefab — countdown UI element.

---

## Quick start (open & run)
1. Clone or download this repository and open the folder in Unity (use the recommended Unity version).
2. Open the demo scene: Scenes/Demo.unity.
3. Enter Play Mode. The demo scene shows how the prefabs and scripts are wired together.

If you want to add the UI into an existing project:
1. Copy `Prefabs/Character Select.prefab` and any required textures/fonts/animations into your project.
2. Ensure TextMeshPro is imported and the TMP components in the prefab are valid.
3. Add the prefab to your Canvas and assign references as described below.

---

## Setup: inspector wiring & recommended Animator states

Most wiring is done in the Inspector. Below are the important components and the public fields you should set.

CharacterSelectManager (Scripts/CharacterSelectManager.cs)
- Public inspector fields:
  - selectFirstLine (string) — initial type/helper text
  - selectSecondLine (string) — initial name/helper text
  - selectCharaterIcon (Sprite) — initial icon
  - characterWindow (Animator) — assign window Animator (the details panel)
  - characterImage (Image) — the main character sprite in details
  - characterNameText (TextMeshProUGUI) — visible name text field
  - characterNameHelperText (TextMeshProUGUI) — helper text used during animated transitions
  - characterTypeText, characterTypeHelperText (TextMeshProUGUI)
  - characterBioText (TextMeshProUGUI)
  - firstAbilityText, secondAbilityText, thirdAbilityText (TextMeshProUGUI)
  - selectionCooldown (float) — time between allowed selections (default 1)

- Behavior:
  - The manager listens to CharacterSelectButton events to update the details window and image.
  - It expects certain Animator state names (see below) to compute transition timings.

CharacterSelectButton (Scripts/CharacterSelectButton.cs)
- Public inspector fields:
  - previewIcon (Sprite) — small preview icon for the button
  - characterIcon (Sprite) — big icon for the details panel
  - characterName (string)
  - characterType (string)
  - characterInfo (string) — biography / description (TextArea)
  - firstAbility, secondAbility, thirdAbility (strings)
  - enableButtonSounds (bool) & AudioClip fields (hoverSound, clickSound, selectSound) and AudioSource
  - objectAnimator (Animator) — animator controlling the button visuals
  - characterManager (CharacterSelectManager) — assign the manager instance
  - previewImage (Image) — the preview Image component on the prefab
  - characterText (TextMeshProUGUI) — visible label on the button
  - useCustomContent (bool) — if true, the button won't auto-update UI in Start()

- UnityEvents:
  - onCharacterClick — invoked when button is clicked
  - onCharacterSelection — invoked when selected

- Implementation notes:
  - The script handles pointer enter/exit/click events. It toggles animator states and plays sounds if enabled.
  - On selection it calls the manager to update the details panel.

WindowManager (Scripts/WindowManager.cs)
- Fields:
  - windows (List<WindowItem>) — each WindowItem contains windowName, titleObject (TMP), windowObject (GameObject), buttonObject (GameObject).
  - currentWindowIndex, windowFadeIn / windowFadeOut (animation state names)
  - buttonFadeIn / buttonFadeOut (animation state names)
- Methods:
  - OpenFirstTab(), OpenPanel(string newPanel), NextPage(), PrevPage(), AddNewItem()
- Use: arrange your category/windows in the Inspector and set the appropriate Animator references on each window and button object.

CategoryButton (Scripts/CategoryButton.cs)
- Fields:
  - buttonIcon (Sprite)
  - objectAnimator (Animator)
  - normalImage / pressedImage (Image)
  - sound options and AudioSource
  - onButtonSelection (UnityEvent) — invoked when clicked

---

## Expected Animator state names and transitions

The scripts depend on certain Animator states to measure animation lengths and switch animations. If you use custom animators, ensure they contain equivalent states or edit the code to match your animator names.

Button animators (used by CharacterSelectButton & CategoryButton):
- Normal to Hover
- Hover to Pressed
- Hover to Normal
- Pressed to Selected
- Pressed to Normal
- Selected to Normal

WindowManager window animator states:
- Window In
- Window Out

The names above are the defaults found in the included prefabs/animators — keep them or update the script constants to match your own animators.

---

## Prefabs & Scenes
- Scenes/Demo.unity: demo scene demonstrating how the prefabs, animations, and scripts are wired.
- Prefabs/Character Select.prefab: main prefab. Inspect its child objects — it typically contains the selection grid, details window (with TextMeshPro fields), and a CharacterSelectManager component.
- Prefabs/Category (Only Icon).prefab: compact category button prefab for use with the WindowManager.
- Prefabs/Countdown (Hexagon).prefab: countdown UI element used by the demo.

Open the demo scene to see how everything is connected. Use that wiring as the pattern for integrating the UI into your own scenes.

---

## Audio, fonts & TextMeshPro
- TextMeshPro components are used for text. If you see missing text or "TMP" warnings, import TextMeshPro and re-assign fonts as necessary.
- To enable sound feedback set `enableButtonSounds = true` on the buttons and supply an AudioSource and audio clips for hover/click/select. The scripts call `soundSource.PlayOneShot(...)`.

---

## Customization & Theming
- Colors / gradients: UIGradient.cs is provided for gradient effects (see Scripts/UIGradient.cs).
- Change fonts by swapping assets in the Fonts/ folder or by assigning different TMP font assets in the inspector.
- To change transitions, edit the AnimatorControllers attached to the prefabs; keep or map the state names used in code.
- Extend the CharacterSelectButton UnityEvents in the inspector to trigger your game logic (load scene, confirm selection, equip character).

---

## Recommendations for source control
- The repo contains many Unity meta files and binary assets (textures, prefabs, scene). Use .gitattributes and consider Git LFS for large binary assets to keep repository size manageable.
- Do not commit runtime or build artifacts from Library/ or Temp/ folders.
- Keep sensitive project keys (if any) out of the repository.

---

## Troubleshooting & FAQ

Q: Character image/text does not update when clicking a button.
- Confirm CharacterSelectButton.characterManager is assigned.
- Ensure CharacterSelectButton.objectAnimator and CharacterSelectManager.currentObjectAnimator are set (prefabs should wire these).
- Verify selectionCooldown and enableSelecting flags are not blocking (default cooldown = 1 second).

Q: Text appears missing or displays "TMP" fallback.
- Import TextMeshPro via Package Manager and assign TMP font assets referenced in the prefabs.

Q: Sounds don't play.
- Ensure `enableButtonSounds` is true and `soundSource` is assigned to an AudioSource that is not muted and has the correct output audio mixer.

Q: Animations skip or timing seems wrong.
- The scripts query current animator state length to time coroutines. If your animator lacks the expected states or has different timings, either adapt your animations to match or edit the script to use fixed delays.

---

## API Reference (summary of public methods & events)

CharacterSelectManager
- public void UpdateCharacter() — used internally to revert previous button animations before selecting a new one.
- public void UpdateInfo() — updates the details panel from the currentCharacter; also triggers ChangeCharacterIcon coroutine and selection cooldown.

CharacterSelectButton
- public void UpdateUI() — sets the preview image and label from inspector content.
- public void SelectCharacter() — triggers selection animation and invokes onCharacterSelection.
- UnityEvents: onCharacterClick, onCharacterSelection (hook game logic for click and selection)

WindowManager
- public void OpenFirstTab()
- public void OpenPanel(string newPanel)
- public void NextPage()
- public void PrevPage()
- public void AddNewItem()

CategoryButton
- UnityEvent onButtonSelection — invoked on click

---

## Contributing
If you'd like to contribute:
1. Fork the repository and create a feature branch.
2. Make changes, include tests/examples where useful, and keep asset sizes reasonable.
3. Open a pull request describing your change, include screenshots/GIFs for UI changes.

Please avoid committing large binary assets directly — use Git LFS.

---

## License & credits
- Original template and assets by micsky — thanks for the original work.
- Reworked and adapted by vEnoch24.

---

## Changelog & TODOs (suggested)
- v1.0 — Reworked by vEnoch24: cleaned scripts, fixed inspector wiring, added demo scene.
- TODO:
  - Add a small sample scene demonstrating how to call onCharacterSelection events to load characters/levels.
  - Provide a custom Inspector or editor utilities to speed up wiring.

---
