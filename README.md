# Recipe-Sharing
The "Find Meal For Your Ingredients" project is a recipe search application that allows users to input an ingredient and discover meals using the MealDB API.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Find Meal For Your Ingredients</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css" integrity="sha512-+4zCK9k+qNFUR5X+cKL9EIR+ZOhtIloNl9GIKS57V1MyNsYpYcUrUeQc9vNfzsWfV28IaLL3i96P9sdNyeRssA==" crossorigin="anonymous" />
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <div class="meal-wrapper">
      <div class="meal-search">
        <h2 class="title">Find Meals For Your Ingredients</h2>
        <blockquote>Real food doesn't have ingredients; real food is ingredients.<br>
          <cite>- Jamie Oliver</cite>
        </blockquote>

        <div class="meal-search-box">
          <input type="text" class="search-control" placeholder="Enter an ingredient" id="search-input">
          <button type="submit" class="search-btn btn" id="search-btn">
            <i class="fas fa-search"></i>
          </button>
        </div>
      </div>

      <div class="meal-result">
        <h2 class="title">Your Search Results:</h2>
        <div id="meal"></div>
      </div>

      <div class="meal-details">
        <button type="button" class="btn recipe-close-btn" id="recipe-close-btn">
          <i class="fas fa-times"></i>
        </button>
        <div class="meal-details-content"></div>
      </div>
    </div>
  </div>

  <script>
    const searchBtn = document.getElementById('search-btn');
    const mealList = document.getElementById('meal');
    const mealDetailsContent = document.querySelector('.meal-details-content');
    const recipeCloseBtn = document.getElementById('recipe-close-btn');

    // Event listeners
    searchBtn.addEventListener('click', getMealList);
    mealList.addEventListener('click', getMealRecipe);
    recipeCloseBtn.addEventListener('click', () => {
        mealDetailsContent.parentElement.classList.remove('showRecipe');
    });

    // Get meal list that matches with the ingredients
    function getMealList() {
        let searchInputTxt = document.getElementById('search-input').value.trim();
        fetch(`https://www.themealdb.com/api/json/v1/1/filter.php?i=${searchInputTxt}`)
            .then(response => response.json())
            .then(data => {
                let html = "";
                if (data.meals) {
                    data.meals.forEach(meal => {
                        html += `
                            <div class="meal-item" data-id="${meal.idMeal}">
                                <div class="meal-img">
                                    <img src="${meal.strMealThumb}" alt="food">
                                </div>
                                <div class="meal-name">
                                    <h3>${meal.strMeal}</h3>
                                    <a href="#" class="recipe-btn">Get Recipe</a>
                                </div>
                            </div>
                        `;
                    });
                    mealList.classList.remove('notFound');
                } else {
                    html = "Sorry, we didn't find any meal!";
                    mealList.classList.add('notFound');
                }
                mealList.innerHTML = html;
            });
    }

    // Get recipe of the meal
    function getMealRecipe(e) {
        e.preventDefault();
        if (e.target.classList.contains('recipe-btn')) {
            let mealItem = e.target.parentElement.parentElement;
            fetch(`https://www.themealdb.com/api/json/v1/1/lookup.php?i=${mealItem.dataset.id}`)
                .then(response => response.json())
                .then(data => mealRecipeModal(data.meals));
        }
    }

    // Create a modal
    function mealRecipeModal(meal) {
        meal = meal[0];
        let html = `
            <h2 class="recipe-title">${meal.strMeal}</h2>
            <p class="recipe-category">${meal.strCategory}</p>
            <div class="recipe-instruct">
                <h3>Instructions:</h3>
                <p>${meal.strInstructions}</p>
            </div>
            <div class="recipe-meal-img">
                <img src="${meal.strMealThumb}" alt="">
            </div>
            <div class="recipe-link">
                <a href="${meal.strYoutube}" target="_blank">Watch Video</a>
            </div>
        `;
        mealDetailsContent.innerHTML = html;
        mealDetailsContent.parentElement.classList.add('showRecipe');
    }
  </script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;400;500;600;700;800;900&display=swap');

    * {
        padding: 0;
        margin: 0;
        box-sizing: border-box;
    }
    :root {
        --tenne-tawny: #d65108;
        --tenne-tawny-dark: #b54507;
    }
    body {
        font-family: 'Poppins', sans-serif;
        font-weight: 300;
        font-size: 1.05rem;
        line-height: 1.6;
    }
    .btn {
        font-family: inherit;
        cursor: pointer;
        font-size: 1.05rem;
    }
    .container {
        min-height: 100vh;
    }
    .meal-wrapper {
        max-width: 1280px;
        margin: 0 auto;
        padding: 2rem;
        background: #fff;
        text-align: center;
    }
    .meal-search-box {
        display: flex;
        margin: 1.2rem auto;
    }
    .search-control {
        flex: 1;
        padding: 0 1rem;
        border: 1px solid var(--tenne-tawny);
        border-radius: 2rem 0 0 2rem;
        outline: none;
        font-size: 1.1rem;
        color: var(--tenne-tawny);
    }
    .search-control::placeholder {
        color: var(--tenne-tawny);
    }
    .search-btn {
        width: 55px;
        height: 55px;
        background: var(--tenne-tawny);
        color: #fff;
        border: none;
        border-radius: 0 2rem 2rem 0;
    }
    .meal-result {
        margin-top: 2rem;
    }
    .meal-item {
        border-radius: 1rem;
        box-shadow: 0 4px 21px -12px rgba(0, 0, 0, 0.79);
        overflow: hidden;
        margin: 1rem 0;
    }
    .meal-img img {
        width: 100%;
    }
    .recipe-btn {
        display: block;
        text-decoration: none;
        background: var(--tenne-tawny);
        color: #fff;
        padding: 0.75rem 1rem;
        border-radius: 2rem;
        margin: 1rem auto;
        width: 150px;
    }
    .meal-details {
        display: none;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: var(--tenne-tawny);
        color: #fff;
        border-radius: 1rem;
        width: 90%;
        max-width: 700px;
        padding: 2rem;
    }
    .showRecipe {
        display: block;
    }
    .recipe-close-btn {
        position: absolute;
        top: 1rem;
        right: 1rem;
        background: #fff;
        border: none;
        border-radius: 50%;
        font-size: 1.2rem;
        cursor: pointer;
    }
  </style>
</body>
</html>
