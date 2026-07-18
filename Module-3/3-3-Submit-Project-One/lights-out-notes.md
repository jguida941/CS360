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
6. [Model methods recap (3.3.3) and my read of isGameOver](#6-model-methods-recap-333-and-my-read-of-isgameover)
7. [The View (MVC)](#7-the-view-mvc)
8. [The Controller (MainActivity)](#8-the-controller-mainactivity)

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

## 6. Model methods recap (3.3.3) and my read of isGameOver

Course matching (method to purpose):

| Method | What it does |
|---|---|
| `selectLight()` | Inverts the selected light and its adjacent lights (true becomes false, false becomes true) |
| `newGame()` | Randomly turns the grid lights on and off (based on the random number generator) |
| `isGameOver()` | Determines if all lights are off (returns true if all off, false otherwise) |
| `isLightOn()` | Returns a light's on/off status; the Controller uses it to draw yellow (on) or black (off) squares |

My read of `isGameOver()`:

- It loops through every row and column.
- If it finds **any** cell that is `true` (a light still on), it returns `false` right away, so the game is not over.
- If it checks **every** cell and finds none on, it falls through to `return true`, so the game is over (all lights off).

## 7. The View (MVC)

- In **MVC**, the **View** defines the UI. Lights Out's UI is a **grid of 9 buttons** with a **New Game** button underneath.
- The layout uses a **ConstraintLayout** with a **GridLayout child** (3 by 3) that holds the 9 light buttons.
- Buttons use **styles** from `themes.xml`; the on/off **colors** come from `colors.xml`; **strings** come from `strings.xml`.
- The app has no dark theme, so the book deletes `themes.xml (night)` under `res/values/themes`.

**strings.xml (Figure 3.3.3):**

```xml
<resources>
    <string name="app_name">Lights Out</string>
    <string name="new_game">New Game</string>
    <string name="congrats">Congratulations!</string>
</resources>
```

**colors.xml (Figure 3.3.4):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="blue_500">#3F51B5</color>
    <color name="blue_700">#303F9F</color>
    <color name="yellow">#FF0</color>
    <color name="black">#000</color>
</resources>
```

`yellow` is a light that is on, `black` is a light that is off; the blues are the app-bar colors.

**themes.xml (Figure 3.3.5):**

```xml
<resources>
    <style name="Theme.LightsOut" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="colorPrimary">@color/blue_500</item>
        <item name="colorPrimaryDark">@color/blue_700</item>
    </style>

    <style name="LightButton">
        <item name="android:layout_height">90dp</item>
        <item name="android:layout_width">90dp</item>
    </style>

    <style name="GameOptionButton">
        <item name="android:layout_height">wrap_content</item>
        <item name="android:layout_width">200dp</item>
        <item name="android:layout_marginTop">5dp</item>
        <item name="android:layout_marginLeft">20dp</item>
        <item name="android:layout_marginRight">20dp</item>
        <item name="android:textSize">20sp</item>
    </style>
</resources>
```

- `Theme.LightsOut` sets the app colors (`colorPrimary`, `colorPrimaryDark`).
- `LightButton` is a 90dp by 90dp square, used for each grid light.
- `GameOptionButton` styles the New Game button (200dp wide, margins, 20sp text).

**Layout (Figure 3.3.6, activity_main.xml):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.zybooks.lightsout.MainActivity">

    <GridLayout
        android:id="@+id/light_grid"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:useDefaultMargins="true"
        android:columnCount="3"
        android:rowCount="3"
        android:layout_margin="25dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" >

        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
        <Button style="@style/LightButton" />
    </GridLayout>

    <Button
        android:id="@+id/new_game_button"
        style="@style/GameOptionButton"
        android:layout_marginTop="20dp"
        android:text="@string/new_game"
        android:onClick="onNewGameClick"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@id/light_grid" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

- The outer `ConstraintLayout` fills the screen.
- The `GridLayout` (`@+id/light_grid`) is 3 columns by 3 rows and holds the nine `@style/LightButton` buttons, centered by constraining its left, right, and top to the parent.
- The New Game `Button` (`@+id/new_game_button`) uses `@style/GameOptionButton`, shows `@string/new_game`, sits below the grid (`layout_constraintTop_toBottomOf="@id/light_grid"`), and calls **`onNewGameClick`** through `android:onClick` (the Controller hook in `MainActivity`).

**3.3.4 fact:** the grid buttons use the **`@style/LightButton`** style.

## 8. The Controller (MainActivity)

The **Controller** is the activity (`MainActivity`) that manipulates the Model and
updates the View. It reads the Model to paint the View, and writes the Model in
response to View events (button taps).

**Fields:**

- `mGame` (the `LightsOutGame` model), `mLightGrid` (the `GridLayout` from the layout), and `mLightOnColor` / `mLightOffColor` (the yellow/black colors resolved to int values).

**`onCreate()`:**

- `setContentView(R.layout.activity_main)`, then `findViewById(R.id.light_grid)`.
- Loops the grid's children and attaches the **same** click handler to every button: `gridButton.setOnClickListener(this::onLightButtonClick)`.
- Resolves the colors once with `ContextCompat.getColor(this, R.color.yellow / R.color.black)`.
- Creates the model (`new LightsOutGame()`) and calls `startGame()`.

**`startGame()`:** `mGame.newGame()` then `setButtonColors()`.

**`onLightButtonClick(view)`** (a tapped light):

- `indexOfChild(view)` gives the flat index (0 to 8) of the tapped button.
- Convert flat index to grid coordinates: **`row = index / GRID_SIZE`**, **`col = index % GRID_SIZE`**.
- `mGame.selectLight(row, col)` updates the model, then `setButtonColors()` repaints.
- If `mGame.isGameOver()`, show a `Toast` with `R.string.congrats` ("Congratulations!").

**`setButtonColors()`** (Model -> View):

- Loops every button, converts its index to `row`/`col` the same way, and sets its background to `mLightOnColor` if `isLightOn(row, col)` else `mLightOffColor`. This is what reflects the model's true/false grid as yellow/black squares.

**`onNewGameClick(view)`:** calls `startGame()`. It is wired to the New Game button by `android:onClick="onNewGameClick"` in the layout.

**Key idea, index to row/col:** the 9 buttons are a flat list of `GridLayout`
children (0 to 8), but the model is a 3x3 array. `index / 3` gives the row and
`index % 3` gives the column, mapping button N to `mLightsGrid[row][col]`.

**Colors are dynamic:** the on/off colors are not set in the layout XML; the
Controller applies them at runtime in `setButtonColors()` so the grid updates as the
model changes.

Full code (Figure 3.3.7):

```java
package com.zybooks.lightsout;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.GridLayout;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.ContextCompat;

public class MainActivity extends AppCompatActivity {

    private LightsOutGame mGame;
    private GridLayout mLightGrid;
    private int mLightOnColor;
    private int mLightOffColor;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mLightGrid = findViewById(R.id.light_grid);

        // Add the same click handler to all grid buttons
        for (int buttonIndex = 0; buttonIndex < mLightGrid.getChildCount(); buttonIndex++) {
            Button gridButton = (Button) mLightGrid.getChildAt(buttonIndex);
            gridButton.setOnClickListener(this::onLightButtonClick);
        }

        mLightOnColor = ContextCompat.getColor(this, R.color.yellow);
        mLightOffColor = ContextCompat.getColor(this, R.color.black);

        mGame = new LightsOutGame();
        startGame();
    }

    private void startGame() {
        mGame.newGame();
        setButtonColors();
    }

    private void onLightButtonClick(View view) {

        // Find the button's row and col
        int buttonIndex = mLightGrid.indexOfChild(view);
        int row = buttonIndex / LightsOutGame.GRID_SIZE;
        int col = buttonIndex % LightsOutGame.GRID_SIZE;

        mGame.selectLight(row, col);
        setButtonColors();

        // Congratulate the user if the game is over
        if (mGame.isGameOver()) {
            Toast.makeText(this, R.string.congrats, Toast.LENGTH_SHORT).show();
        }
    }

    private void setButtonColors() {

        for (int buttonIndex = 0; buttonIndex < mLightGrid.getChildCount(); buttonIndex++) {
            Button gridButton = (Button) mLightGrid.getChildAt(buttonIndex);

            // Find the button's row and col
            int row = buttonIndex / LightsOutGame.GRID_SIZE;
            int col = buttonIndex % LightsOutGame.GRID_SIZE;

            if (mGame.isLightOn(row, col)) {
                gridButton.setBackgroundColor(mLightOnColor);
            } else {
                // ---- 2nd screenshot missing: lines below completed from the standard pattern ----
                gridButton.setBackgroundColor(mLightOffColor);
            }
        }
    }

    public void onNewGameClick(View view) {
        startGame();
    }
}
```

Note: the second screenshot did not come through, so the final lines (the `else`
color branch, the closing braces, and `onNewGameClick`) are completed from the
standard zyBooks pattern. If your file differs, resend and I will correct it.
