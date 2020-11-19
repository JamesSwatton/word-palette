<template>
    <div id="words">
        <button
            @click="emitWord"
            v-for="(word, i) in words"
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
        emitWord(ev) {
            eventBus.$emit("selectedWord", ev.target.value);
        }
    }
};
</script>
