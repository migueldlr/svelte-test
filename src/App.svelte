<script lang="ts">
  import produce from 'immer';
  import { quintOut } from 'svelte/easing';
  import { crossfade } from 'svelte/transition';
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

  let data = [1, 2, 3, 4].map(makeEl);

  const addAtIndex = (index: number, value: number) => {
    data = produce(data, (draft) => {
      draft.splice(index, 0, makeEl(value));
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
    {#each data as el}
      <div class="box">{el.value}</div>
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
  .label {
    margin: 0;
  }
</style>
