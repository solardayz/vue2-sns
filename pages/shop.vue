<template>
  <v-container>
    <v-row>
      <v-col
        v-for="product in products"
        :key="product.id"
        cols="12"
        sm="6"
        md="4"
      >
        <v-card>
          <v-img
            :src="product.image"
            height="200px"
            class="white--text align-end"
            gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
          >
            <v-card-title class="white--text">{{ product.name }}</v-card-title>
          </v-img>
          <v-card-subtitle>{{ product.price }}</v-card-subtitle>
          <v-card-text>{{ product.description }}</v-card-text>
          <v-card-actions>
            <v-btn color="primary">Add to Cart</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "Shopping",
  data() {
    return {
      products: [
        {
          id: 1,
          name: "Win1 Warrior Boxing Gloves",
          price: "$110.00 USD",
          description:
            "High quality boxing gloves designed for professional training.",
          image:
            "https://topboxer.com/cdn/shop/files/2Mvp7JAn_1_1080x.png?v=1712335452",
        },
        {
          id: 2,
          name: "Win1 Maroon Boxing Gloves",
          price: "$110.00 USD",
          description: "Durable and stylish maroon boxing gloves.",
          image:
            "https://topboxer.com/cdn/shop/products/old_school_1_1080x.jpg?v=1570922712",
        },
        {
          id: 3,
          name: "Alien Black Boxing Gloves",
          price: "$110.00 USD",
          description: "Sleek black boxing gloves with superior grip.",
          image:
            "https://topboxer.com/cdn/shop/products/old_school_red-removebg-preview_900x.png?v=1658896011",
        },
        {
          id: 4,
          name: "Alien Black Snake Boxing Gloves",
          price: "$110.00 USD",
          description: "Black snake design for an extra edge in the ring.",
          image:
            "https://topboxer.com/cdn/shop/products/metallic_gold-removebg-preview_900x.png?v=1663110200",
        },
        {
          id: 5,
          name: "Alien Black/Green Boxing Gloves",
          price: "$110.00 USD",
          description: "Black and green gloves with excellent padding.",
          image:
            "https://topboxer.com/cdn/shop/products/blackgold_900x.jpg?v=1560627638",
        },
      ],
    };
  },
};
</script>

<style scoped>
.v-card-title {
  font-size: 18px;
  font-weight: bold;
}
.v-card-subtitle {
  font-size: 16px;
  color: #888;
}
.v-card-text {
  font-size: 14px;
  color: #555;
}
</style>
