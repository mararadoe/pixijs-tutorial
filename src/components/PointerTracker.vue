<template>
  <div>
    <p>食べたお肉の数:{{ ateNum }}</p>
    <div id="pixi_container"></div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onBeforeMount, onMounted, ref } from "vue";
import * as PIXI from "pixi.js";
import boxIntersect from "box-intersect";

const createBox = (sprite: PIXI.Sprite): [number, number, number, number] => {
  return [
    sprite.position.x - sprite.width / 2,
    sprite.position.y - sprite.height / 2,
    sprite.position.x + sprite.width / 2,
    sprite.position.y + sprite.height / 2,
  ];
};

const randRange = (min: number, max: number) =>
  Math.floor(Math.random() * (max - min + 1) + min);

export default defineComponent({
  name: "PointerTracker",
  props: {
    msg: String,
  },
  setup() {
    const pixiApp = new PIXI.Application({
      antialias: true,
      autoDensity: true,
      backgroundColor: 0x1099bb,
      resolution: devicePixelRatio,
    });
    const ateNum = ref(0);
    const meatArr: PIXI.Sprite[] = [];

    onBeforeMount(() => {
      PIXI.utils.clearTextureCache();
    });

    onMounted(() => {
      console.log("mounted");
      const container = document.getElementById("pixi_container");
      if (container) container.appendChild(pixiApp.view);
      const dinosaur = PIXI.Sprite.from("./img/Tyrannosaurus.png");
      dinosaur.anchor.x = 0.5;
      dinosaur.anchor.y = 0.5;
      dinosaur.scale.set(200 / dinosaur.width);
      pixiApp.stage.addChild(dinosaur);

      dinosaur.position.set(
        pixiApp.renderer.screen.width / 2,
        pixiApp.renderer.screen.height / 2
      );

      pixiApp.stage.interactive = true;
      pixiApp.stage.hitArea = pixiApp.renderer.screen;
      pixiApp.stage.on("pointermove", (e: PIXI.InteractionEvent) => {
        dinosaur.position.copyFrom(e.data.global);
      });
      setInterval(() => {
        const boxes: [number, number, number, number][] = [];
        boxes.push(createBox(dinosaur));
        for (const meat of meatArr) {
          boxes.push(createBox(meat));
        }
        boxIntersect(boxes, (i, j) => {
          if (Math.min(i, j) === 0) {
            const meatIdx = Math.max(i, j);
            const eatens = meatArr.splice(meatIdx - 1, 1);
            for (const eaten of eatens) {
              pixiApp.stage.removeChild(eaten);
              ateNum.value++;
            }
          }
        });
      }, 100);

      setInterval(() => {
        if (meatArr.length >= 30) return;
        const meat = PIXI.Sprite.from("./img/niku_manga.png");
        meat.anchor.x = 0.5;
        meat.anchor.y = 0.5;
        meat.scale.set(100 / meat.width);
        meat.position.set(
          randRange(50, pixiApp.renderer.screen.width - 50),
          randRange(50, pixiApp.renderer.screen.height - 50)
        );
        pixiApp.stage.addChild(meat);
        meatArr.push(meat);
      }, 200)
    });
    return { ateNum };
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

