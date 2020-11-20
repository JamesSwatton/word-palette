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
import Stopwords from "./helpers/stopwords.json";
//const stopwords = JSON.parse(Stopwords)
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
                const cleanWords = this.createWords(e.target.result);
                this.palette = cleanWords;
                this.persist("palette", JSON.stringify(this.palette));
            };
            reader.readAsText(file);
        },
        persist(key, value) {
            localStorage.setItem(key, value);
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
        },
        createWords(string) {
            console.log(Stopwords.stopwords);
            let cleanWords = string
                .replace(/\s+/g, " ")
                .split(" ")
                .map(word => {
                    const match = word.match(/\w+\S?\w+|\w/);
                    if (match) {
                        if (
                            Stopwords.stopwords.includes(match[0].toLowerCase())
                        ) {
                            return match[0];
                        } else if (/'s/.test(match[0])) {
                            return match[0].replace(/'s/, "");
                        } else {
                            return match[0];
                        }
                    }
                });
            return cleanWords;
        }
    },
    created() {
        this.populatePaletteFromStorage();
        this.listenForWord();
    }
};
</script>

<style></style>
