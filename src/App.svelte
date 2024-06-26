<script lang="ts">
	import { onMount } from 'svelte'

  import Fa from 'svelte-fa'
  import { faGithub } from '@fortawesome/free-brands-svg-icons'
  import { faFilePdf } from '@fortawesome/free-solid-svg-icons'

  import Blanchor from "./Blanchor.svelte"

  import Pacman, { ARENA, DIRECTION, User, WALLS } from "./lib/pacman";
  import selectAction from "./lib/train/selectAction";
  import weights from "./lib/train/data/weights.json"
  import eGreedy from './lib/train/eGreedy';
  import linearQFunction from './lib/train/linearQFunction';


  let canvas: HTMLCanvasElement
  let game: Pacman | null = null
  let useAgent = true

  onMount(async () => {
    game = new Pacman({
      arena: ARENA,
      canvas,
      userActCallback: (user: User):DIRECTION => {
        if(useAgent && user.onWholeBlock(user.position)) {
          return selectAction(
            user.game,
            weights,
            eGreedy,
            linearQFunction,
          )
        }
        return user.desiredDirection
      },
      walls: WALLS,
    })
  })
</script>

<main>
  <header>
    <div>
      <h1>Pac-Man using Reinforcement Learning</h1>

      <br/>

      <div style="font-size:2em">
        <Blanchor href="https://github.com/charvijain12/PacMan">
          <Fa class="icon-button" icon={faGithub} style="color:white;"/>
        </Blanchor>
      </div>
    </div>
  </header>

	<section>
    <div id="game-container">
      <canvas id="pacman" bind:this={canvas}/>

      <div><button on:click={() => game && game.startNewGame()}>Start a New Game</button></div>

      <br/>

      <div>
        <div><b>How should Pac-Man be controled?</b></div>
        <div id="agent-control-buttons">
          <button class={useAgent===true && "focused"} on:click={() => useAgent = true}>By a Pre-Trained Agent</button>
          <span>or</span>
          <button class={useAgent===false && "focused"} on:click={() => useAgent = false}>By Myself with Arrow Keys</button>
        </div>
      </div>
    </div>
  </section>
  
  <section><hr/></section>

</main>

<style>
  main {
    width: 100%;
    overflow-x: hidden;
  }

	header, section, footer {
		padding: 1em;
	}
	@media only screen and (min-width: 600px) {
		header, section, footer {
			padding-left: var(--tablet-padding);
			padding-right: var(--tablet-padding);
		}
	}
	@media only screen and (min-width: 1000px) {
		header, section, footer {
			padding-left: var(--desktop-padding);
			padding-right: var(--desktop-padding);
		}
	}

	header {
		display: flex;
		align-items: center;
		justify-content: center;
		overflow: hidden;
		background-color: #17202A;
		color: white;
    text-align: center;
    padding-top: 5em;
    padding-bottom: 5em;
	}

  #agent-control-buttons {
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding-top: 0.5em;
  }
  @media only screen and (min-width: 450px) {
		#agent-control-buttons {
			flex-direction: row;
		}
	}

  #agent-control-buttons button {
    background-color: transparent;
    border: 1px solid gray;
    color: gray
  }
  #agent-control-buttons button.focused {
    background-color: green;
    border: 1px solid green;
    color: white
  }

  #agent-control-buttons span {
    margin-top: 0.25em;
    padding-left: 1em;
    padding-right: 1em;
  }

  #game-container {
    text-align: center;
  }

  canvas {
    width: 100%;
    max-width: 500px;
  }

	footer {
		background-color: #ddd;
	}
</style>