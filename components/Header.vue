<template>
    <div class="flex justify-between bg-gray-700 items-center px-10 h-20 fixed w-full z-50 shadow-lg">
        <ul class="navigation flex text-white uppercase text-xl">
            <nuxt-link to="/"><li class="pr-5">Magasin</li></nuxt-link>
            <nuxt-link to="collection"><li class="pr-10">Collection</li></nuxt-link>
            <li>Rejoindre une partie</li>
        </ul>
        <div class="flex profile text-white items-center">
            <div>Hugues</div>
            <img src="/faces/hugues.png" alt="" class="pl-8 w-auto h-12 block">
        </div>
    </div>
</template>

<style>
.nuxt-link-exact-active {
    @apply font-bold;
}
</style>
