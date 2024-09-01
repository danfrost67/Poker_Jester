<script>
    import { onMount } from 'svelte';

    let step = -1;
    let player = '';
    let venue = '';
    let date = '';
    let currentHand = { suited: false, card1: null, card2: null };
    let position = '';
    let playerCount = 8;
    const positionList = ['B', 'BB', 'SB', 'CO', 'HJ', 'LJ', 'UTG', 'UTG1', 'UTG2'];
	let positions = ['B', 'BB', 'SB', 'CO', 'HJ', 'LJ', 'UTG', 'UTG1'];
    const cards = ['A', 'K', 'Q', 'J', 'T', '9', '8', '7', '6', '5', '4', '3', '2'];
    const players = [2, 3, 4, 5, 6, 7, 8, 9];
    let handHistory = [];
    let gameHistory = [];

    onMount(() => {
        const now = new Date();
        date = now.toISOString().split('T')[0]; // YYYY-MM-DD format

        for (let i = 0; i < localStorage.length; i++) {
            // Get the key at position i
            const key = localStorage.key(i);
            // Print the key to the console
            gameHistory.push(key);
            console.log(key);
        }
        step = 0;
    });

    function confirmGameInfo() {
        // create a local storage key
        const key = `${venue} - ${date}`;
        // create a local storage value that is json 
        const value = JSON.stringify({ player, venue, date, handHistory });
        // set the key value pair in local storage
        localStorage.setItem(key, value);

        gameHistory.push(key);

        step = 2;
    }

    function loadGame(key) {
        const value = localStorage.getItem(key);
        const gameDetails = JSON.parse(value);

        console.log(gameDetails);

        player = gameDetails.player;
        venue = gameDetails.venue;
        date = gameDetails.date;
        handHistory = gameDetails.handHistory ?? [];
        step = 4;
    }

    function handlePosition(pos) {
        position = pos;
        document.querySelectorAll('.position-selection button').forEach(btn => {
            btn.classList.toggle('active', btn.textContent === pos);
        });
    }

    function handlePlayers(p) {
        playerCount = parseInt(p);
		positions = [];
		for (let i=0; i < Math.min(6, playerCount); i++){
            positions.push(positionList[i]);
        }

        if (playerCount === 7) {
            positions.push('UTG');
        };

        if (playerCount === 8) {
            positions.push('UTG1');
            positions.push('UTG');
        };

        if (playerCount === 9) {
            positions.push('UTG2');
            positions.push('UTG1');
            positions.push('UTG');
        };
    }

    function handleCardSelection(card) {
        if (!currentHand.card1) {
            currentHand.card1 = card;
        } else if (!currentHand.card2) {
            currentHand.card2 = card;
        } else {
            alert('You may only select two cards');
        }
    }

    function toggleSuited() {
        currentHand.suited = !currentHand.suited;
    }

    function undoLastCardSelection() {
        if (currentHand.card2) {
            currentHand.card2 = null;
        } else if (currentHand.card1) {
            currentHand.card1 = null;
        }
    }

    function goToPositionAndPlayerSelection() {
        step = 2;
    }

    function confirmCardSelection() {
        handHistory.push({
            position: position,
            playerCount: playerCount,
            card1: currentHand.card1,
            card2: currentHand.card2,
            suited: currentHand.suited
        });

        // update the game in local storage
        const key = `${venue} - ${date}`;
        const value = JSON.stringify({ player, venue, date, handHistory });
        localStorage
            .setItem(key, value);

        currentHand = { suited: false, card1: null, card2: null };

        step = 4;
    }

    function confirmPlayerPosition() {
        step = 3;
    }

    function goToCardSelectionScreen() {
        // if there is no position or player count selected, go back to the position and player selection screen
        if (!position || !playerCount) {
            step = 2;
        }

        step = 3;
    }

    function goToPositionAndPlayerScreen() {
        step = 2;
    }
</script>

<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 20px;
        background-color: #f4f4f4;
    }

    .container {
        max-width: 900px;
        margin: auto;
        padding: 20px;
        background: #D3D3D3;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .player-postion {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 20px;
    }

    .card-grid {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 10px;
    }

    .card-grid button {
        padding: 20px;
        font-size: 24px;
        border: 1px solid #007bff;
        border-radius: 4px;
        background-color: #f8f9fa;
        color: #007bff;
        cursor: pointer;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .card-grid button:hover{
        background-color: #007bff;
        color: #fff;
    }

    .player-info {
        display: flex;
        flex-direction: column;
        margin-bottom: 15px;
    }

    .player-info label {
        font-weight: bold;
        margin-bottom: 5px;
    }

    .player-info input 
    {
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
        width: 100%;
    }

    .position-selection, .player-selection {
        margin-bottom: 20px;
    }

    .position-selection ul, .player-selection ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .position-selection button, .player-selection button {
        padding: 10px;
        font-size: 16px;
        width: 100%;
        border: 1px solid #007bff;
        border-radius: 4px;
        background-color: #f8f9fa;
        color: #007bff;
        cursor: pointer;
    }

    .position-selection button.active, .player-selection button.active {
        background-color: #007bff;
        color: #fff;
    }

    .position-selection button:hover, .player-selection button:hover {
        background-color: #007bff;
    }

    .card-display {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
        align-items: center;
        text-align: center;
        background-color: #90EE90;
        width: 100%;
    }

    .position-player-info {
        margin-bottom: 10px;
        font-size: 18px;
        text-align: center;
        display: flex;
        align-items: center;
    }

    .card-info span {
        display: inline-block;
        margin-right: 10px;
    }

    .card-display span {
        font-size: 24px;
        margin-right: 10px;
    }

    .card-display .undo-button {
        margin-left: 20px;
        background-color: #f8f9fa;
        border: 1px solid #ccc;
        border-radius: 50%;
        width: 36px;
        height: 36px;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
    }

    .card-display .undo-button:hover {
        background-color: #e2e6ea;
    }

.cards-suited {
    margin-bottom: 20px; /* Add space below the Suited row */
    display: flex;
    justify-content: center;
    align-items: center;
}

.cards-suited span {
    margin-right: 10px;
}

.cards-suited button {
    padding: 10px 20px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #f8f9fa;
    color: #007bff;
    cursor: pointer;
}

.cards-suited button.active {
    background-color: #007bff;
    color: #fff;
}

    .confirm-button {
        padding: 10px 20px;
        font-size: 16px;
        background-color: #28a745; /* Green color */
        color: #fff;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        margin-top: 20px;
        width: 100%;
    }

    .confirm-button:hover {
        background-color: #218838;
    }

    .text-box {
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: #f8f9fa;
        margin-bottom: 20px;
        cursor: pointer;
        width: 100%;
        text-align: center;
    }

    .text-box:hover {
        background-color: #e2e6ea;
    }
</style>

<!-- HH    HH   TTTTTTTT MM      MM LL       -->						
<!-- HH    HH      TT    MMM    MMM LL       -->						
<!-- HHHHHHHH      TT    MM MMMM MM LL       -->						
<!-- HH    HH      TT    MM  MM  MM LL       -->						
<!-- HH    HH      TT    MM  MM  MM LLLLLLLL -->						

<div class="container">

<!-- Game History-->

    {#if step === 0}
    <div class="player-info">

        {#each gameHistory as game}
        <button class="card-info" on:click={() => loadGame(game)}>
            {game}
        </button>
        {/each}

        <button class="confirm-button" on:click={() => step = 1}>Add New Game</button>
    </div>

<!-- PPPPPPP    LL           AA     YY    YY  EEEEEEEE  RRRRRRR  -->						
<!-- PP    PP   LL         AA  AA    YY  YY   EE        RR    RR -->						
<!-- PPPPPPPP   LL        AAAAAAAA    YYYY    EEEEEE    RRRRRRR  -->						
<!-- PP         LL        AA    AA     YY     EE        RR   RR  -->						
<!-- PP         LLLLLLLL  AA    AA     YY     EEEEEEEE  RR    RR -->						
    {:else if step === 1}

        <div class="player-info">
            <h2>Player Information</h2>
            <label for="player">Player:</label>
            <input id="player" type="text" bind:value={player} />
            <label for="venue">Venue:</label>
            <input id="venue" type="text" bind:value={venue} />
            <label for="date">Date:</label>
            <input id="date" type="date" bind:value={date} />
            <button class="confirm-button" on:click={confirmGameInfo}>Confirm</button>
        </div>

<!-- PPPPPPP     OOOOOO    SSSSSSS  IIIIIIII  TTTTTTTT  IIIIIIII   OOOOOO   NN    NN -->						
<!-- PP    PP   OO    OO  SS           II        TT        II     OO    OO  NNN   NN -->						
<!-- PPPPPPPP   OO    OO   SSSSSS      II        TT        II     OO    OO  NN NN NN -->						
<!-- PP         OO    OO        SS     II        TT        II     OO    OO  NN  NNNN -->						
<!-- PP          OOOOOO   SSSSSSS   IIIIIIII     TT     IIIIIIII   OOOOOO   NN    NN -->						
    {:else if step === 2}
        <div class="player-postion">
            <div class="player-selection">
                <h2>Players</h2>
                <ul>
                    {#each players as p}
                        <li>
                            <button
                                class={p === playerCount ? 'active' : ''}
                                on:click={() => handlePlayers(p)}
                            >
                                {p}
                            </button>
                        </li>
                    {/each}
                </ul>
            </div>
            <div class="position-selection">
                <h2>Position</h2>
                <ul>
                    {#each positions as pos}
                        <li>
                            <button
                                class={pos === position ? 'active' : ''}
                                on:click={() => handlePosition(pos)}
                            >
                                {pos}
                            </button>
                        </li>
                    {/each}
                </ul>
            </div>
        </div>
        <button class="confirm-button" on:click={confirmPlayerPosition}>Confirm</button>

<!--  CCCCCCC      AA     RRRRRRR   DDDDDD     SSSSSSS -->						
<!-- CC          AA  AA   RR    RR  DD    DD  SS       -->						
<!-- CC         AAAAAAAA  RRRRRRR   DD    DD   SSSSSS  -->						
<!-- CC         AA    AA  RR   RR   DD    DD        SS -->						
<!--  CCCCCCC   AA    AA  RR    RR  DDDDDD    SSSSSSS  -->						
    {:else if step === 3}
        <div>
            <div class="position-player-info">
                <div class="text-box" on:click={goToPositionAndPlayerScreen}>
                    {position} | {playerCount}
                </div>
            </div>
            <div class="card-display">
                <span>Hand: 
                {currentHand.card1 ?? ' '}
                {currentHand.card2 ?? ' '}
                {currentHand.card2 != undefined ? (currentHand.suited ? 's' : 'o'): ' '}
                </span>
            </div>
            <div class="cards-suited">
                <span>Suited:</span>
                <button class={currentHand.suited ? 'active' : ''} on:click={toggleSuited}>
                    {currentHand.suited ? 'Yes' : 'No'}
                </button>
            </div>
            <!-- Card Grid -->
            <div class="card-grid">
                {#each cards as card}
                    <button on:click={() => handleCardSelection(card)}>{card}</button>
                {/each}
            </div>
            <button class="confirm-button" on:click={confirmCardSelection}>Confirm</button>
        </div>

<!--    AA       CCCCCCC  TTTTTTTT  IIIIIIII   OOOOOO   NN    NN -->						
<!--  AA  AA    CC           TT        II     OO    OO  NNN   NN -->						
<!-- AAAAAAAA   CC           TT        II     OO    OO  NN NN NN -->						
<!-- AA    AA   CC           TT        II     OO    OO  NN  NNNN -->						
<!-- AA    AA    CCCCCCC     TT     IIIIIIII   OOOOOO   NN    NN -->						
    {:else if step === 4}
        <div>
            {#each handHistory as hand}

            <div class="card-info">
                <div class="text-box">
                    Pos: {hand.position}  Count: {hand.playerCount}  C1: {hand.card1 ?? ' '} C2: {hand.card2 ?? ' '} Suited: {hand.suited ? 'Yes' : 'No'}
                </div>
            </div>
            {/each}
            <button class="confirm-button" on:click={goToCardSelectionScreen}>Add Another Hand</button>
        </div>
    {/if}
</div>
