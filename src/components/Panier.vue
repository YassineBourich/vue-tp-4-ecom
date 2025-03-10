<template>
    <!-- Menu de recherche -->
	<section class="panier" :style="{height: screenHeight + 'px'}">
		<h3>Panier</h3>
        <div class="product" v-for="product in Panier">
			<div class="prod_cell">
				<img :src="product.image" alt="product.nom">
				<div class="info">
					<h4>{{product.nom}}</h4>
					<p class="price">Prix: {{product.prix}} $</p>
					<p :class="[product.disponible ? 'disponible' : 'nDisponible']">{{product.disponible ? "Disponible" : "Pas Disponible"}}</p>
				</div>
			</div>
		</div>
	</section>
</template>

<script>
import { eventBus } from "@/eventBus.js";

export default {
    name: "Panier",
    data() {
        return {
            screenHeight: window.innerHeight - 180,
            Panier: [],
        }
    },

    methods: {
        updateScreenHeight() {
            this.screenHeight = window.innerHeight - 180;
        }
    },
    
    mounted() {
        
        window.addEventListener("resize", this.updateScreenHeight);
        eventBus.on("cartLoaded", (data) => {
            this.Panier = data;
            console.log(this.Panier);
        });
    },
    beforeUnmount() {
        window.removeEventListener("resize", this.updateScreenHeight);
        eventBus.on("cartLoaded");
    },
}
</script>