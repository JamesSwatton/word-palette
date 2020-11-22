<template>
    <div id="app">
        <input @change="populatePalette" id="file-selector" type="file" />
        <textarea v-model="textString" id="text" cols="30" rows="10"></textarea>
        <button @click="reshuffle" id="shuffle">Shuffle</button>
        <Words :words="shuffledPalette"></Words>
    </div>
</template>

<script>
import { eventBus } from "./main";
import Words from "./components/Words";
import Stopwords from "./helpers/stopwords.json";
export default {
    name: "App",
    components: {
        Words
    },
    data() {
        return {
            palette: [],
            shuffledPalette: [],
            textString: ""
        };
    },
    methods: {
        populatePalette(ev) {
            const file = ev.target.files[0];
            const reader = new FileReader();

            reader.onload = e => {
                const cleanWords = this.createWords(e.target.result);
                const shuffleWords = this.shuffleWords(cleanWords);
                this.palette = cleanWords;
                this.shuffledPalette = shuffleWords;
                this.persist("palette", JSON.stringify(this.palette));
            };
            reader.readAsText(file);
        },
        persist(key, value) {
            localStorage.setItem(key, value);
        },
        addWordToTextSting(word) {
            this.textString += ` ${word}`;
        },
        listenForWord() {
            eventBus.$on("selectedWord", data => {
                this.addWordToTextSting(data.word);
                this.removeWord(this.shuffledPalette, data.index);
            });
        },
        populatePaletteFromStorage() {
            if (localStorage.getItem("palette")) {
                try {
                    this.palette = JSON.parse(localStorage.getItem("palette"));
                    this.shuffledPalette = this.shuffleWords(this.palette);
                } catch (e) {
                    localStorage.removeItem("palette");
                }
            }
        },
        createWords(string) {
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
                })
                .slice(0, -1);
            return cleanWords;
        },
        shuffleWords(words) {
            let w = [...words],
                m = w.length,
                t,
                i;
            while (m) {
                i = Math.floor(Math.random() * m--);
                t = w[m];
                w[m] = w[i];
                w[i] = t;
            }
            return w;
        },
        reshuffle() {
            this.shuffledPalette = this.shuffleWords(this.palette);
        },
        removeWord(array, i) {
            array.splice(i, 1);
        }
    },
    created() {
        this.populatePaletteFromStorage();
        this.listenForWord();
    }
};
</script>

<style></style>
