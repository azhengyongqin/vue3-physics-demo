<template>
  <div id="world"></div>
</template>

<script>
import Matter, {Engine, Render, Runner, Bodies, World, Events, Mouse} from "matter-js";
import circleImageSrc from "@/assets/chrome.svg";
import {onMounted} from "vue";

export default {
  name: "App",
  setup() {
    onMounted(() => {
      // Create engine
      const engine = Engine.create();
      const world = engine.world;

      const containerWidth = 278; // 容器宽度
      const containerHeight = 240; // 容器高度

      const circleWidth = 56; // 圆直径
      const imageWidth = 24; // 图片宽度


      // Render
      const render = Render.create({
        element: document.getElementById('world'),
        engine: engine,
        options: {
          width: 278,
          height: 240,
          wireframes: false,
          background: 'transparent',
          pixelRatio: window.devicePixelRatio
        }
      });

      Render.run(render);

      // Create ground without tilt
      const wallOption = {
        isStatic: true,
        render: {
          fillStyle: 'transparent',
          strokeStyle: 'transparent'
        }
      };
      const ground = Bodies.rectangle(containerWidth / 2, containerHeight, containerWidth, 6, wallOption);
      World.add(world, ground);

      // Walls without tilt
      const leftWall = Bodies.rectangle(0, containerHeight / 2, 2, containerHeight, wallOption);
      const rightWall = Bodies.rectangle(containerWidth - 2, containerHeight / 2, 2, containerHeight, wallOption);
      World.add(world, [leftWall, rightWall]);


      // Add circles using screen refresh rate
      let circleIndex = 0;

      function addCircleOnAnimationFrame() {
        if (circleIndex < 8) {
          const circle = Bodies.circle(
              Math.random() * (containerWidth - circleWidth) + circleWidth / 2,
              0,
              circleWidth / 2,
              {
                render: {
                  fillStyle: "#e1e1e1",
                  strokeStyle: "#e1e1e1",
                  lineWidth: 1,
                  sprite: {
                    texture: circleImageSrc,
                    xScale: circleWidth / imageWidth,
                    yScale: circleWidth / imageWidth
                  }
                }
              }
          );
          World.add(world, circle);

          circleIndex++;
          requestAnimationFrame(addCircleOnAnimationFrame);
        }
      }

      requestAnimationFrame(addCircleOnAnimationFrame);

      // TODO 添加点击事件
      const mouse = Mouse.create(render.canvas);
      Events.on(mouse, 'mousedown', (event) => {
        const bodies = Matter.Query.point(world.bodies, event.mouse.position);
        bodies.forEach(body => {
          if (body.circleRadius) {
            body.render.fillStyle = "black";
            console.log(event.mouse.position)
          }
        });
      });

      // Run engine
      Runner.run(engine)
    });

    return {};
  }
};
</script>

<style>
#world {
  width: 278px;
  height: 240px;
  position: relative;
  border: 1px solid #CACED4;
  overflow: hidden;
}
</style>
