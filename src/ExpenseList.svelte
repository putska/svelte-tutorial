<script>
    import {blur, fly, fade} from 'svelte/transition';
    import { flip } from 'svelte/animate';
    import SectionTitle from "./Title.svelte";
    import Expense from "./Expense.svelte";
    export let expenses
</script>
<style>
    h2{
        text-transform: capitalize;
    }
</style>
<section>
    <SectionTitle title="expense list" />
    <ul>
        {#each expenses as expense,index (expense.id)}
        <div in:fly={{x:200, delay:index*700}} out:fly={{ x: -200}} animate:flip>
            <Expense {...expense}  />
        </div>
            
        {:else}
        <h2>
            Currently you have no expenses
        </h2>
        {/each}

    </ul>

</section>