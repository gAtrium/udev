<template>
  <canvas ref="bgDots" class="bgDots"></canvas>
  <div id="maindiv">
    <div id="header">
        <img src="~/assets/logo-test.png" id="logoleft" style="margin-left:0; height: 100%;" alt=""/>
        <NuxtLink to="/">Home</NuxtLink>
        <NuxtLink to="/team ">meet_the_team</NuxtLink>
        <NuxtLink to="/bounce">our_sponsors</NuxtLink> 
        <NuxtLink to="/slide">announcements</NuxtLink>
        <div id="termcontainer">
          <HackerTyperText ref="_termref" id="term" fontSize="1" txt="$ TODO: term style nav using this box"/>
        </div>
    </div>
    <div id="NuxtPageContainer">
      <NuxtPage />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      canvas: null,
      ctx: null,
      dots: [],
      _totalarea: 0,
      termref: null,
      dotRadius: 5

    }
  },
  methods: {
    // function to set the size of the canvas and update the total area
    setCanvasSize() {
      this.canvas.width = window.innerWidth;
      this.canvas.height = window.innerHeight;
      this._totalarea = this.canvas.width * this.canvas.height;
      this.maxDistance = Math.sqrt(this._totalarea) / 7;
      console.log(this.maxDistance)
    },

    // initialize canvas and start animation
    init() {
      this.canvas = this.$refs.bgDots;
      console.log(this.canvas);
      this.ctx = this.canvas.getContext('2d');

      // set the initial size of the canvas
      this.setCanvasSize();

      // update the size of the canvas and max distance when the window is resized
      window.addEventListener('resize', () => {
        this.setCanvasSize();
      });

      // start animation
      this.animate();
    },

    // function to create a new dot object with random position and velocity
    createDot() {
      const x = Math.random() * this.canvas.width;
      const y = Math.random() * this.canvas.height;
      const vx = Math.random() * 2 - 1;
      const vy = Math.random() * 2 - 1;
      const alpha = 0;

      return { x, y, vx, vy, alpha };
    },

    updateDotAlpha(dot) {
      // increment the alpha value of the dot by a small amount
      dot.alpha += 0.01;

      // clamp the alpha value to the range [0, 1]
      if (dot.alpha > 1) {
        dot.alpha = 1;
      }
    },


    // function to draw a dot on the canvas
    drawDot(dot) {
      // calculate the distance of the dot from the nearest edge of the canvas
      const distanceFromEdge = Math.min(
        dot.x,
        this.canvas.width - dot.x,
        dot.y,
        this.canvas.height - dot.y
      );

      // calculate the alpha value of the dot based on its distance from the edge of the canvas
      const alpha = Math.max(0, distanceFromEdge / this.maxDistance);

      this.ctx.beginPath();
      this.ctx.arc(dot.x, dot.y, this.dotRadius, 0, 2 * Math.PI);
      this.ctx.fillStyle = `rgba(255, 255, 255, ${alpha * dot.alpha})`; // use the dot's alpha value and the calculated alpha value to set the fill style
      this.ctx.fill();
    },
    // function to connect two dots with a line
    connectDots(dot1, dot2, distance) {
      this.ctx.beginPath();
      this.ctx.moveTo(dot1.x, dot1.y);
      this.ctx.lineTo(dot2.x, dot2.y);

      this.ctx.strokeStyle = `rgba(255,255,255,${(1 - distance / this.maxDistance) * Math.min(dot1.alpha, dot2.alpha)})`;
      this.ctx.stroke();
    },

    // function to update the position of a dot based on its velocity
    updateDotPosition(dot) {
      dot.x += dot.vx;
      dot.y += dot.vy;

      // delete dot if it goes off the screen
      if (dot.x < 0 || dot.x > this.canvas.width || dot.y < 0 || dot.y > this.canvas.height) {
        this.dots = this.dots.filter(d => d !== dot);
      }
    },

    // function to calculate the distance between two dots
    calculateDistance(dot1, dot2) {
      const dx = dot1.x - dot2.x;
      const dy = dot1.y - dot2.y;
      return Math.sqrt(dx * dx + dy * dy);
    },

    // main animation loop
    animate() {
      // clear canvas
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

      // update positions of all dots
      for (const dot of this.dots) {
        this.updateDotPosition(dot);
        this.updateDotAlpha(dot);
      }

      // draw all dots and connect close dots
      for (let i = 0; i < this.dots.length; i++) {
        const dot1 = this.dots[i];
        this.drawDot(dot1);
        for (let j = i + 1; j < this.dots.length; j++) {
          const dot2 = this.dots[j];
          const distance = this.calculateDistance(dot1, dot2);
          if (distance < this.maxDistance) {
            this.connectDots(dot1, dot2, distance);
          }
        }
      }

      // add new dot if necessary
      if (this.dots.length < this._totalarea / (this.maxDistance * this.maxDistance)) {
        this.dots.push(this.createDot());
      }

      requestAnimationFrame(this.animate);
    }
  },
  mounted() {
    this.init();
    this.termref = this.$refs._termref;
    setInterval(()=> {
      var lol = ["Use this terminal as another way of navigation", "Adjust logo color scheme better", "Do actual functional pages", "Finish this thing"]
      //print(lol[Math.random() * lol.length])
      if(this.termref.isWriting() == 0) this.termref.writeNew(lol[Math.floor(Math.random() * lol.length)], "$ TODO: ".length) 
    }, Math.random() * 6000);
  },
  beforeMount() {
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.setCanvasSize);
  }
}
</script>

<style>
#maindiv {
  position: fixed;
  display: flex;
  scrollbar-width: none;  /* Firefox */
  overflow: scroll;
  background-color: black;
  color: white;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  margin: 0;
  padding: 0;
  align-items: flex-start;
  flex-direction: column;
  z-index: 2;
}
#maindiv::-webkit-scrollbar { 
    display: none;  /* Safari and Chrome */
}

.bgDots {
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  z-index: 3;
  position: fixed;
  opacity: 0.6;
  pointer-events: none;
}

#header {
  width: 100%;
  height: 4em;
  border-bottom:1px;
  border-bottom-style: solid;
  display: flex;
  align-items: center;
  font-size: 2rem;
  justify-content: flex-start;

}

#header a::before {
  content: "./";
}
#header a {
  margin-left: 1em;
  font-family: monospace;
  color: white;
}

#header a:link,
#header a:visited,
#header a:hover,
#header a:active {
  text-decoration: none;
}

#header img {
  padding-left: 10px;
}

#imgcontainer {
  width: 100%;
  height: 5%;
  justify-self: flex-start;
  margin-left: 50px;
  border-left-color: white;
  border-left-width: 1px;
  border-left-style: solid;
}
#logoleft {
  display:flex;
  align-self: flex-start;
  margin-left: 0;
}

#termcontainer {
  border-left-style: solid;
  border-left-width: 1px;
  margin-left: 1em;
  width: fit-content;
  height: fit-content;
  align-content: center;
  justify-content: center;
  overflow-x: scroll;
  scrollbar-width: none;
}
#termcontainer p {
  margin-left: 20px;
}
#termcontainer::-webkit-scrollbar{
  display: none;
  width: 0;
}

#NuxtPageContainer {
  margin-top: 0;
  position: relative;
  width: 100%;
  height: calc(100% - 4em);
}

</style>