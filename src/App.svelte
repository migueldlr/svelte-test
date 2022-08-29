<script lang="ts">
  import produce from 'immer';
  import { quintOut } from 'svelte/easing';
  import { crossfade } from 'svelte/transition';
  import { flip } from 'svelte/animate';
  import { getRandomInt } from './lib/util';

  let uid = 0;
  let index = 0;
  let value = 0;

  const makeEl = (value: number) => {
    return {
      id: uid++,
      value,
    };
  };

  const [send, receive] = crossfade({
    duration: (d) => Math.sqrt(d * 200),

    fallback(node, params) {
      const style = getComputedStyle(node);
      const transform = style.transform === 'none' ? '' : style.transform;

      return {
        duration: 200,
        easing: quintOut,
        css: (t) => `
					transform: ${transform} translateY(-${100 - t * 100}px);
					opacity: ${t}
				`,
      };
    },
  });

  let data = [1, 2, 3, 4].map(makeEl);

  const addAtIndex = (index: number, value: number) => {
    data = produce(data, (draft) => {
      draft.splice(index, 0, makeEl(value));
    });
  };

  const removeAtIndex = (index: number) => {
    data = produce(data, (draft) => {
      draft.splice(index, 1);
    });
  };
</script>

<main>
  <h1>Vite + Svelte</h1>

  <div style="display: flex;">
    <p class="label">index:&nbsp;</p>
    <input type="number" bind:value={index} />
  </div>

  <div style="display: flex;">
    <p class="label">value:&nbsp;</p>
    <input type="number" bind:value />
  </div>

  <button
    on:click={() => {
      addAtIndex(index, value);
      value = getRandomInt(0, 9);
    }}>add</button
  >

  <div class="container">
    {#each data as el, index (el.id)}
      <div
        class="box"
        on:click={() => {
          removeAtIndex(index);
        }}
        in:receive={{ key: el.id }}
        out:send={{ key: el.id }}
        animate:flip={{ duration: 200 }}
      >
        {el.value}
      </div>
    {/each}
  </div>
</main>

<style>
  .container {
    display: flex;
  }
  .box {
    width: 4em;
    height: 4em;
    box-sizing: border-box;
    border: 1px black solid;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .box:hover {
    background-color: pink;
    transition: background-color 200ms;
  }
  .label {
    margin: 0;
  }
</style>
