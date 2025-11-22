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


## Getting Started

Follow these steps to set up your automated Fitness & Nutrition system:

### 1. Import the CSV files into Airtable
Create a new Airtable base with four tables:
- **MealPlan**
- **WorkoutPlan**
- **WorkoutDifficulty**
- **Record**

Use the CSV import feature to load each CSV file into its corresponding table.

### 2. Create a Personal Access Token (PAT)
Go to:
https://airtable.com/create/tokens
- Create a new token
- Grant access to your new base
- Enable `data.records:read` and `data.records:write`

Copy the token.

### 3. Set up your Custom GPT
In ChatGPT → **Build a GPT**:
- Add an **Action**
- Import the `airtable_fitness_schema_template.json` file
- Set API authentication to:
  - Type: API Key
  - Header name: `Authorization`
  - Value: `Bearer YOUR_TOKEN`

### 4. Upload your custom GPT instructions and privacy policy
- Paste the contents of `custom_gpt_config.txt` into the Instructions field
- Add your privacy policy link

### 5. Start using it
Ask things like:
- “What’s today's workout?”
- “What’s my meal plan today?”
- “Log that I completed today’s workout and hit my protein target.”

Your GPT will read from Airtable and log data whenever you request.

Enjoy your automated training system!


## Architecture Overview

```text
+-------------------------+
|       Custom GPT        |
|  (Fitness Assistant)    |
+-----------+-------------+
            |
            | Airtable API (via Actions)
            v
+-------------------------+
|        Airtable         |
|-------------------------|
| MealPlan        (data/) |
| WorkoutPlan     (data/) |
| WorkoutDifficulty(data/)|
| Record          (data/) |
+-------------------------+

Support files in this repo:
- README.md                      -> Project overview and setup
- data/MealPlan.csv              -> 4-week rotating meal plan
- data/WorkoutPlan.csv           -> 24-week workout schedule
- data/WorkoutDifficulty.csv     -> Workout scaling rules
- data/RecordTemplate.csv        -> Daily logging template
- gpt/custom_gpt_config.txt      -> GPT instructions + privacy wording
- schema/airtable_fitness_schema_template.json -> OpenAPI schema for Actions
```

## Example Prompts

Once everything is wired up, you can say things like:

- "What is today's workout?"
- "What is my meal plan for today?"
- "Log that I completed today's workout and breakfast."
- "Log that I walked 2 miles, did 15 minutes of jump rope, and stayed under my calorie goal."
- "StrengthB feels too easy. Increase the difficulty for this phase."
- "Show me what my workout will be next Monday."
