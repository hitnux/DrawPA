<template>
  <div id="app">
    <Sidebar></Sidebar>
    <div class="container">
      <Tabs>
        <div slot="homeTab" style="display: flex;">
            <div class="lineRange">
              <b-field label="Line Width">
                <b-input   
                  size="is-small" 
                  type="number"
                  min="1" max="100"
                  v-model="line"
                  :value="line">
              </b-input>
              </b-field>
              <b-slider type="is-info" v-model="line" :value="line" :min="1" size="is-small"></b-slider>
          </div>
          <div>
            <input type="color" v-model="colorPicker">
          </div>
            <b-button type="is-danger"
                icon-left="delete" @click="clearAll">
                Delete
            </b-button>
            <b-button type="is-danger"
                icon-left="delete" @click="erase">
                Eraser
            </b-button>
            <b-button type="is-info"
                icon-left="delete" @click="pen">
                Pen
            </b-button>
            <b-button type="is-success"
                icon-left="delete" @click="saveImg">
                Save
            </b-button>
            <b-button type="is-success"
                icon-left="delete" @click="findColor">
                Color finder
            </b-button>
        </div>
      </Tabs>
      <div class="canvasContainer">
        <canvas class="canvas" :height="canvasHeight" :width="canvasWidth"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './components/Sidebar.vue'
import Tabs from './components/Tabs.vue'

export default {
  name: 'app',
  components: {
    Sidebar,
    Tabs
  },
  data: function () {
    return {
      canvas: "",
      context: "",
      x: 0,
      y: 0,
      isMouseDown: false,
      canvasHeight: 600,
      canvasWidth: 900,
      line: 5,
      color: "#000",
      colorPicker: "",
      save: "",
      saveFile: "image.png",
    }
  },
  mounted(){
      this.canvas= document.getElementsByClassName('canvas')[0];
      this.context=this.canvas.getContext('2d');
      this.canvas.style.cursor = "url(http://www.rw-designer.com/cursor-extern.php?id=72974), auto";
      this.canvas.addEventListener( 'mousedown', this.startDrawing );
      this.canvas.addEventListener( 'mousemove', this.drawLine );
      this.canvas.addEventListener( 'mouseup', this.stopDrawing );
      this.canvas.addEventListener( 'mouseout', this.stopDrawing );
      this.context.lineWidth=this.line;
      this.context.strokeStyle=this.color;
    },
    methods: {
        stopDrawing(){ this.isMouseDown = false; },
        startDrawing(e){
                this.context.lineCap = 'round';
                this.isMouseDown = true;   
                [this.x, this.y] = [e.offsetX, e.offsetY];  
        },
        drawLine(e){
            if ( this.isMouseDown ) {
                const newX = e.offsetX;
                const newY = e.offsetY;
                this.context.beginPath();
                this.context.moveTo( this.x, this.y );
                this.context.lineTo( newX, newY );
                this.context.stroke();
                [this.x, this.y] = [newX, newY];
            }
        },
        clearAll(){
          this.context.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
        },
        erase(){
          this.oldColor=this.color;
          this.color="#fff";
          this.canvas.style.cursor = "url(http://www.rw-designer.com/cursor-extern.php?id=72976), auto";
        },
        pen(){
          this.color=this.oldColor;
          this.canvas.style.cursor = "url(http://www.rw-designer.com/cursor-extern.php?id=72974), auto";
        },
        saveImg(){
          const fs = require('fs');
          const url = this.canvas.toDataURL('image/jpg', 0.8);

          // remove Base64 stuff from the Image
          const base64Data = url.replace(/^data:image\/png;base64,/, "");
          fs.writeFile(__dirname, base64Data);
        },
        findColor(){
            this.canvas.style.cursor = "crosshair";
            this.canvas.removeEventListener( 'mousedown', this.startDrawing );
            this.canvas.addEventListener('mousedown', this.finder);
        },
        finder(e) {
            let x = e.layerX;
            let y = e.layerY;
            let pixel = this.context.getImageData(x, y, 1, 1);
            let data = pixel.data;
            this.colorPicker="#"+this.rgba2hex(data);
            this.canvas.removeEventListener( 'mousedown', this.finder );
            this.canvas.addEventListener('mousedown', this.startDrawing);
            this.pen();
        },
        rgba2hex(rgb) {
            return rgb ? 
                (rgb[0] | 1 << 8).toString(16).slice(1) +
                (rgb[1] | 1 << 8).toString(16).slice(1) +
                (rgb[2] | 1 << 8).toString(16).slice(1) : rgb;
        }
    },
    watch: {
      'line': function () {
        this.context.lineWidth=this.line;
      },
      'colorPicker': function(){
        this.color=this.colorPicker;
      },
      'color' : function () {
        this.context.strokeStyle=this.color;
      }
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  /*color: #2c3e50;*/
  background: #222;
  height: 100vh;
  display: flex;
}
:root{
  --tabs-background: #222;
  --tabs-color: #aaa;
  --tabs-active: #fff;
}
.canvasContainer{
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}
.canvas{
  display: block;
  background: #fff;
  border: solid 1px blue;
}
</style>
<style lang="scss">
// Import Bulma's core
@import "~bulma/sass/utilities/_all";

// Set your colors
$primary: #8c67ef;
$primary-invert: findColorInvert($primary);
$twitter: #4099FF;
$twitter-invert: findColorInvert($twitter);

// Setup $colors to use as bulma classes (e.g. 'is-twitter')
$colors: (
    "white": ($white, $black),
    "black": ($black, $white),
    "light": ($light, $light-invert),
    "dark": ($dark, $dark-invert),
    "primary": ($primary, $primary-invert),
    "info": ($info, $info-invert),
    "success": ($success, $success-invert),
    "warning": ($warning, $warning-invert),
    "danger": ($danger, $danger-invert),
    "twitter": ($twitter, $twitter-invert)
);

// Links
$link: $primary;
$link-invert: $primary-invert;
$link-focus-border: $primary;

// Import Bulma and Buefy styles
@import "~bulma";
@import "~buefy/src/scss/buefy";
</style>
