<script>
// JJJJJJJJ      AA     VV    VV     AA      SSSSSSS   CCCCCCC  RRRRRRR   IIIIIIII  PPPPPPP   TTTTTTTT 						
//    JJ       AA  AA    VV  VV    AA  AA   SS        CC        RR    RR     II     PP    PP     TT    						
//    JJ      AAAAAAAA   VV  VV   AAAAAAAA   SSSSSS   CC        RRRRRRR      II     PPPPPPPP     TT    						
// J  JJ      AA    AA     VV     AA    AA        SS  CC        RR   RR      II     PP           TT    						
//  JJJ       AA    AA     VV     AA    AA  SSSSSSS    CCCCCCC  RR    RR  IIIIIIII  PP           TT    						
    import { onMount } from 'svelte';
    let step = -1;
    let player = '';
    let venue = '';
    let date = '';
    let currentHand = { suited: false, card1: null, card2: null };
    let position = '';
    let playerCount = 8;
    let toCall = 2;
    let myAction = 'Fold';
    const positionList = ['B', 'BB', 'SB', 'CO', 'HJ', 'LJ', 'UTG', 'UTG1', 'UTG2'];
	let positions = ['B', 'BB', 'SB', 'CO', 'HJ', 'LJ', 'UTG', 'UTG1'];
    let actions = ['Fold', 'Call', 'Raise'];
    const cards = ['A', 'K', 'Q', 'J', 'T', '9', '8', '7', '6', '5', '4', '3', '2'];
    const players = [2, 3, 4, 5, 6, 7, 8, 9];
    let handHistory = [];
    let gameHistory = [];

    onMount(() => {
        const now = new Date();

        const year = now.getFullYear();
        const month = now.getMonth() + 1; // JavaScript months are 0-indexed
        const day = now.getDate();

        date = `${year}-${month.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`;

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
        const key = `${player} - ${venue} - ${date}`;
        // create a local storage value that is json 
        const value = JSON.stringify({ player, venue, date, handHistory });
        // set the key value pair in local storage
        localStorage.setItem(key, value);
        gameHistory.push(key);
        step = 2;
    }

// LL          OOOOOO      AA     DDDDDD               GGGGGG      AA    MM      MM EEEEEEEE 						
// LL         OO    OO   AA  AA   DD    DD            GG         AA  AA  MMM    MMM EE       						
// LL         OO    OO  AAAAAAAA  DD    DD            GG   GGG  AAAAAAAA MM MMMM MM EEEEEE   						
// LL         OO    OO  AA    AA  DD    DD            GG     G  AA    AA MM  MM  MM EE       						
// LLLLLLLL    OOOOOO   AA    AA  DDDDDD               GGGGGG   AA    AA MM  MM  MM EEEEEEEE 						
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

// PPPPPPP     OOOOOO    SSSSSSS  IIIIIIII  TTTTTTTT  IIIIIIII   OOOOOO   NN    NN 						
// PP    PP   OO    OO  SS           II        TT        II     OO    OO  NNN   NN 						
// PPPPPPPP   OO    OO   SSSSSS      II        TT        II     OO    OO  NN NN NN 						
// PP         OO    OO        SS     II        TT        II     OO    OO  NN  NNNN 						
// PP          OOOOOO   SSSSSSS   IIIIIIII     TT     IIIIIIII   OOOOOO   NN    NN 						    
    function handlePosition(pos) {
        position = pos;
        document.querySelectorAll('.position-selection button').forEach(btn => {
            btn.classList.toggle('active', btn.textContent === pos);
        });
    }

// PPPPPPP    LL           AA     YY    YY  EEEEEEEE  RRRRRRR    SSSSSSS 						
// PP    PP   LL         AA  AA    YY  YY   EE        RR    RR  SS       						
// PPPPPPPP   LL        AAAAAAAA    YYYY    EEEEEE    RRRRRRR    SSSSSS  						
// PP         LL        AA    AA     YY     EE        RR   RR         SS 						
// PP         LLLLLLLL  AA    AA     YY     EEEEEEEE  RR    RR  SSSSSSS  						    
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

    function confirmPlayerPosition() {
        step = 3;
    }


//  CCCCCCC      AA     RRRRRRR   DDDDDD     SSSSSSS 						
// CC          AA  AA   RR    RR  DD    DD  SS       						
// CC         AAAAAAAA  RRRRRRR   DD    DD   SSSSSS  						
// CC         AA    AA  RR   RR   DD    DD        SS 						
//  CCCCCCC   AA    AA  RR    RR  DDDDDD    SSSSSSS  						
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

    function confirmCardSelection() {
        step = 4;
    }

    function confirmAction() {
        handHistory.push({
            position: position,
            playerCount: playerCount,
            card1: currentHand.card1,
            card2: currentHand.card2,
            suited: currentHand.suited
        });

        // update the game in local storage
        const key = `${player} - ${venue} - ${date}`;
        const value = JSON.stringify({ player, venue, date, handHistory });
        localStorage
            .setItem(key, value);

        currentHand = { suited: false, card1: null, card2: null };

        step = 5;

    }   

    function handleAction(action) {
        myAction = action;
    }


// JJJJJJJJ   UU    UU MM      MM PPPPPPP             PPPPPPP      AA      GGGGGG   EEEEEEEE 						
//    JJ      UU    UU MMM    MMM PP    PP            PP    PP   AA  AA   GG        EE       						
//    JJ      UU    UU MM MMMM MM PPPPPPPP            PPPPPPPP  AAAAAAAA  GG   GGG  EEEEEE   						
// J  JJ      UU    UU MM  MM  MM PP                  PP        AA    AA  GG     G  EE       						
//  JJJ        UUUUUU  MM  MM  MM PP                  PP        AA    AA   GGGGGG   EEEEEEEE 						    

    function goToPositionAndPlayerScreen() {
        step = 2;
    }

    function goToCardSelectionScreen() {
        // if there is no position or player count selected, go back to the position and player selection screen
        if (!position || !playerCount) {
            step = 2;
        }
        step = 3;
    }

</script>

<style>
/*  CCCCCCC    SSSSSSS   SSSSSSS */						
/* CC         SS        SS       */						
/* CC          SSSSSS    SSSSSS  */						
/* CC               SS        SS */						
/*  CCCCCCC   SSSSSSS   SSSSSSS  */						

    .container {
        max-width: 900px;
        margin: auto;
        padding: 20px;
        background: #D3D3D3;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        display: flex;
        flex-direction: column;
        margin-bottom: 15px;

    }

    .container button {
        padding: 10px 20px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: #f8f9fa;
        color: #007bff;
        cursor: pointer;
        width: 100%;
    }

    .container label {
        font-weight: bold;
        margin-bottom: 5px;
    }

    .container input 
    {
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
        width: 100%;
    }

    .container ul, li {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .confirm button{
        background-color: #28a745;
        color: #000000;
    }

    .text-box {
        padding: 10px;
        font-size: 24px;
        border: 1px solid #ccc;
        border-radius: 4px;
        background-color: #f8f9fa;
        margin-bottom: 20px;
        cursor: pointer;
        width: 100%;
        text-align: center;
    }

/* PPPPPPP     OOOOOO    SSSSSSS  IIIIIIII  TTTTTTTT  IIIIIIII   OOOOOO   NN    NN */						
/* PP    PP   OO    OO  SS           II        TT        II     OO    OO  NNN   NN */						
/* PPPPPPPP   OO    OO   SSSSSS      II        TT        II     OO    OO  NN NN NN */						
/* PP         OO    OO        SS     II        TT        II     OO    OO  NN  NNNN */						
/* PP          OOOOOO   SSSSSSS   IIIIIIII     TT     IIIIIIII   OOOOOO   NN    NN */						    
    .player-postion {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 20px;
    }

    .position-selection button.active, .player-selection button.active {
        background-color: #007bff;
        color: #fff;
    }

/*  CCCCCCC      AA     RRRRRRR   DDDDDD     SSSSSSS */						
/* CC          AA  AA   RR    RR  DD    DD  SS       */						
/* CC         AAAAAAAA  RRRRRRR   DD    DD   SSSSSS  */						
/* CC         AA    AA  RR   RR   DD    DD        SS */						
/*  CCCCCCC   AA    AA  RR    RR  DDDDDD    SSSSSSS  */						
    .card-grid {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 10px;
    }

    /* .card-grid button {
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
    } */

    /* .card-display {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
        align-items: center;
        text-align: center;
        background-color: #90EE90;
        width: 100%;
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
    margin-bottom: 20px; 
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
} */

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
        <div class = "confirm">
            <button class="confirm-button" on:click={() => step = 1}>Add New Game</button>
        </div>
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
        <div class = "confirm">
            <button class="confirm-button" on:click={confirmGameInfo}>Confirm</button>
        </div>
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
        <div class = "confirm">
            <button class="confirm-button" on:click={confirmPlayerPosition}>Confirm</button>
        </div>

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
            <div class = "confirm">
                <button class="confirm-button" on:click={confirmCardSelection}>Confirm</button>
            </div>
        </div>

<!--    AA       CCCCCCC  TTTTTTTT  IIIIIIII   OOOOOO   NN    NN -->						
<!--  AA  AA    CC           TT        II     OO    OO  NNN   NN -->						
<!-- AAAAAAAA   CC           TT        II     OO    OO  NN NN NN -->						
<!-- AA    AA   CC           TT        II     OO    OO  NN  NNNN -->						
<!-- AA    AA    CCCCCCC     TT     IIIIIIII   OOOOOO   NN    NN -->						
    {:else if step === 4}
    <div>
        <!-- <p>ACTION</p> -->
        <div class="position-player-info">
            <div class="text-box" on:click={goToPositionAndPlayerScreen}>
                {position} | {playerCount}
            </div>
        </div>
        <div class="position-player-info">
            <div class="text-box" on:click={goToCardSelectionScreen}>
                {currentHand.card1 ?? ' '}
                {currentHand.card2 ?? ' '}
                {currentHand.card2 != undefined ? (currentHand.suited ? 's' : 'o'): ' '}
            </div>
        </div>
        <div class = "toCall">
            <label for="toCall">To call:</label>
            <input id="toCall" type="text" bind:value={toCall} />
        </div>
        <div class="action">
            <span>Action:</span>
            {#each actions as action}
                <button on:click={() => handleAction(action)}>{action}</button>
            {/each}

        </div>


        <div class = "confirm">
            <button class="confirm-button" on:click={confirmAction}>Confirm</button>
        </div>
    </div>

<!-- HH    HH   IIIIIIII   SSSSSSS  TTTTTTTT   OOOOOO   RRRRRRR   YY    YY -->						
<!-- HH    HH      II     SS           TT     OO    OO  RR    RR   YY  YY  -->						
<!-- HHHHHHHH      II      SSSSSS      TT     OO    OO  RRRRRRR     YYYY   -->						
<!-- HH    HH      II           SS     TT     OO    OO  RR   RR      YY    -->						
<!-- HH    HH   IIIIIIII  SSSSSSS      TT      OOOOOO   RR    RR     YY    -->						
    {:else if step === 5}
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
