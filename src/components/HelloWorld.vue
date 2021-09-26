<template>
  <div id="pixi_container"></div>
</template>

<script lang="ts">
import { defineComponent, onMounted } from "vue";
import * as PIXI from "pixi.js";

export default defineComponent({
  name: "HelloWorld",
  props: {
    msg: String,
  },
  setup() {
    const size = { width: 340, height: 240 };
    const pixiApp: PIXI.Application = new PIXI.Application(size);
    let textobj: PIXI.Text;
    onMounted(() => {
      console.log("mounted");
      const container = document.getElementById("pixi_container");
      if (container) container.appendChild(pixiApp.view);

      // テキストオブジェクトを作る
      textobj = new PIXI.Text("Hello Wordl!", {
        fontSize: 60,
        fontFamily: "Arial",
        fontWeight: "bold",
        // font: "bold 60pt Arial",
        fill: "white",
      });
      textobj.position.x = size.width / 2;
      textobj.position.y = size.height / 2;

      pixiApp.stage.addChild(textobj);

      pixiApp.ticker.add((delta) => {
        textobj.rotation += 0.01;
      });
    });
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
