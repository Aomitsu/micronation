<template>
  <div
    class="flex flex-col h-screen pt-20 text-center prose-montserrat"
  >
    <div class="">
        Nombre total de Pixels : {{ totalPixels }}
        <div v-if="colorsArray" v-for="key in colorsSort" :key="key.color">
          <span :style="'color: '+ key">■</span> {{ key }} : {{ colorsArray[key] }} ( {{ Number(colorsArray[key] / colorsSort.length * 100).toFixed(3) }} % )
        </div>
    </div>
  </div>
</template>

<script>

let flagPixels = new Array();

export default {
    data() {
        return {
          totalPixels: null,
          colorsSort: [],
          colorsArray: [],
        }
    },
    methods: {
      async FetchMap() {
        // console.log("Fetching the whole map");
        return await fetch(`${process.env.apiUrl}/flag`, {
          method: "GET",
          crossDomain: true,
          headers: {
            "Content-Type": "application/json",
          },
        })
          .then((response) => response.json())
          .then((data) => {
            // console.log("DEBUG - New map array : ", data);

            data = data.filter(p => !!p)
              .sort((a, b) => (a.indexInflag - b.indexInFlag));

            flagPixels = data;
            return flagPixels;
          })
          // .catch((error) => console.log(error));
      },

      async getInfos() {
        this.totalPixels = flagPixels.length
        let colors = [];

        flagPixels.forEach(pixel => {
	        if (!pixel) return;

	        if (colors[pixel.hexColor]) {
		        colors[pixel.hexColor] = colors[pixel.hexColor] + 1;
	        } else {
		        colors[pixel.hexColor] = 1
	        }
        });

        this.colorsArray = colors
        this.colorsSort = Object.keys(colors).sort((a, b) => colors[b] - colors[a])

        console.log({
          colorsArray: this.colorsArray,
          colors
        })
      }
    
    },
    async mounted() {
      await this.FetchMap();
      await this.getInfos();
    }
};
</script>

<style>

a {
  color: darkred;
}

img {
  display: inline-block;
}

</style>
