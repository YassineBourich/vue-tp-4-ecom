<template>
    <div class="fiches" :style="{height: screenHeight + 'px'}">
        
		<div class="product" v-for="product in applyFilter">
			<div class="prod_cell">
				<img :src="product.image" alt="product.nom">
				<div class="info">
					<h2>{{product.nom}}</h2>
					<p class="price">Prix: {{product.prix}} $</p>
					<p class="categ">Categorie: {{product.categorie}}</p>
					<p :class="[product.disponible ? 'disponible' : 'nDisponible']">{{product.disponible ? "Disponible" : "Pas Disponible"}}</p>
				</div>
                <button class="addBtn" v-on:click="ajouterAuPanier(product.id)">Ajouter au panier</button>
			</div>
		</div>
	</div>
</template>

<script>
import { eventBus } from "@/eventBus.js";

export default {
    name: "Fiches",
    data() {
        return {
            JSONLoadsuccess: false,
            imagesUrl: "../assets/images/",
            screenHeight: window.innerHeight - 165,
            products: [],
            Categories: [],
            filters: {
                pieceRecherche: "",
                selectedCategory: "All",
                disponibility: "All",
                trie: "None"
            },
            panier: []
        }
    },

    methods: {
        get_random_disponibility() {
            let r = Math.random();

            return r < (1/2);
        },
        updateScreenHeight() {
            this.screenHeight = window.innerHeight - 165;
            console.log(this.screenHeight);
        },

        getCategories() {
            let Categories = ["All"];
            this.products.forEach(item => {
                if(!Categories.includes(item.categorie)) {
                    Categories.push(item.categorie);
                }
            });

            return Categories;
        },

        sorting(List, ordre) {
            let r;
            if (ordre == "asc") {
                for (let i = 0; i < List.length; i++) {
                    for (let j = i + 1; j < List.length; j++) {
                        if (List[j].prix < List[i].prix) {
                            r = List[j];
                            List[j] = List[i];
                            List[i] = r;
                        }
                    }
                }
            } else if (ordre == "desc") {
                for (let i = 0; i < List.length; i++) {
                    for (let j = i + 1; j < List.length; j++) {
                        if (List[j].prix > List[i].prix) {
                            r = List[j];
                            List[j] = List[i];
                            List[i] = r;
                        }
                    }
                }
            }
        },

        filterCondition(item) {
            let cond1 = (this.filters.pieceRecherche === "" || item.nom.toLowerCase().includes(this.filters.pieceRecherche.toLowerCase()));
            let cond2 = (this.filters.selectedCategory == "All" || item.categorie == this.filters.selectedCategory);
            
            let dispKey = item.disponible ? "Disponible" : "Not Disponible";
            let cond3 = (this.filters.disponibility == "All" || dispKey == this.filters.disponibility);

            return cond1 && cond2 && cond3;
        },

        ajouterAuPanier(id) {
            if (this.JSONLoadsuccess) {
                this.panier.push(this.products[id - 1]);
                eventBus.emit("cartLoaded", this.panier);
            }
        }
    },

    computed: {

        applyFilter() {
            if (this.JSONLoadsuccess) {
                const filteredProducts = this.products.filter(this.filterCondition);

                this.sorting(filteredProducts, this.filters.trie);

                return filteredProducts;
            } else {
                return [];
            }
        }
    },

    beforeMount() {
        fetch("pieces-autos.json")
        .then(response => response.json())
        .then(data => {
            this.products = data;
            this.JSONLoadsuccess = true;
            this.products.forEach((item, idx) => {
                this.products[idx]["image"] = require(`@/assets/images/${item.nom}.jpg`);
                this.products[idx]["disponible"] = this.get_random_disponibility();
            });

            this.Categories = this.getCategories();

            eventBus.emit("categoriesLoaded", this.Categories);
        })
        .catch(err => console.log(err));
    },
    
    mounted() {
        
        window.addEventListener("resize", this.updateScreenHeight);
        eventBus.on("filtersUpdated", (newFilters) => {
            this.filters = newFilters;
        });

    },
    beforeUnmount() {
        window.removeEventListener("resize", this.updateScreenHeight);
        eventBus.off("filtersUpdated");
    },
}
</script>