<script>
// @ts-nocheck

  import { tick } from "svelte";

    $: search = "";

    let promise = fetchRecipes();

    async function fetchRecipes() {
        await tick();
        const res = await fetch(`https://www.themealdb.com/api/json/v1/1/filter.php?i=${search}`);
        const data = await res.json();

        if (res.ok) {
            return data;
        } else {
            throw new Error(data);
        }
    }


    const handleSubmit = () => {
        promise = fetchRecipes();
    }

    const toogleMode = () => {
        let icon = document.getElementById("mode-icon");
        let html = document.documentElement;
        if (html.getAttribute("data-theme") == "dark") {    
            html.setAttribute("data-theme", "light")
            icon.classList.remove("fa-sun");
            icon.classList.add("fa-moon");
            icon.style.color = '#000';
        }else{
            html.setAttribute("data-theme", "dark")
            icon.classList.remove("fa-moon");
            icon.classList.add("fa-sun");
            icon.style.color = '#fff';
        }
    }
</script>

<h1>Find meals for your ingredients</h1><i class="fa-regular fa-sun mode-icon" id="mode-icon" role="none" on:click={toogleMode}></i>
<p>Real food doesn't have ingredients, real food is ingredients.
<span class="quote-author">- Jamie Oliver</span></p>

<!-- <p>{search}</p> -->

<div class="button-container">
    <input type="text" name="search" bind:value={search} placeholder="Enter an ingredient" />
    <button type="submit" on:click={handleSubmit}>Search</button>
</div>

<!-- Result section -->

<h3>Your search results</h3>

{#await promise}
	<p>Loading...</p>
{:then obj}
	{#if obj.meals}
        <div class="meals-container">
            {#each (obj.meals).slice(0, 12) as meal}
                <!-- {console.log(meal)} -->
                <div class="meal-container">
                    <img src={meal.strMealThumb} alt={meal.strMeal}>
                    <h6>{(meal.strMeal).slice(0, 35)}</h6>
                    <a data-sveltekit-preload-data="hover" href={`/${meal.idMeal}`} class="get-recipe-link">
                        <button>Get Recipe</button>
                    </a>
                </div>
            {/each}
        </div>
    {:else}
        <p>No results found!</p>
    {/if}
{:catch error}
	<p style="color: red">Something went wrong, Try again!</p>
{/await}

<style>
     *{
        text-align: center;
    }
    .quote-author{
        font-style: italic;
        display: block;
        text-transform: capitalize;
    }
    h1{
        margin: 0 auto;
        margin-top: 10vh;
        margin-bottom: 5vh;
    }
    .button-container{
        display: flex;
        width: 40vw;
        margin: 0 auto;
        height: 60px;
    }
    .button-container button{
        width: 200px;
        border-top-left-radius: 0;
        border-bottom-left-radius: 0;
        height: 100%;
    }
    .button-container input{
        border-top-right-radius: 0;
        border-bottom-right-radius: 0;
        text-align: left;
        height: 100%;
    }
    .meals-container{
        display: flex;
        flex-wrap: wrap;
        align-items: center;
        justify-content: center;
        gap: 2rem;
    }
    .meals-container .meal-container{
        width: 24%;
        box-shadow: 0 4px 21px -12px rgba(0, 0, 0, 0.79);
        border-radius: 6px;
    }
    .meals-container .meal-container img{
        width: 100%;
        height: 350px;
        border-top-left-radius: 6px;
        border-top-right-radius: 6px;
    }
    .get-recipe-link button{
        width: 150px;
        padding: 10px;
        /* border-radius: 25px; */
        margin: 0 auto;
        font-size: 18px;
        margin-bottom: 30px;
    }
    h6{
        font-size: 22px;
        margin-top: 30px;
        margin-bottom: 20px;
    }
    .mode-icon{
        color: white;
        position: fixed;
        bottom: 40px;
        right: 50px;
        font-size: 24px;
    }
</style>