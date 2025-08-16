<script>
    import { onMount } from "svelte";

    let clicks = 0;
    let growth = 1;
    let lvl = 1;
    let nextgoal = 32;

    let shop = [{
            "visible" : false,
            "boost" : null,
            "price" : null,
        },
        {
            "visible" : false,
            "boost" : null,
            "price" : null,
        },
        {
            "visible" : false,
            "boost" : null,
            "price" : null,
        }
    ]

    function grow(){
        clicks += growth;

        if(nextgoal < clicks){
            lvl += 1;
            growth = (2**lvl - 2**(lvl-1)) + growth;
            nextgoal = Math.round(clicks**1.1) + 32;
            
            console.log("lvl'd up")
            new Audio("/ting.mp3").play();
        }

        console.log("clicks: "+clicks+"\ngrowth: "+growth+"\nnext goal: "+nextgoal+"\nlvl: "+lvl)
    }

    function restock(){
        if(clicks > 32 && growth > 1){
            shop[0].visible = true;
            shop[1].visible = true;
            shop[2].visible = true;
            
            new Audio("/gong.mp3").play();
        }

        shop[0].price = Math.round((Math.random() * clicks) + (clicks * 0.4));
        shop[1].price = Math.round((Math.random() * clicks) + (clicks * 0.4));
        shop[2].price = Math.round((Math.random() * clicks) + (clicks * 0.4));

        shop[0].boost = Math.round(Math.random() * growth * 0.7);
        shop[1].boost = Math.round(Math.random() * growth * 0.7);
        shop[2].boost = Math.round(Math.random() * growth * 0.7);

        console.log("restocked")
        
    }

    function buy(pid){
        if(clicks >= shop[pid].price){
            new Audio("/buy.mp3").play();

            shop[pid].visible = false;
            clicks -= shop[pid].price;
            growth += shop[pid].boost;
        }
    }

    onMount(() => {
        setInterval(() => {
            restock();
        }, 5_000);
    })
</script>

<hr>
    <h1 style="text-align: center; font-size: 50px;">{clicks}</h1>
<hr>
<div id="stats" class="NCE">
    <p>Growth:</p>
    <h1>{growth}</h1>
    <p>Next Goal:</p>
    <h1>{nextgoal}</h1>
</div>

<button class="NCE" id="btn" on:click={grow}>Grow</button>

<div class="NCE" id="shop">
    <h3>Boost Shop</h3>
    {#each shop as purchase, i}
        {#if purchase.visible}
            <div class="purchase" on:click={buy(i)}>
                <h2>+{purchase.boost} Growth</h2>
                <b><p>-{purchase.price} Clicks</p></b>
            </div>
        {/if}
    {/each}
</div>

<style>
    *{
        font-family: 'Lucida Console';
    }

    #stats{
        padding: 10px;
        background-color: beige;
    }
    #btn{
        width: 20%;
        margin-top: 15px;
        height: 30px;
        font-size: 25px;
    }
    #shop{
        background-color: lightcoral;
        margin-top: 15px;
        padding: 10px;
    }
    .NCE{
        margin-left: 40%;
        margin-right: 40%;       
    }
    .purchase{
        background-color: gray;
        padding: 5px;
        margin-top: 10px;
    }
</style>