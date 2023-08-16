<script>
  import { Chess } from 'chess.js';
  import { tweened } from 'svelte/motion';
  const game = new Chess();

  let board = game.board();

  const pieceSymbols = {
    'p': '♟',
    'r': '♜',
    'n': '♞',
    'b': '♝',
    'q': '♛',
    'k': '♚',
    'P': '♙',
    'R': '♖',
    'N': '♘',
    'B': '♗',
    'Q': '♕',
    'K': '♔',
    '.' : '',
  };

  function movePiece(from, to) {
    game.move({ from, to });
    board = game.board();
  }
  let text = ``;

  let now = Date.now();

  let end = now + 5 * 60 * 1000;

  let isPaused = true;
  $: count = isPaused ? 5 * 60 : Math.round((end - now) / 1000);
  $: h = Math.floor(count / 3600);
  $: m = Math.floor((count - h * 3600) / 60);
  $: s = count - h * 3600 - m * 60;
  $: minname = m > 1 ? "mins" : "min";


  function updateTimer() {
    now = Date.now();
  }
  let interval = setInterval(updateTimer, 1000);

  function handleStart() {
    now = Date.now();
    end = now + 60 * 5 * 1000;
    interval = setInterval(updateTimer, 1000);
  
    isPaused = false;
  }
</script>

<style>
  .board {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
    width: 600px;
    height: 600px;
  }

  .square {
    width: 75px;
    height: 75px;
    font-size: 50px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .white {
    color: black;
    background-color: #eee;
  }

  .black {
    color: white;
    background-color: #333;
  }
 
</style>
<div class="board">
    {#each board as row, rowIndex}
      {#each row as square, colIndex}
        <div class="square {((rowIndex + colIndex) % 2 === 0) ? 'white' : 'black'}" on:click={() => movePiece(String.fromCharCode(97 + colIndex) + (8 - rowIndex), 'e4')}>
          {square ? pieceSymbols[square.color === 'w' ? square.type.toUpperCase() : square.type] : ''}
        </div>
      {/each}
    {/each}
  </div>
  
  <textarea bind:value={text}></textarea>
  
  <h1>Game begins in <span class="mins">{m}</span>{minname} 
      <span class="secs">{s}</span>s
  </h1>
  
  <button on:click={handleStart} >
      Start timer
  </button>
  