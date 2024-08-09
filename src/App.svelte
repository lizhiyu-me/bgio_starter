<script>
    import { Client } from "boardgame.io/client";
    import { TicTacToe } from "./Game";
    import { onMount } from "svelte";

    let client;
    let cells = Array(9).fill(null);
    let winner = null;

    function createClient() {
        client = Client({ game: TicTacToe, debug: true });
        client.start();
        client.subscribe(handleUpdate);
    }

    function handleUpdate(state) {
        cells = state.G.cells;
        winner = state.ctx.gameover
            ? state.ctx.gameover.winner !== undefined
                ? "Winner: " + state.ctx.gameover.winner
                : "Draw!"
            : null;
    }

    function handleCellClick(id) {
        client.moves.clickCell(id);
    }

    onMount(() => {
        createClient();
    });
</script>

<div class="container">
    {#if client}
        <table>
            {#each Array(3) as _, i}
                <tr>
                    {#each Array(3) as _, j}
                        {@const id = 3 * i + j}
                        <td class="cell" on:click={() => handleCellClick(id)}>
                            {cells[id] || ""}
                        </td>
                    {/each}
                </tr>
            {/each}
        </table>
        <p class="winner">{winner ? winner : "-"}</p>
    {/if}
</div>

<style>
    .cell {
        width: 50px;
        height: 50px;
        border: 1px solid black;
        text-align: center;
        vertical-align: middle;
    }

    .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
</style>
