# Module 3 Notes: The Lights Out App (3.3)

Study notes for section 3.3. Lights Out is the running project example that gets
improved in later sections. These notes capture the concept and the setup facts; the
hands-on Android Studio build itself is left to the app.

## Contents

1. [What Lights Out is](#1-what-lights-out-is)
2. [Project setup (reference)](#2-project-setup-reference)
3. [3.3.1 facts](#3-331-facts)

## 1. What Lights Out is

- Goal: turn **all** the lights out, meaning make every square black.
- The board is a grid of squares; each square is a light that is on (yellow) or off (black).
- **Tapping a square toggles that square plus its orthogonally adjacent squares** (up, down, left, right; not diagonals).
- The **New Game** button randomly resets each square to on or off.
- It is built simply here and improved in later sections.

## 2. Project setup (reference)

New Android Studio project settings the book uses:

| Setting | Value |
|---|---|
| Template | Empty Views Activity (Phone and Tablet tab) |
| Name | Lights Out |
| Package name | com.zybooks.lightsout |
| Language | Java |
| Minimum API level | default |
| Build configuration language | Groovy DSL (build.gradle) |

Reach it from the welcome screen ("Start a new Android Studio project") or
`File > New > New Project`.

## 3. 3.3.1 facts

- The Empty Views Activity template creates a **single activity named `MainActivity`** (true).
- `activity_main.xml` does **not** use a `GridLayout` by default. The Empty Views Activity template lays out with a **ConstraintLayout**, so "GridLayout by default" is false.
