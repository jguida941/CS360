# Module 3 Notes: The Lights Out App (3.3)

Study notes for section 3.3. Lights Out is the running project example that gets
improved in later sections. These notes capture the concept and the setup facts; the
hands-on Android Studio build itself is left to the app.

## Contents

1. [What Lights Out is](#1-what-lights-out-is)
2. [Project setup (reference)](#2-project-setup-reference)
3. [3.3.1 facts](#3-331-facts)
4. [The Model (MVC)](#4-the-model-mvc)
5. [LightsOutGame class (the Model)](#5-lightsoutgame-class-the-model)

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

## 4. The Model (MVC)

- In **MVC**, the **Model** holds the game logic and contains **no UI code**.
- The Model stores the lights' on/off states as a **3x3 array of booleans** (`mLightsGrid`): `true` = light on, `false` = light off.
- Indexed as `[row][col]`, rows 0 to 2 and columns 0 to 2.
- What the Model must do (3.3.2):
  1. Represent the grid as a 2D boolean array, 3 rows by 3 columns.
  2. **New game:** randomly assign each light `true` or `false`.
  3. **Click a light:** flip the appropriate elements (`true -> false`, `false -> true`).
- The Model class `LightsOutGame` exposes methods the Controller calls to read and change state and update the View.

Note on the starting state: the book's caption describes the grid as conceptually
"all on" at first, but in the Java code `new boolean[3][3]` leaves every cell at its
default `false`. `newGame()` is what fills the grid with random values.

## 5. LightsOutGame class (the Model)

Figure 3.3.2, the full Model class.

```java
package com.zybooks.lightsout;

import java.util.Random;

public class LightsOutGame {
    public static final int GRID_SIZE = 3;

    // Lights that make up the grid
    private final boolean[][] mLightsGrid;

    public LightsOutGame() {
        mLightsGrid = new boolean[GRID_SIZE][GRID_SIZE];
    }

    public void newGame() {
        Random randomNumGenerator = new Random();
        for (int row = 0; row < GRID_SIZE; row++) {
            for (int col = 0; col < GRID_SIZE; col++) {
                mLightsGrid[row][col] = randomNumGenerator.nextBoolean();
            }
        }
    }

    public boolean isLightOn(int row, int col) {
        return mLightsGrid[row][col];
    }

    public void selectLight(int row, int col) {
        mLightsGrid[row][col] = !mLightsGrid[row][col];
        if (row > 0) {
            mLightsGrid[row - 1][col] = !mLightsGrid[row - 1][col];
        }
        if (row < GRID_SIZE - 1) {
            mLightsGrid[row + 1][col] = !mLightsGrid[row + 1][col];
        }
        if (col > 0) {
            mLightsGrid[row][col - 1] = !mLightsGrid[row][col - 1];
        }
        if (col < GRID_SIZE - 1) {
            mLightsGrid[row][col + 1] = !mLightsGrid[row][col + 1];
        }
    }

    public boolean isGameOver() {
        for (int row = 0; row < GRID_SIZE; row++) {
            for (int col = 0; col < GRID_SIZE; col++) {
                if (mLightsGrid[row][col]) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

Method summary:

- **`GRID_SIZE = 3`** and **`mLightsGrid`** (a `boolean[3][3]`) hold the board. `mLightsGrid` is `final`, so the array reference never changes, but its contents do.
- **Constructor** allocates the 3x3 grid; every cell defaults to `false`.
- **`newGame()`** loops every cell and sets it to `randomNumGenerator.nextBoolean()` (random `true`/`false`).
- **`isLightOn(row, col)`** returns whether that cell is on. This is a read method the View uses to draw the grid.
- **`selectLight(row, col)`** toggles the tapped cell **and its four orthogonal neighbors** with the `!` (NOT) operator. The four `if` bounds checks (`row > 0`, `row < GRID_SIZE - 1`, `col > 0`, `col < GRID_SIZE - 1`) skip neighbors that would fall off the edge of the grid.
- **`isGameOver()`** scans the whole grid; if any cell is still `true` (a light is on) it returns `false`. It returns `true` only when every light is off, which is the win condition.
