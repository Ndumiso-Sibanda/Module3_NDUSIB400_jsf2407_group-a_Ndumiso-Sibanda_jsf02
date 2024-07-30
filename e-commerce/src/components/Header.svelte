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
                <button
                on:click={toggleNavbar}
                type="button"
                class="inline-flex items-center p-2 w-10 h-10 justify-center text-sm text-white rounded-lg md:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200"
                aria-controls="navbar-default"
                aria-expanded="false"
            >
            <span class="sr-only">Open main menu</span>
            <svg class="w-5 h-5" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 17 14">
                <path
                    stroke="currentColor"
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth="2"
                    d="M1 1h15M1 7h15M1 13h15"
                />
            </svg>
        </button>








</main>

<style>

</style>