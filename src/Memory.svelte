<script>
    let baseUrl =
        "https://raw.githubusercontent.com/desertfox/memory-svelte/main/public/images/";

    export let cards = [
        baseUrl + "css.png",
        baseUrl + "github.png",
        baseUrl + "html.png",
        baseUrl + "npm.png",
        baseUrl + "ts.png",
    ];
    export let width = "60%";

    export let cardBack = baseUrl + "cardBack.png";
    export let onGameFinished = () => {};

    const cardsArray = cards.map((card, index) => {
        return {
            img: card,
            id: index,
        };
    });

    const gameGrid = cardsArray
        .concat(cardsArray)
        .map((c, i) => {
            return {
                id: i,
                name: c.img,
                img: c.img,
                found: false,
            };
        })
        .sort(() => 0.5 - Math.random());

    let firstGuess = null;
    let secondGuess = null;
    let finished = false;

    function selectCard(card) {
        if (firstGuess == null) {
            firstGuess = card;
        } else if (secondGuess == null) {
            secondGuess = card;
            if (firstGuess.name == secondGuess.name) {
                firstGuess.found = true;
                firstGuess = null;
                secondGuess.found = true;
                secondGuess = null;
                checkForWin();
            } else {
                setTimeout(() => {
                    firstGuess = null;
                    secondGuess = null;
                }, 1000);
            }
        }
    }

    function checkForWin() {
        finished = gameGrid.filter((card) => !card.found).length == 0;
        if (finished) {
            onGameFinished();
        }
    }
</script>

<div class="grid" style="width: {width};">
    {#each gameGrid as card}
        <div
            class="card"
            class:selected={card.found ||
                (firstGuess && firstGuess.id == card.id) ||
                (secondGuess && secondGuess.id == card.id)}
            on:click={() => selectCard(card)}
        >
            <div class="front" style="background-image: url({cardBack});" />
            <div class="back" style="background-image: url({card.img});" />
        </div>
    {/each}
</div>

<style>
    .grid {
        max-width: 960px;
        margin: 0 auto;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-evenly;
    }

    .card {
        position: relative;
        transition: all 0.4s linear;
        transform-style: preserve-3d;
        margin: 5px;
    }

    .card,
    .back,
    .front {
        height: 150px;
        width: 150px;
    }

    .back,
    .front {
        position: absolute;
        backface-visibility: hidden;
    }

    .front {
        z-index: 2;
        background: #fab942 no-repeat center center / contain;
    }

    .back {
        transform: rotateY(180deg);
        background-size: contain;
        background-repeat: no-repeat;
        background-position: center center;
    }

    .selected {
        transform: rotateY(180deg);
    }
</style>
