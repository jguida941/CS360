# Android Layout Notes: Gravity & Text/Strings

## Gravity: what it does

`gravity` controls **where content sits inside a view/container**. You give it a
position keyword and it pushes the content there.

### Common values

| Value                | What it does                                      |
|----------------------|---------------------------------------------------|
| `top`                | Pushes content to the **top**                     |
| `bottom`             | Pushes content to the **bottom**                  |
| `center`             | Centers content **both** ways (horizontal + vert) |
| `center_horizontal`  | Centers content **left-to-right** only            |
| `center_vertical`    | Centers content **up-and-down** only              |
| `start`              | Pushes to the start (left in English)             |
| `end`                | Pushes to the end (right in English)              |

So:
- `bottom` = "true" bottom → content goes to the **bottom**.
- `center_horizontal` = content goes to the **horizontal center** (middle across).
- You can **combine** them with `|`:
  ```
  android:gravity="bottom|center_horizontal"
  ```
  → sits at the bottom, centered across the width.

### The two flavors (this trips people up)

- **`android:gravity`** → positions the **content INSIDE** this view.
  (e.g. on a TextView, it moves the *text* inside the box.)
- **`android:layout_gravity`** → positions **THIS view inside its parent**.
  (moves the whole box within the parent layout.)

Rule of thumb:
> `gravity` = my kids. `layout_gravity` = me inside my parent.

---

## Text & Strings

- Text shown to the user comes from the **`android:text`** attribute:
  ```xml
  <TextView android:text="Hello" />
  ```
- **Best practice:** don't hardcode the string. Put it in
  `res/values/strings.xml` and reference it:
  ```xml
  <!-- strings.xml -->
  <string name="greeting">Hello</string>
  ```
  ```xml
  <!-- layout -->
  <TextView android:text="@string/greeting" />
  ```
- Why use strings.xml:
  - One place to edit all text.
  - Easy translation / localization (different languages).
  - Android Studio warns you ("hardcoded string") if you skip it.

- `@string/name` = "go look this up in strings.xml".
- Plain `"Hello"` = hardcoded literal text (works, but not recommended).

---

## How to put everything in the MIDDLE of the screen

Do this in the Design editor (not the XML):

1. In the **Component Tree**, click the **`LinearLayout`** (the root/container that
   holds everything, NOT the button or text).
2. In the **Attributes** panel, find **`gravity`** (use the search box, type `gravity`).
3. Expand the `gravity` row (click the little arrow ▸) and check:
   - **`center_horizontal`** (centers left-to-right)
   - **`center_vertical`** (centers up-and-down)
4. Both checked = dead center of the screen.

> Note: `center_horizontal` + `center_vertical` together = the same as just `center`.
> If it stays stuck at the bottom, make sure **`bottom` is UNCHECKED**, bottom
> overrides center_vertical.

---

## Quick cheat sheet

```
bottom              -> content to bottom
center              -> dead center (both axes)
center_horizontal   -> centered across (left-right)
center_vertical     -> centered up-down
top|end             -> combine with |
gravity             -> content inside the view
layout_gravity      -> the view inside its parent
@string/name        -> text pulled from strings.xml
```

---

## Colors: changing view colors

### Button's purple (filled) color

1. Select `buttonSayHello` in the **Component Tree**.
2. Use the **search icon** in the Attributes panel.
3. Search for **`backgroundTint`**.
4. Click the color box/value next to `backgroundTint`.
5. Choose a color from the picker.

Use **`backgroundTint`**, NOT `background`, because Material-style buttons
normally get their filled color through a **background tint** or the **app
theme**, setting `background` directly can override/break the Material styling.

### Other color controls

| What you want to color        | Attribute to use                         |
|-------------------------------|------------------------------------------|
| Button's words                | `textColor`                              |
| TextView's words              | `textColor`                              |
| Plain Text input's words      | `textColor`                              |
| Whole screen (background)     | select root `LinearLayout` → `background`|

### Reusable color resource

For a permanent, reusable color, the picker may offer:

> **Create New Resource → Color Value Resource**

Android **color resources** (in `res/values/colors.xml`) can then be reused
across multiple views instead of repeating the same hex value everywhere.
Reference them as `@color/name`.

**Refs:**
- MaterialButton API, https://developer.android.com/reference/com/google/android/material/button/MaterialButton
- Resources guide, https://developer.android.com/guide/topics/resources/drawable-resource
