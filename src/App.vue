<template>
    <div id="app">
        <input @change="populatePalette" id="file-selector" type="file" />
        <textarea id="text" cols="30" rows="10"></textarea>
        <Words :words="palette"></Words>
    </div>
</template>

<script>
import Words from "./components/Words";
export default {
    name: "App",
    components: {
        Words
    },
    data() {
        return {
            palette: []
        };
    },
    methods: {
        populatePalette(ev) {
            const file = ev.target.files[0];
            const reader = new FileReader();

            reader.onload = e => (this.palette = e.target.result.split(" "));
            reader.readAsText(file);
        }
    }
};
</script>

<style></style>
