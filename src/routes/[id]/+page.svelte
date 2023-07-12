<script>
    import { page } from "$app/stores";

    let id = $page.params.id;

    let promise = fetchRecipeDetails();

    async function fetchRecipeDetails() {
		const res = await fetch(`https://www.themealdb.com/api/json/v1/1/lookup.php?i=${id}`);
		const data = await res.json();

		if (res.ok) {
			return data;
		} else {
			throw new Error(data);
		}
	}

</script>

<main>
    {#await promise}
        <p>Loading...</p>
    {:then obj}
        {#if obj.meals}
            {#each obj.meals as meal}
                <div class="header-content">
                    <div class="header-text">
                        <h2>{meal.strMeal}</h2>
                        <p>See full recipe on: <a href={meal.strSource}>BBC Good Food</a></p>
                        <p><a href={meal.strYoutube}>Watch Video</a></p>
                    </div>
                    <img src={meal.strMealThumb} alt={meal.strMeal} width="300" height="200">
                </div>
                <h4>Ingredients</h4>
                <ul>
                    {#each Array(20) as num, i} 
                        {#if meal[`strIngredient${i+1}`]}
                            <li>{meal[`strMeasure${i+1}`]} {meal[`strIngredient${i+1}`]}</li>
                        {/if}
                    {/each}
                </ul>
                <h4>Instructions</h4>
                <p class="instructions">{@html (meal.strInstructions).replaceAll('\r\n', '<br />')}</p>
            {/each}
        {:else}
            <p>No results found!</p>
        {/if}
    {:catch error}
        <p style="color: red">Something went wrong, Try again!</p>
    {/await}
</main>

<style>
    main{
        width: 55%;
        margin: 30px auto;
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
        border-radius: 6px;
        padding-bottom: 20px;
    }
    img{
        width: 45%;
        height: auto;
        border-top-right-radius: 6px;
    }
    .header-content{
        display: flex;
        width: 100%;
        box-shadow: rgba(0, 0, 0, 0.30) 0px 2px 10px;
    }
    .header-text{
        margin-left: 20px;
        width: 70%;
        display: flex;
        flex-direction: column;
    }
    h2{
        text-align: center;
        margin-top: 80px;
        margin-bottom: 15px;
    }
    .header-text p{
        text-align: center;
        margin-bottom: 20px;
    }
    ul li{
        list-style: none;
    }
    ul{
        padding: 0;
        margin-left: 40px;
    }
    h4{
        margin-top: 40px;
        margin-bottom: 20px;
        margin-left: 30px;
    }
    .instructions{
        margin-left: 40px;
    }
    
</style>