---
name: nutrition-planner
description: Create personalized nutrition plans, calculate calories/macros, and generate meal ideas. Use this skill for diet planning, weight management, or muscle gain. Triggers: nutrition plan, diet plan, meal plan, calories, macros, recipes, plano alimentar, dieta, calorias, macronutrientes, receitas, emagrecer, ganhar massa. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Nutrition Planner

## Overview

The Nutrition Planner skill is a comprehensive tool designed to assist users in creating personalized nutrition plans tailored to their specific goals, preferences, and physiological data. It automates the calculation of essential metrics like Basal Metabolic Rate (BMR) and Total Daily Energy Expenditure (TDEE), determines optimal macronutrient splits, and generates structured meal plans complete with recipe ideas and grocery lists. This skill acts as a personal nutrition assistant, simplifying the complex process of diet planning and empowering users to take control of their health and wellness journey.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: nutrition plan, diet plan, meal plan, calorie calculation, macronutrients, healthy eating, weight loss diet, muscle gain diet, plano alimentar, plano nutricional, dieta, calcular calorias, macronutrientes, receitas, emagrecer, ganhar massa.
- Phrases: "create a meal plan", "calculate my calories", "I need a diet for weight loss", "what should I eat to gain muscle", "criar um plano alimentar", "calcular minhas calorias", "preciso de uma dieta para emagrecer".
- Context: Any discussion about creating structured eating plans, calculating nutritional needs, or planning meals for specific health or fitness goals.

**Example user queries that trigger this skill:**
- "I want a nutrition plan to lose 10 pounds."
- "Can you create a 7-day meal plan for me?"
- "Quero uma dieta para ganhar massa muscular."
- "Como posso calcular minhas necessidades de calorias e macros?"

## When to Use This Skill

This skill is particularly useful in the following scenarios:

-   **Weight Management:** When a user wants to lose, gain, or maintain weight and needs a structured plan with specific calorie and macro targets.
-   **Muscle Gain:** For individuals looking to build muscle mass who require a diet plan with adequate protein and a calibrated caloric surplus.
-   **Healthy Eating:** For users who want to improve their overall diet, eat more balanced meals, and understand their nutritional needs better.
-   **Specific Dietary Needs:** When a user follows a particular diet (e.g., vegan, vegetarian, keto, paleo) and needs help creating a balanced plan that adheres to those restrictions.
-   **Meal Prepping:** To generate a full week's worth of meals and a consolidated grocery list to streamline the meal prep process.
-   **Performance Nutrition:** For athletes or highly active individuals who need to optimize their diet for performance and recovery.

## Core Capabilities

### 1. Nutritional Needs Assessment

The skill first gathers essential user data to perform a detailed nutritional assessment. This forms the foundation of the personalized plan.

-   **Basal Metabolic Rate (BMR) Calculation:** The skill uses the Mifflin-St Jeor equation, a widely accepted formula, to estimate the number of calories the user's body burns at rest.
    -   **For Men:** `BMR = (10 * weight in kg) + (6.25 * height in cm) - (5 * age in years) + 5`
    -   **For Women:** `BMR = (10 * weight in kg) + (6.25 * height in cm) - (5 * age in years) - 161`

-   **Total Daily Energy Expenditure (TDEE) Calculation:** Based on the BMR, the skill calculates the TDEE by factoring in the user's activity level. This represents the total calories burned in a day.

| Activity Level                | Multiplier |
| ----------------------------- | ---------- |
| Sedentary (little to no exercise) | 1.2        |
| Lightly Active (1-2 days/week)  | 1.375      |
| Moderately Active (3-5 days/week) | 1.55       |
| Very Active (6-7 days/week)     | 1.725      |
| Extra Active (hard daily exercise) | 1.9        |

-   **Goal-Oriented Calorie Adjustment:** The skill adjusts the TDEE to create a caloric deficit for weight loss (typically -500 calories/day) or a caloric surplus for muscle gain (typically +300-500 calories/day).

-   **Macronutrient Splitting:** The skill recommends a macronutrient (protein, carbs, fat) split based on the user's goals. These are flexible and can be adjusted.
    -   **Weight Loss (Balanced):** 40% Carbs, 30% Protein, 30% Fat
    -   **Muscle Gain:** 40% Carbs, 35% Protein, 25% Fat
    -   **Maintenance:** 50% Carbs, 25% Protein, 25% Fat
    -   **Ketogenic:** 5-10% Carbs, 70-80% Fat, 10-20% Protein

### 2. Personalized Meal Plan Generation

Using the calculated targets, the skill generates a detailed meal plan. The user can specify the number of days and meals per day.

-   **Customization:** The plan can be tailored to dietary restrictions (e.g., gluten-free, dairy-free), allergies, and food preferences.
-   **Structured Output:** The skill presents the meal plan in a clear, organized table format, showing each meal (Breakfast, Lunch, Dinner, Snacks) with food items, quantities, and estimated macronutrient/calorie counts.

### 3. Recipe and Grocery List Creation

To make the plan actionable, the skill provides recipes and a shopping list.

-   **Recipe Suggestions:** For each meal in the plan, the skill can provide simple, easy-to-follow recipes. It can use the `Browser` tool to search for specific recipes online if needed.
-   **Consolidated Grocery List:** The skill compiles all ingredients from the meal plan into a single, organized grocery list, categorized by aisle (e.g., Produce, Protein, Dairy, Pantry) to simplify shopping.

## Step-by-Step Workflow

1.  **Initiation:** The user starts by stating their goal. For example, "I want a nutrition plan to lose 10 pounds."
2.  **Data Collection:** The skill prompts the user for necessary information:
    -   `Please provide your age, gender, weight (in kg or lbs), height (in cm or ft/in), and activity level (Sedentary, Lightly Active, Moderately Active, Very Active, Extra Active).`
3.  **Goal Confirmation:** The skill confirms the primary goal (e.g., weight loss, muscle gain, maintenance) and any secondary goals.
4.  **Dietary Preferences:** The skill asks for any dietary restrictions, allergies, or strong food dislikes.
    -   `Do you have any dietary restrictions, such as vegan, vegetarian, or gluten-free? Are there any foods you dislike?`
5.  **Plan Generation:** The skill calculates the BMR, TDEE, and target macros, then presents this summary to the user for approval.
6.  **Meal Plan Request:** The user requests a meal plan for a specific duration.
    -   `Generate a 7-day meal plan based on these targets.`
7.  **Output Delivery:** The skill delivers the full meal plan in a Markdown table. It then asks if the user wants recipes or a grocery list.
    -   `Here is your 7-day meal plan. Would you like recipes for these meals or a consolidated grocery list?`
8.  **Follow-up Actions:** The user can request recipes, the grocery list, or modifications to the plan.
    -   `Give me the recipe for the 'Chicken and Quinoa Bowl' and create a grocery list for the first 3 days.`

## Best Practices

-   **Provide Accurate Data:** The quality of the plan depends on the accuracy of the initial data. Encourage users to provide the most precise information possible.
-   **Encourage Professional Consultation:** Always include a disclaimer that the skill is not a substitute for professional medical or nutritional advice, especially for users with pre-existing health conditions.
-   **Leverage Browser for Variety:** Use the `Browser` tool to find a wide variety of recipes to keep the meal plan interesting and prevent dietary fatigue.
-   **Start Small:** For users new to meal planning, suggest starting with a 3-day plan to make it less overwhelming.
-   **Incorporate Flexibility:** Remind users that the plan is a guideline, not a rigid set of rules. Small deviations are acceptable and can improve long-term adherence.
-   **Save Plans to Files:** Use the `Write` tool to save the generated meal plans and grocery lists to `.md` files for easy access and reference.

## Examples

### Example 1: Initial Setup for Weight Loss

**User Prompt:**
"I want to create a nutrition plan. My goal is to lose about 15 pounds. I am a 30-year-old male, 5'10" (178 cm), 190 lbs (86 kg), and I work a desk job but go to the gym 3 times a week."

**Agent's Thought Process:**
1.  Identify the goal: Weight Loss.
2.  Extract user data: Age=30, Gender=Male, Height=178cm, Weight=86kg, Activity=Moderately Active.
3.  Calculate BMR: `(10 * 86) + (6.25 * 178) - (5 * 30) + 5 = 860 + 1112.5 - 150 + 5 = 1827.5`
4.  Calculate TDEE: `1827.5 * 1.55 = 2832.6`
5.  Apply weight loss deficit: `2832.6 - 500 = 2332.6`
6.  Set macros (40C/30P/30F): Carbs=233g, Protein=175g, Fat=78g.
7.  Present the summary and ask for the next step.

**Agent's Response:**
"Based on your information, here is your estimated daily nutritional target for weight loss:

-   **Calories:** ~2,330 kcal/day
-   **Protein:** ~175g
-   **Carbohydrates:** ~233g
-   **Fat:** ~78g

Would you like me to generate a meal plan based on these targets? Please let me know if you have any dietary restrictions."

### Example 2: Generating a 3-Day Vegan Meal Plan

**User Prompt:**
"Yes, please generate a 3-day vegan meal plan. I don't like mushrooms."

**Agent's Thought Process:**
1.  Goal: Generate a 3-day meal plan.
2.  Constraint 1: Vegan.
3.  Constraint 2: No mushrooms.
4.  Structure a Markdown table for 3 days with 3-4 meals per day.
5.  Populate the table with appropriate vegan meals, ensuring variety and avoiding mushrooms.
6.  Use placeholder calorie/macro counts or calculate them approximately.

**Agent's Response:**
"Here is a 3-day vegan meal plan designed for you. All meals are mushroom-free.

| Day       | Breakfast                               | Lunch                                  | Dinner                                     | Snack(s)                 |
| :-------- | :-------------------------------------- | :------------------------------------- | :----------------------------------------- | :----------------------- |
| **Day 1** | Oatmeal with berries and almond butter  | Lentil soup with a side of whole-grain bread | Tofu stir-fry with brown rice and vegetables | Apple with peanut butter |
| **Day 2** | Tofu scramble with spinach and toast    | Quinoa salad with black beans and corn | Chickpea curry with basmati rice           | A handful of almonds     |
| **Day 3** | Smoothie with banana, spinach, and protein powder | Black bean burgers on a whole-wheat bun | Pasta with a tomato and vegetable sauce    | Hummus with carrot sticks|

Would you like recipes for any of these meals or a grocery list for this 3-day plan?"

## References

-   [TDEE Calculator](https://tdeecalculator.net/)
-   [Healthline: Best Macronutrient Ratio](https://www.healthline.com/nutrition/best-macronutrient-ratio)
-   [Mayo Clinic Calorie Calculator](https://www.mayoclinic.org/healthy-lifestyle/weight-loss/in-depth/calorie-calculator/itt-20402304)
-   [PubMed - Mifflin-St Jeor Equation](https://pubmed.ncbi.nlm.nih.gov/15883556/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
