# Fitness & Nutrition Automation Project

_Last updated: 2025-11-22_

This project is a structured system of CSV files designed to work with Airtable and a Custom GPT to manage:

- Meal planning
- Workout scheduling
- Workout difficulty and progression
- Daily fitness, nutrition, and wellness logging

The idea is:
- **Airtable** stores all structured data.
- A **Custom GPT** reads from and writes to Airtable to guide daily actions and log progress.

## CSV Files / Tables

These four CSV files correspond directly to four Airtable tables.

### 1. MealPlan.csv

Defines a 4-week rotating meal plan.

Columns:
- `Week` — integer (1–4)
- `DayOfWeek` — text (Monday–Sunday)
- `Meal` — Breakfast, Lunch, or Dinner
- `Recipe` — name of the meal (e.g., "Protein Oats", "Chicken Rice Bowl")

This table tells the GPT what meals are planned for a given week and day.

### 2. WorkoutPlan.csv

Defines a 24-week workout schedule, mapped by workout code and progression phase.

Columns:
- `Week` — integer (1–24)
- `DayOfWeek` — text (Monday–Sunday)
- `WorkoutCode` — e.g. StrengthA, StrengthB, StrengthC, Cond1, Cond2, Yoga_Light, Recovery
- `ProgressionPhase` — Phase1_Base, Phase2_Volume, Phase3_Supersets, Phase4_Load, Phase5_CondPlus, Phase6_Peak
- `Notes` — optional free text for that day (left blank in this template)

This table defines **which type of workout** you do each day and which phase of progression you’re in.

### 3. WorkoutDifficulty.csv

Defines how each workout code behaves under each progression phase.

Columns:
- `WorkoutCode`
- `ProgressionPhase`
- `WorkoutName` — human-readable description of the workout
- `ProgressionMultiplier` — a relative load/volume indicator (e.g. 1.0x, 1.3x, 1.6x)
- `ProgressionNotes` — short text explaining how to scale difficulty in that phase

The Custom GPT uses this table to:
- Interpret what each `WorkoutCode` means
- Scale volume and intensity according to `ProgressionMultiplier` and notes

### 4. RecordTemplate.csv

Template for a daily log table (Airtable table name usually `Record`).

Columns:
- `Date`
- `Week`
- `DayOfWeek`
- `PlanWorkoutCode`
- `CompletedWorkout`
- `CompletedBreakfast`
- `CompletedLunch`
- `CompletedDinner`
- `UnderCalorieGoal`
- `HitProteinTarget`
- `NoUnplannedSnacks`
- `Calories`
- `Protein_g`
- `Water_oz`
- `MilesWalked`
- `JumpRope_Min`
- `SleepHours`
- `Stress_1_5`
- `Energy_1_5`
- `Mood_1_5`
- `Weight_lbs`
- `Notes`

The Custom GPT creates records in this table to log what actually happened each day.

## How the System Works (High Level)

1. **Planning**
   - Import these CSVs into Airtable as four tables.
   - Optionally customize meals, workouts, or difficulty notes.

2. **Custom GPT Integration**
   - Configure a Custom GPT with Actions that can:
     - Read from `MealPlan`, `WorkoutPlan`, and `WorkoutDifficulty`
     - Insert new rows into `Record`
   - The GPT:
     - Tells you today’s planned workout and meals
     - Generates step-by-step workout instructions based on `WorkoutCode` and `WorkoutDifficulty`
     - Logs completions and metrics into `Record` when you ask

3. **Daily Use**
   - Ask the GPT: “What’s today’s workout?” or “What’s my meal plan today?”
   - After you complete things, say: “Log that I completed today’s workout and breakfast.”
   - The GPT sends properly structured data back to Airtable.

## Notes

- Checkbox-style fields in Airtable (e.g. `CompletedWorkout`) should be stored as boolean values (true/false) when using the API.
- This project is designed for general wellness and fitness tracking, not for medical data or HIPAA-regulated information.
- You can adjust progression, add new workout codes, or tweak meals at any time in Airtable.

Enjoy building your automated fitness + nutrition workflow!
