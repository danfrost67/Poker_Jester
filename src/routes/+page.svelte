<script>
    import { onMount } from 'svelte';

    let step = 1;
    let player = '';
    let venue = '';
    let date = '';
    let currentHand = { suited: false, card1: null, card2: null };
    let position = '';
    let playerCount = 8;
    const positionList = ['B', 'CO', 'HJ', 'LJ', 'UTG2', 'UTG1', 'UTG', 'BB', 'SB'];
	let positions = ['B', 'CO', 'HJ', 'LJ', 'UTG2', 'UTG1', 'UTG', 'BB', 'SB'];
    const cards = ['A', 'K', 'Q', 'J', 'T', '9', '8', '7', '6', '5', '4', '3', '2'];
    const players = ['2', '3', '4', '5', '6', '7', '8', '9'];

    onMount(() => {
        const now = new Date();
        date = now.toISOString().split('T')[0]; // YYYY-MM-DD format
    });

    function confirmPlayerInfo() {
        step = 2;
    }

    function handlePosition(pos) {
        position = pos;
        document.querySelectorAll('.position-selection button').forEach(btn => {
            btn.classList.toggle('active', btn.textContent === pos);
        });
    }

    function handlePlayers(p) {
        playerCount = parseInt(p);
		let positions = [];
		for (let i=0; i < playerCount; i++){
            positions.push(positionList[i]);
        }

        // if (playerCount = 6) {
        //     positions.push('UTG');
        // };

        document.querySelectorAll('.player-selection button').forEach(btn => {
            btn.classList.toggle('active', btn.textContent === p);
        });
    }

    function confirmPlayerPosition() {
        step = 3;
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
        step = 4;
    }

    function goToPositionAndPlayerScreen() {
        step = 2;
    }

    function goToCardSelectionScreen() {
        step = 3;
    }
</script>

<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 20px;
        /* background-color: #FF0000; */
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

    .grid-container {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 20px;
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

    .card-grid button:hover,
    .card-grid button.active {
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

    .player-info input,
    .player-info select {
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
        width: 100%;
    }

    .player-info button {
        padding: 10px 20px;
        font-size: 16px;
        background-color: #28a745; /* Green color */
        color: #fff;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        width: 100%;
        margin-top: 10px;
    }

    .player-info button:hover {
        background-color: #218838;
    }

    .position-selection, .player-selection {
        margin-bottom: 20px;
    }

    .position-selection ul, .player-selection ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .position-selection li, .player-selection li {
        margin-bottom: 10px;
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
        /* background-color: #e2e6ea; */
        background-color: #007bff;
    }

    .card-selection {
        margin-bottom: 20px;
    }

    .card-display {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
        align-items: center;
    }

    .card-info {
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

    .card-info button {
        margin-left: 10px;
        padding: 5px 10px;
        font-size: 14px;
        border: 1px solid #007bff;
        background-color: #fff;
        color: #007bff;
        border-radius: 4px;
        cursor: pointer;
    }

    .card-info button:hover {
        background-color: #007bff;
        color: #fff;
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

    .card-display .suited {
        margin-top: 10px;
        display: flex;
        align-items: center;
    }

    .suited span {
        margin-right: 10px;
    }

    .suited button {
        padding: 10px 20px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: #f8f9fa;
        color: #007bff;
        cursor: pointer;
    }

    .suited button.active {
        background-color: #007bff;
        color: #fff;
    }

    .suited button:hover {
        background-color: #007bff;
        color: #fff;
    }

    .suited-row {
    margin-bottom: 20px; /* Add space below the Suited row */
    display: flex;
    justify-content: center;
    align-items: center;
}

.suited-row span {
    margin-right: 10px;
}

.suited-row button {
    padding: 10px 20px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #f8f9fa;
    color: #007bff;
    cursor: pointer;
}

.suited-row button.active {
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

<div class="container">
    {#if step === 1}
        <div class="player-info">
            <h2>Player Information</h2>
            <label for="player">player:</label>
            <input id="player" type="text" bind:value={player} />
            <label for="venue">venue:</label>
            <input id="venue" type="text" bind:value={venue} />
            <label for="date">Date:</label>
            <input id="date" type="date" bind:value={date} />
            <button class="confirm-button" on:click={confirmPlayerInfo}>Confirm</button>
        </div>
    {:else if step === 2}
        <div class="player-postion">
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
        </div>
        <button class="confirm-button" on:click={confirmPlayerPosition}>Confirm</button>
    {:else if step === 3}
        <div>
            <div class="card-info">
                <span on:click={goToPositionAndPlayerSelection}>{position}</span>
                <span on:click={goToPositionAndPlayerSelection}>{playerCount}</span>
            </div>
            <div class="card-display">
                <div class="card-info">
                    <span>{currentHand.card1 ?? ' '}</span>
                    <span>{currentHand.card2 ?? ' '}</span>
                    <span>{currentHand.card2 != undefined ? (currentHand.suited ? 's' : 'o'): ' '}</span>
                </div>
                <!-- Move Suited Section Here -->
                <div class="suited-row">
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
        </div>
    {:else if step === 4}
        <div>
            <div class="card-info">
                <div class="text-box" on:click={goToPositionAndPlayerScreen}>
                    {position} | {playerCount}
                </div>
                <div class="text-box" on:click={goToCardSelectionScreen}>
                    {currentHand.card1 ?? ' '}, {currentHand.card2 ?? ' '}, Suited: {currentHand.suited ? 'Yes' : 'No'}
                </div>
            </div>
        </div>
    {/if}
</div>
