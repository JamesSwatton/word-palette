<template>
    <div id="app">
        <input @change="populatePalette" id="file-selector" type="file" />
        <textarea v-model="textString" id="text" cols="30" rows="10"></textarea>
        <Words :words="palette"></Words>
    </div>
</template>

<script>
import { eventBus } from "./main";
import Words from "./components/Words";
export default {
    name: "App",
    components: {
        Words
    },
    data() {
        return {
            palette: [],
            textString: ""
        };
    },
    methods: {
        populatePalette(ev) {
            const file = ev.target.files[0];
            const reader = new FileReader();

            reader.onload = e => {
                this.palette = e.target.result.split(" ");
                this.persist("palette", JSON.stringify(this.palette))
            };
            reader.readAsText(file);
        },
        persist(key, value) {
            localStorage.setItem(key, value)
        },
        addWordToTextSting(word) {
            this.textString += ` ${word}`;
            console.log(this.textString);
        },
        listenForWord() {
            eventBus.$on("selectedWord", data => this.addWordToTextSting(data));
        },
        populatePaletteFromStorage() {
            if (localStorage.getItem("palette")) {
                try {
                    this.palette = JSON.parse(localStorage.getItem("palette"));
                } catch (e) {
                    localStorage.removeItem("palette");
                }
            }
        }
    },
    created() {
        this.populatePaletteFromStorage();
        this.listenForWord();
    }
};
</script>

<style></style>
