# Design for ror-nom

## Overview
A multi-user application. A user only has one pantry. They want to use as many ingredients in their pantry in a meal plan as possible.

### Details
Ingredients are stored by their pantry quantities and can be used in partial quantities. The users are created directly in Rails, and sign in with their username and no password. Ingredients are associated with users via a Rich Join, so that the quantity can be stored per-user. Recipes are associated with users via a simple join. Pantry is a user’s list of ingredients.  Recipes can be shared between users. There are no private recipes. But pantries are private. And MealPlans are too. 

Wet/dry measurements can be converted between each other, but new associations between two measurement types cannot be created. 

## Architecture

### Models
* User username:string references:recipes references:ingredients_to_users
* Ingredient name:string defaultUnit:MeasurementUnit references:ingredients_to_users
* IngredientOwnership quantity:float unit:MeasurementUnit references:users references:ingredients
* IngredientUsage quantity:float unit:MeasurementUnit
* Recipe name:string ingredients:IngredientUsage[] references:users 
* MealPlan recipes:Recipe[] references:users

### Objects
* MeasurementUnit name:string kind:[wet, dry] multiplier:float

### Controllers
* MealPlan index show new edit delete
* User index show new edit delete
* Access new delete
* Ingredient index show new edit delete

### Associations
* User has many MealPlans
* MealPlan belongs to User
* IngredientOwnerships belongs to User
* User has many IngredientOwnerships
* Ingredients has many IngredientOwnerships
* IngredientOwnerships belongs to Ingredient 
* Users HMABT Recipes
* Recipes HMABT Users
* Recipe has many IngredientUsages
* IngredientUsage belongs to Recipe
* IngredientUsage belongs to Ingredient
* Ingredient has many IngredientUsages

### Example of Base Tables
Users
* Ben
* Baron
* Bean

Ingredients
* Parsnips, oz
* Quail eggs, dozens
* Rutebega, lbs

Recipes
* Saladette
* Deviled Eggs

### Example of Simple Joins

RecipesToUsers
* Saladette, baron
* Deviled Eggs, baron
* Deviled Eggs, ben

### Example of Rich Joins
Each ownership is associated with one user only. Each user is associated with multiple ownerships. 

IngredientOwnerships
* Parsnips, ben, 1, oz
* Quail eggs, ben, 1, dozen
* Rutebega, bean, 1, lb
* Rutebega, baron, 0.5, lb
* Quail eggs, baron, 2, dozen

IngredientUsages
* Saladette, Parsnips, 0.5, oz
* Saladette, Rutebega, 0.5, lb
* Deviled eggs, Quail eggs, 0.5, dozen
* Deviled eggs, Rutebega, 0.25, lb



