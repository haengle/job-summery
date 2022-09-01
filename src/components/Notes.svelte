<script type="ts">
import Emoji from "./Emoji.svelte";
import { clickOutside } from "../lib/clickOutside.js"

export let notes;
export let toggled = false;

function closeToggles(event) {
    toggled = false;
}

</script>

<div class="notes relative" use:clickOutside on:click_outside={closeToggles}>
    <button class="toggle-btn" on:click={() => toggled = !toggled}>
        <Emoji symbol="🗒️" label="Notes"></Emoji>
    </button>
    {#if toggled}
    <div class="tooltip" class:active={toggled}>{notes}</div>
    {/if}
</div>

<style>
    .tooltip {
        @apply border border-gray-200 bg-white rounded-md p-2 text-xs w-32
        text-center z-50
        absolute -right-1/2 top-[calc(100%_+_.5rem)]
        after:w-0 after:h-0 after:-top-2
        after:absolute after:left-[72%] after:-translate-x-1/2
        after:border-solid after:border-transparent
        after:border-l-8 after:border-b-8 after:border-r-8
        after:border-b-gray-200;
    }

    .toggle-btn {
        @apply appearance-none p-2 bg-none
    }
</style>