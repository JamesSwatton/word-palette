<template>
    <div id="words">
        <button
            v-for="(word, i) in words"
            @click="emitWord($event, i)"
            :key="i"
            class="word"
            :value="word"
        >
            {{ word }}
        </button>
    </div>
</template>

<script>
import { eventBus } from "../main";
export default {
    name: "Words",
    props: ["words"],
    methods: {
        emitWord(ev, i) {
            let word = ev.target.value;
            eventBus.$emit("selectedWord", {word: word, index: i});
            
        },
    }
};
</script>
