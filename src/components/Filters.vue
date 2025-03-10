<template>
    <!-- Menu de recherche -->
	<section class="filtres" :style="{height: screenHeight + 'px'}">
		<h3>Filtres</h3>
        <form action="">
            <input id="txtSearch" type="text" placeholder="Recherche d'une piece ..." v-model="pieceRecherche">
            <div class="sel">
                <label for="categorie">Categorie</label>
                <select name="categorie" id="categorie" v-model="selectedCategory">
                    <option v-for="cat in Categories" :value="cat">{{ cat }}</option>
                </select>
            </div>
            <br>
            <div class="sel">
                <label for="disponibility">Disponibilitée</label>
                <select name="disponibility" id="disponibility" v-model="disponibility">
                    <option value="All">All</option>
                    <option value="Disponible">Disponible</option>
                    <option value="Not Disponible">Not Disponible</option>
                </select>
            </div>
            <br>
            <div class="sel">
                <label for="trie">Ordre de prix</label>
                <select name="trie" id="trie" v-model="trie">
                    <option value="None">None</option>
                    <option value="asc">Croissant</option>
                    <option value="desc">Decroissant</option>
                </select>
            </div>
        </form>
	</section>
</template>

<script>
import { eventBus } from "@/eventBus.js";

export default{
    name: "Filters",
    data() {
        return {
            pieceRecherche: "",
            Categories: [],
            selectedCategory: "All",
            disponibility: "All",
            trie: "None",
            screenHeight: window.innerHeight - 300,
        }
    },

    methods: {
        updateScreenHeight() {
            this.screenHeight = window.innerHeight - 300;
        },
        emitFilters() {
            eventBus.emit("filtersUpdated", {
                pieceRecherche: this.pieceRecherche,
                selectedCategory: this.selectedCategory,
                disponibility: this.disponibility,
                trie: this.trie
            });
        }
    },
    
    mounted() {
        
        window.addEventListener("resize", this.updateScreenHeight);
        eventBus.on("categoriesLoaded", (data) => {
            this.Categories = data;
        });

        this.$watch(
            () => [this.pieceRecherche, this.selectedCategory, this.disponibility, this.trie],
            this.emitFilters
        );
    },
    beforeUnmount() {
        window.removeEventListener("resize", this.updateScreenHeight);
        eventBus.off("categoriesLoaded");
    },
}
</script>