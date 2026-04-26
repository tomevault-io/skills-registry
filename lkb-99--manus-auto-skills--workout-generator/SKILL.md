---
name: workout-generator
description: Generate personalized workout plans with progression and tracking. Use this skill when users want to create a workout routine, training program, or exercise plan. Triggers: workout, training, exercise, fitness plan, gym routine, treino, academia, plano de treino, rotina de exercícios. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Workout Generator

## Overview
This skill is designed to assist users in creating personalized workout routines. It takes into account the user's fitness level, goals, available equipment, and preferences to generate a comprehensive and effective training program. The skill also incorporates principles of progressive overload and provides a framework for tracking progress over time, ensuring continuous improvement and adaptation.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: workout, training, exercise, fitness, gym, routine, plan, program, treino, academia, exercício, rotina, plano, programa
- Phrases: "create a workout plan", "make a gym routine", "generate a training program", "montar um treino", "criar uma rotina de exercícios", "plano de academia"
- Context: Any discussion about creating or structuring an exercise or fitness schedule.

**Example user queries that trigger this skill:**
- "I want to start working out, can you create a plan for me?"
- "Preciso de um treino para a academia, 3 vezes por semana."
- "How can I generate a push/pull/legs routine?"
- "Monte uma rotina de exercícios para fazer em casa."

## When to Use This Skill

**ALWAYS use this skill when the user wants to:**
- Create a new workout plan from scratch.
- Generate a structured training program based on their goals (e.g., muscle gain, fat loss).
- Get a personalized exercise routine for home or the gym.
- Find a workout split (e.g., Full Body, Upper/Lower, PPL).
- Receive a plan that includes progression and tracking.

## Core Capabilities

### 1. Personalized Workout Generation
The core of this skill is its ability to generate workout plans tailored to the individual. This is achieved by gathering information on the following parameters:

*   **Fitness Level:** Beginner, Intermediate, or Advanced.
*   **Primary Goal:** Muscle Gain (Hypertrophy), Strength Gain, Fat Loss, or General Fitness.
*   **Training Frequency:** Number of days per week the user can train.
*   **Training Split:** The preferred way to split workouts (e.g., Full Body, Upper/Lower, Push/Pull/Legs).
*   **Available Equipment:** A list of equipment the user has access to (e.g., dumbbells, barbells, resistance bands, bodyweight).
*   **Exercise Preferences:** Any specific exercises the user enjoys or wants to include.
*   **Exercise Exclusions:** Any exercises the user wants to avoid due to injury or preference.

### 2. Progressive Overload Implementation
The skill incorporates the principle of progressive overload, which is crucial for long-term progress. It provides a clear progression path for each exercise in the workout plan. This can be achieved through various methods:

*   **Increasing Weight:** Gradually increasing the weight lifted for a given number of sets and reps.
*   **Increasing Reps:** Increasing the number of repetitions performed with the same weight.
*   **Increasing Sets:** Increasing the number of sets performed for an exercise.
*   **Decreasing Rest Time:** Reducing the rest periods between sets.
*   **Improving Form:** Focusing on better execution of the exercise.

### 3. Workout Tracking and Logging
To ensure progress is being made, the skill provides a template for tracking workouts. This allows users to log the following data for each session:

*   **Date:** The date of the workout.
*   **Workout:** The name of the workout performed (e.g., "Push Day A").
*   **Exercise:** The name of the exercise.
*   **Sets:** The number of sets performed.
*   **Reps:** The number of reps performed in each set.
*   **Weight:** The weight used for each set.
*   **RPE (Rate of Perceived Exertion):** A subjective measure of how difficult the set was on a scale of 1-10.

## Step-by-Step Workflow

1.  **Gather User Information:** Start by asking the user for the necessary information to create their workout plan. Use a clear and concise set of questions.

2.  **Select a Training Split:** Based on the user's training frequency and preferences, choose an appropriate training split.

3.  **Choose Exercises:** Select exercises for each workout day based on the chosen split, available equipment, and user preferences. Ensure a balanced selection of compound and isolation exercises.

4.  **Set Volume (Sets and Reps):** Determine the number of sets and reps for each exercise based on the user's goal.

5.  **Define Progression:** Specify the progression model for each exercise.

6.  **Provide a Tracking Template:** Offer a template for the user to log their workouts.

## Best Practices

*   **Prioritize Compound Lifts:** Base the workout around compound exercises (e.g., squats, deadlifts, bench press, overhead press, rows) as they provide the most bang for your buck.
*   **Include a Warm-up and Cool-down:** Always recommend a proper warm-up before each workout and a cool-down afterward.
*   **Listen to Your Body:** Advise users to pay attention to their bodies and not to push through pain.
*   **Be Consistent:** Emphasize the importance of consistency for achieving results.
*   **Focus on Form:** Remind users that proper form is more important than lifting heavy weight.
*   **Adjust as Needed:** Encourage users to adjust their workout plan as they progress and their goals change.

## Examples

### Example 1: Beginner Full Body Workout (3 days/week)

**User Profile:**
*   **Fitness Level:** Beginner
*   **Goal:** General Fitness
*   **Training Frequency:** 3 days/week
*   **Available Equipment:** Dumbbells, Resistance Bands, Bodyweight

**Workout Plan:**

**Workout A**
| Exercise | Sets | Reps | Rest (seconds) |
| :--- | :--- | :--- | :--- |
| Goblet Squat | 3 | 8-12 | 60-90 |
| Push-ups (or Knee Push-ups) | 3 | As many as possible | 60 |
| Dumbbell Rows | 3 | 8-12 per side | 60 |
| Plank | 3 | 30-60 seconds | 60 |
| Bicep Curls | 2 | 10-15 | 45 |

**Workout B**
| Exercise | Sets | Reps | Rest (seconds) |
| :--- | :--- | :--- | :--- |
| Bodyweight Lunges | 3 | 10-15 per side | 60 |
| Dumbbell Overhead Press | 3 | 8-12 | 60-90 |
| Band Pull-Aparts | 3 | 15-20 | 45 |
| Glute Bridges | 3 | 12-15 | 60 |
| Tricep Extensions (with bands) | 2 | 10-15 | 45 |

**Progression:**
*   Start with a weight that allows you to complete the target rep range with good form.
*   Once you can complete all sets and reps for an exercise, increase the weight in the next session.

### Example 2: Intermediate Push/Pull/Legs Workout (6 days/week)

**User Profile:**
*   **Fitness Level:** Intermediate
*   **Goal:** Muscle Gain (Hypertrophy)
*   **Training Frequency:** 6 days/week
*   **Available Equipment:** Full Gym

**Workout Plan:**

**Push A**
| Exercise | Sets | Reps | Rest (seconds) |
| :--- | :--- | :--- | :--- |
| Barbell Bench Press | 4 | 6-8 | 90-120 |
| Incline Dumbbell Press | 3 | 8-12 | 60-90 |
| Seated Dumbbell Shoulder Press | 3 | 8-12 | 60-90 |
| Cable Lateral Raises | 3 | 12-15 | 45-60 |
| Tricep Pushdowns | 3 | 10-15 | 45-60 |

**Pull A**
| Exercise | Sets | Reps | Rest (seconds) |
| :--- | :--- | :--- | :--- |
| Deadlifts | 3 | 4-6 | 120-180 |
| Pull-ups (or Lat Pulldowns) | 4 | 6-10 | 90-120 |
| Barbell Rows | 3 | 8-12 | 60-90 |
| Face Pulls | 3 | 15-20 | 45-60 |
| Dumbbell Bicep Curls | 3 | 10-15 | 45-60 |

**Legs A**
| Exercise | Sets | Reps | Rest (seconds) |
| :--- | :--- | :--- | :--- |
| Barbell Back Squats | 4 | 6-8 | 120-180 |
| Romanian Deadlifts | 3 | 8-12 | 90-120 |
| Leg Press | 3 | 10-15 | 60-90 |
| Leg Curls | 3 | 12-15 | 45-60 |
| Calf Raises | 4 | 15-20 | 30-45 |

**Push B, Pull B, Legs B:** Similar structure with different exercise variations.

**Progression:**
*   **Double Progression:** For each exercise, work within the given rep range. Once you can hit the top of the rep range for all sets, increase the weight.

## Templates

### Workout Logging Template (Markdown)

```markdown
**Date:** YYYY-MM-DD
**Workout:** Push Day A

| Exercise | Set 1 | Set 2 | Set 3 | Set 4 |
| :--- | :--- | :--- | :--- | :--- |
| Barbell Bench Press | 8 reps @ 100kg | 7 reps @ 100kg | 6 reps @ 100kg | 6 reps @ 100kg |
| Incline Dumbbell Press | 12 reps @ 30kg | 11 reps @ 30kg | 10 reps @ 30kg | - |
| ... | ... | ... | ... | ... |
```

## References

*   [How To Build Your Own Workout Routine (Plans & Exercises)](https://www.nerdfitness.com/blog/how-to-build-your-own-workout-routine/)
*   [Progressive Overload Explained: Grow Muscle & Strength Today](https://blog.nasm.org/progressive-overload-explained)
*   [Hacking the Workout Journal: How to Track Your Workouts](https://jamesclear.com/workout-journal)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
