<template>
    <div id="words">
        <button v-for="(word, i) in words" :key="i" class="word">{{ word }}</button>
    </div>
</template>

<script>
export default {
    name: "Words",
    props: ['words']
}
</script>


