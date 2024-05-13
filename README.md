using System;
using System.Collections.Generic;
using System.Linq;
using recipeApplication;

internal class Program
{
    // Class containing application logic and user interface
    class Application
    {
        // Static list to store recipes
        private static readonly List<Recipe> recipes = new List<Recipe>();

        // Main method, entry point of the program
        static void Main(string[] args)
        {
            while (true)
            {
                // Display menu options
                Console.WriteLine("Options:");
                Console.WriteLine("1. Create recipe");
                Console.WriteLine("2. Display recipe list");
                Console.WriteLine("3. Scale ingredients");
                Console.WriteLine("4. Reset ingredients");
                Console.WriteLine("5. Exit");

                string input = Console.ReadLine();

                // Handle user input based on chosen option
                switch (input)
                {
                    case "1":
                        CreateRecipe();
                        break;
                    case "2":
                        DisplayRecipeList();
                        break;
                    case "3":
                        ScaleIngredients();
                        break;
                    case "4":
                        ResetIngredients();
                        break;
                    case "5":
                        Environment.Exit(0);
                        break;
                    default:
                        Console.WriteLine("Invalid choice. Please enter a number between 1 and 5.");
                        break;
                }
            }
        }

        // Method to create a new recipe
        static void CreateRecipe()
        {
            Console.WriteLine("Enter the name of the recipe:");
            string recipeName = Console.ReadLine();

            Recipe recipe = new Recipe { Name = recipeName };
            recipes.Add(recipe);
            Console.WriteLine($"Recipe '{recipeName}' created successfully.");

            AddIngredients(recipe);
            AddSteps(recipe);
        }

        // Method to add ingredients to a recipe
        static void AddIngredients(Recipe recipe)
        {
            Console.WriteLine("Enter the number of ingredients:");
            int numIngredients = int.Parse(Console.ReadLine());

            for (int i = 0; i < numIngredients; i++)
            {
                Ingredient ingredient = new Ingredient();

                Console.WriteLine($"Enter details for ingredient {i + 1}:");
                Console.Write("Name: ");
                ingredient.Name = Console.ReadLine();
                Console.Write("Quantity: ");
                ingredient.Quantity = double.Parse(Console.ReadLine());
                // Store original quantity
                ingredient.OriginalQuantity = ingredient.Quantity;
                Console.Write("Unit: ");
                ingredient.Unit = Console.ReadLine();
                Console.Write("Calories: ");
                ingredient.Calories = int.Parse(Console.ReadLine());
                // Store original calories
                ingredient.OriginalCalories = ingredient.Calories;
                Console.Write("Food Group: ");
                ingredient.FoodGroup = Console.ReadLine();

                recipe.Ingredients.Add(ingredient);
            }
        }

        // Method to add steps to a recipe
        static void AddSteps(Recipe recipe)
        {
            Console.WriteLine("Enter the number of steps:");
            int numSteps = int.Parse(Console.ReadLine());

            for (int i = 0; i < numSteps; i++)
            {
                Step step = new Step();

                Console.WriteLine($"Enter step {i + 1}:");
                step.Description = Console.ReadLine();

                recipe.Steps.Add(step);
            }
        }

        // Method to display the list of recipes
        static void DisplayRecipeList()
        {
            if (recipes.Count == 0)
            {
                Console.WriteLine("No recipes entered.");
                return;
            }

            // Sort recipes alphabetically by name
            recipes.Sort((x, y) => string.Compare(x.Name, y.Name, StringComparison.Ordinal));

            Console.WriteLine("Recipes:");
            for (int i = 0; i < recipes.Count; i++)
            {
                Console.WriteLine($"{i + 1}. {recipes[i].Name}");
            }

            Console.WriteLine("Choose a recipe to display:");
            if (!int.TryParse(Console.ReadLine(), out int choiceIndex) || choiceIndex < 1 || choiceIndex > recipes.Count)
            {
                Console.WriteLine("Invalid choice.");
                return;
            }

            DisplayRecipe(recipes[choiceIndex - 1]);
        }

        // Method to display the details of a specific recipe
        static void DisplayRecipe(Recipe recipe)
        {
            Console.WriteLine($"Recipe: {recipe.Name}");
            Console.WriteLine("Ingredients:");
            foreach (var ingredient in recipe.Ingredients)
            {
                Console.WriteLine($"{ingredient.Name}: {ingredient.Quantity} {ingredient.Unit} - {ingredient.Calories} Calories");
            }
            Console.WriteLine("Steps:");
            foreach (var step in recipe.Steps)
            {
                Console.WriteLine(step.Description);
            }
            int totalCalories = recipe.TotalCalories();
            Console.WriteLine($"Total Calories: {totalCalories}");

            if (totalCalories > 300)
            {
                Console.WriteLine("Warning: This recipe exceeds 300 calories.");
            }
        }

        // Method to scale ingredients of a recipe by a given factor
        static void ScaleIngredients()
        {
            Console.WriteLine("Choose a recipe to scale ingredients:");
            if (!int.TryParse(Console.ReadLine(), out int choiceIndex) || choiceIndex < 1 || choiceIndex > recipes.Count)
            {
                Console.WriteLine("Invalid choice.");
                return;
            }

            Console.WriteLine("Choose a scale factor (0.5 for half, 2 for double, 3 for triple):");
            if (!double.TryParse(Console.ReadLine(), out double scale) || scale <= 0)
            {
                Console.WriteLine("Invalid scale factor.");
                return;
            }

            Recipe selectedRecipe = recipes[choiceIndex - 1];
            ScaleIngredients(selectedRecipe, scale);
            Console.WriteLine("Ingredients scaled successfully.");
        }

        // Method to scale ingredients of a specific recipe by a given factor
        static void ScaleIngredients(Recipe recipe, double scale)
        {
            foreach (var ingredient in recipe.Ingredients)
            {
                ingredient.Quantity *= scale;
                ingredient.Calories = (int)Math.Round(ingredient.Calories * scale);
            }
        }

        // Method to reset ingredients of a specific recipe to their original values
        static void ResetIngredients()
        {
            Console.WriteLine("Choose a recipe to reset ingredients:");
            if (!int.TryParse(Console.ReadLine(), out int choiceIndex) || choiceIndex < 1 || choiceIndex > recipes.Count)
            {
                Console.WriteLine("Invalid choice.");
                return;
            }

            Recipe selectedRecipe = recipes[choiceIndex - 1];
            foreach (var ingredient in selectedRecipe.Ingredients)
            {
                // Reset quantity and calories to original values
                ingredient.Quantity = ingredient.OriginalQuantity;
                ingredient.Calories = ingredient.OriginalCalories;
            }
            Console.WriteLine("Ingredients reset successfully.");
        }
    }
}

[CLASSES]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace recipeApplication
{

    // Class representing an Ingredient with its properties
    internal class Ingredient
    {
        public string Name { get; set; }
        public double Quantity { get; set; }
        public string Unit { get; set; }
        public int Calories { get; set; }
        public string FoodGroup { get; set; }
        // Additional properties to store original quantity and calories for scaling and resetting
        public double OriginalQuantity { get; set; }
        public int OriginalCalories { get; set; }
    }
}



using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace recipeApplication
{
    // Class representing a Recipe with its properties and methods
    internal class Recipe
    {
        public string Name { get; set; }
        public List<Ingredient> Ingredients { get; set; }
        public List<Step> Steps { get; set; }

        // Constructor initializes ingredient and step lists
        public Recipe()
        {
            Ingredients = new List<Ingredient>();
            Steps = new List<Step>();
        }

        // Method to calculate the total calories of all ingredients in the recipe
        public int TotalCalories()
        {
            return Ingredients.Sum(ingredient => ingredient.Calories);
        }
    }
}



using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace recipeApplication
{
    // Class representing a step in a recipe
    internal class Step
    {
        public string Description { get; set; }
    }
}
