<script lang="ts">
  interface ITile {
    isBomb: boolean
    revealed: boolean
    nearbyBombs: number
  }
  type TBoard = ITile[][]

  let board: TBoard = []
  let rows = 10
  let cols = 10
  let bombs = 10

  let gameover: boolean = false

  for (let i = 0; i < rows; i++) {
    board[i] = new Array(10)
    for (let j = 0; j < cols; j++) {
      board[i][j] = {
        isBomb: false,
        revealed: false,
        nearbyBombs: 0,
      }
    }
  }

  for (let i = 0; i < bombs; i++) {
    let x = Math.floor(Math.random() * rows)
    let y = Math.floor(Math.random() * cols)
    if (board[x][y].isBomb) {
      i--
      continue
    }
    board[x][y].isBomb = true
  }

  const countNearbyBombs = (i: number, j: number) => {
    if (board[i][j].isBomb) {
      board[i][j].nearbyBombs = -1
      return
    }
    let count = 0
    for (let x = -1; x <= 1; x++) {
      for (let y = -1; y <= 1; y++) {
        try {
          if (board[x + i][y + j].isBomb) count++
        } catch (e) {}
      }
    }
    board[i][j].nearbyBombs = count
  }

  for (let i = 0; i < rows; i++) {
    for (let j = 0; j < cols; j++) {
      countNearbyBombs(i, j)
    }
  }

  const handleTileClick = (i: number, j: number) => {
    if (board[i][j].isBomb) {
      gameover = true
      setTimeout(() => {
        window.location.reload()
      }, 3000)
    }
    board[i][j].revealed = true
    if (board[i][j].nearbyBombs === 0) {
      for (let x = -1; x <= 1; x++) {
        for (let y = -1; y <= 1; y++) {
          try {
            if (!board[x + i][y + j].isBomb && !board[x + i][y + j].revealed) {
              handleTileClick(x + i, y + j)
            }
          } catch (e) {}
        }
      }
    }
  }
</script>

<div class="container">
  {#if gameover}
    <h1>GAME OVER....</h1>
  {/if}
  <div class="game">
    {#each board as row, i}
      {#each row as tile, j}
        <div class="tile" class:revealed={tile.revealed} on:click={() => handleTileClick(i, j)}>
          {#if tile.revealed}
            {#if tile.isBomb}
              🧨
            {:else if tile.nearbyBombs > 0}
              <div
                class:blue={tile.nearbyBombs === 1}
                class:green={tile.nearbyBombs === 2}
                class:red={tile.nearbyBombs === 3}
              >
                {tile.nearbyBombs}
              </div>
            {/if}
          {/if}
        </div>
      {/each}
    {/each}
  </div>
</div>

<style>
  .container {
    width: 100vw;
    min-height: 100vh;
    display: grid;
    place-items: center;
  }
  .game {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    width: 400px;
    height: 400px;
  }
  .tile {
    display: grid;
    place-items: center;
    width: 10%;
    height: 10%;
    border: 1px solid black;
    box-sizing: border-box;
    cursor: pointer;
  }

  .revealed {
    background-color: #d4d4d4;
  }
  .blue {
    color: blue;
    font-weight: bold;
    font-size: 1.2rem;
  }
  .green {
    color: green;
    font-weight: bold;
    font-size: 1.2rem;
  }
  .red {
    color: red;
    font-weight: bold;
    font-size: 1.2rem;
  }
</style>
