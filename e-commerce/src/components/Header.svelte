<script>
import { onMount } from 'svelte';
import { navigate } from 'svelte-routing';
let navbarDropdown;
const toggleNavbar = () => {
    if (navbarDropdown.classList.contains('hidden')) {
        navbarDropdown.classList.remove('hidden');
    } else {
        navbarDropdown.classList.add('hidden');
    }
};
</script>


<main>
<h1>Header goes here</h1>
</main>

<style>

</style>