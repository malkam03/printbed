# AGENTS.md

Instructions for any AI agent working in this repo. Read this before touching files, git, or Printables listings.

## What this repo is

3D models Malcolm designs, mostly replacement parts for things too old to buy parts for. Public, licensed CC BY-SA 4.0. Models are also mirrored to Printables.com.

## Hard rules (never undo without asking)

- Never add `Co-authored-by` or `Copilot-Session` trailers to any commit. Malcolm is the sole author; you're here just to help organize the models.
- Commit author and committer are always `Malcolm Davis <me@malcolmdavis.xyz>`.
- Never commit or push without explicit approval. Stage and show the diff, then stop and wait.
- No em dashes, ever. Prefer colons and semicolons; a semicolon needs a complete sentence on both sides.
- Models and photos are made entirely by Malcolm. AI only assists with documentation text; this is disclosed in the root README and must stay true.

## Repo layout

```
README.md          catalog + repo-wide policies
LICENSE             CC BY-SA 4.0
.gitattributes       *.FCStd/*.3mf binary diff=zip
<family>/
  README.md          family index: scope sentence + parts table only
  <model-name>/
    README.md         self-contained model page (see template below)
    <model-name>.3mf / .step / .FCStd
    images/
```

Model folders sit directly under their family folder, not under a shared `models/` directory. Families nest one level only. Root README stays generic and never shows a concrete example tree; it describes the convention in words, not by example.

## File formats

Exactly three files per model, no STL:
- `.3mf`: printable, carries units.
- `.step`: editable in any CAD tool. Exported from FreeCAD (Open CASCADE backend), never from a slicer.
- `.FCStd`: the FreeCAD source.

One printable file means no stale copies drifting out of sync.

## Adding a new model: step by step

1. **Discover loose files.** Malcolm often drops new files directly into a family folder (e.g. `suzuki-samurai/`) via Finder, not as chat attachments. Check with:
   `git status --porcelain=v1 --untracked-files=all` or `find <family> -maxdepth 1 -type f`.
2. **Get the missing facts from Malcolm**, don't assume: what the part is and what it does, material (PLA/PETG/TPU/etc.) and why, exact or measurable dimensions, print settings (supports, orientation, slicer defaults vs custom), fit/tolerance notes, whether more photos are coming.
3. **Measure dimensions from the mesh** if not given (3MF bounding box via the model's XML, or ask Malcolm to measure).
4. **Create the model folder**: `<family>/<model-name>/` (kebab-case).
5. **Rename and move files** into it:
    - `<model-name>.3mf`, `<model-name>.step`, `<model-name>.FCStd`
    - photos into `images/<model-name>-<descriptor>.jpg` (e.g. `-fitted`, `-scale-1`, `-original-vs-printed`)
6. **Strip EXIF from every photo before committing**, no exceptions, even if there's no GPS data. Use Python/PIL (rebuild the image from pixel data, keep only the ICC profile):
   ```python
   from PIL import Image
   im = Image.open(path)
   data = list(im.getdata())
   clean = Image.new(im.mode, im.size)
   clean.putdata(data)
   clean.save(path, icc_profile=im.info.get("icc_profile"))
   ```
   Verify with `Image.open(path)._getexif()` returning `None` afterward.
7. **Write the model README** following the established template (see below).
8. **Update `<family>/README.md`** parts table with the new row.
9. **Update root `README.md`** catalog table with the new row. This is the only root content allowed to change per new model.
10. **Pre-commit review**: diff stat, full README diff, grep for stray `TODO`, confirm every image reference in the README matches an actual file, confirm `.gitattributes` already covers any new extension (it currently covers `.FCStd`/`.3mf` as binary, `.step` as text).
11. **Wait for explicit approval before committing.**

## Model README template

Sections, in order: title, one-line description, "The part" (what it is, where it goes, why it exists), "Printing" (material, settings, orientation, supports), "Fitting" (how it's installed/used), "Files" (table: filename, format, purpose), "License and warning" (repeats the CC BY-SA line and a one-line safety disclaimer even though they're also at root, because GitHub renders this file standalone and Printables mirrors link here with no parent page). Include a Printables link line once published.

Attribution line format: match the wording already used in existing model READMEs (currently "Malcolm Davis Steele").

## Git workflow: branch + PR (current, as of the bolt cap model)

Every new model gets its own branch and PR. Do not push directly to `main`.

1. Create a branch: `git checkout -b <family>-<model-name>` from an up-to-date `main`.
2. Stage the new/changed files, do the pre-commit review above.
3. Wait for explicit approval, then commit (single author, descriptive multi-line message).
4. Push the branch, then open a PR with `create_pull_request` (never `gh pr create`) and a descriptive body.
5. Malcolm merges when ready; do not merge without being asked.

The very first model (mirror-spacer) was pushed directly to `main`. That was the old flow and is not to be repeated.

## Printables listing

For each model, once ready to publish, draft a listing with:
- **Name**: `<Family> <descriptive part name> (<year>)`, matching the vehicle/model naming already used.
- **Summary**: under 120 characters, plain sentence, no markdown.
- **Tags**: space-separated, lowercase, covering vehicle/model/part/material/category terms.
- **License**: CC BY-SA 4.0 maps to Printables' "Creative Commons: Attribution, Share Alike" option. Never "NoDerivatives."
- **Origin**: Original.
- **AI label**: "No, fully human-made" with a parenthetical noting Malcolm drew all geometry and only docs were AI-assisted.
- **Files to upload**: the three model files (3mf, step, FCStd).
- **Description**: write it once in Markdown for reference/editing, but also provide a plain-paragraph version with no `###`/`**` syntax for pasting. Printables' description field is a rich-text (WYSIWYG) editor, not a raw Markdown textarea: pasting Markdown source leaves literal `#`/`*` characters. Either paste plain text and apply bold/headings manually via the toolbar, or copy from a *rendered* Markdown preview (e.g. VS Code's Markdown preview pane) so the clipboard carries real HTML formatting.
- End the description with the GitHub source link: `https://github.com/malkam03/printbed/tree/main/<family>/<model-name>`.
- Once published, add the Printables link to the model's README.

Keep each listing as a draft file outside the repo (session artifacts), not committed, since it's working notes for a manual upload process, not repo content.
