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
onMount(() => {
});
</script>

<main>
    <header class="sticky z-50 top-0">
        <nav class="bg-gray-500 border-gray-200">
            <div class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto p-4">
                <button on:click={() => navigate('/')} class="flex items-center space-x-3 rtl:space-x-reverse">
                    <img src="/online-shop.png" class="h-8" alt="Flowbite Logo" />
                    <span class="self-center text-2xl font-semibold whitespace-nowrap text-white">SwiftCart</span>
                </button>
                
</main>

<style>

</style>