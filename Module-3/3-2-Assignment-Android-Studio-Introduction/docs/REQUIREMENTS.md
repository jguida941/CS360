# Assignment Requirements

Using the Android Studio Layout Editor, review the readings in this module's
Resources section. This document lists the rubric criteria and how this project
addresses each one.

## Rubric criteria

### Project setup
- [x] Create a new Android Studio project with **No Activity**
      (the Empty Activity option will not allow selecting Java).
- [x] Name the project and include your name in the title
      → `android_studio_intro_justin_guida`.
- [x] Select **Java** as the language.
- [x] Select **API 34** as the target SDK.
- [x] Select **Groovy DSL** as the build config.

> Note: the first-time setup can take up to ~10 minutes while Android Studio
> downloads and configures dependencies. Progress is visible via the **Build**
> tab at the bottom of the screen.

### Layout
- [x] Create a `layout` folder within `res`.
- [x] Create a new file titled **`activity_main.xml`**.
- [x] Create a layout of your choosing that includes a **Button**, a
      **TextView**, and a **Plain Text** (EditText).

### Identify elements with relevant names
- [x] Button text = **"Say Hello"**.
- [x] Remove all text from the **TextView**.
- [x] Button ID = **`buttonSayHello`**.
- [x] Plain Text ID = **`nameText`**.
- [x] TextView ID = **`textGreeting`**.

## Written reflection

### Challenges experienced with Android Studio
_(Add your own reflection here.)_

Some common early challenges:
- Understanding the difference between the **Component Tree** (structure) and the
  **preview**, and knowing you have to select the right element to edit it.
- Learning that `gravity` positions the children **inside** a container, while
  `layout_gravity` positions the element **inside its parent**.
- Centering content: you select the root **LinearLayout** and set its `gravity`
  to `center` (or check `center_horizontal` + `center_vertical`); leaving
  `bottom` checked keeps it pinned to the bottom.
- Avoiding **duplicate IDs**, two views cannot share the same ID.

### Initial experience and questions
_(Add your own reflection here.)_

### Challenges accessing or using Android Studio
_(Add your own reflection here, useful for later work in the course.)_
