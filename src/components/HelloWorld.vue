<template>
  <div id="canvas"></div>
</template>

<script>
import P5 from 'p5';
export default {
  name: 'Canvas',
  props: {
    onOff: Boolean,
  },
  data() {
    return {
      line_2d_s: [],
      line_2d_t: [],
      aLabeldeg: 0,
      dLabeldeg: 90,
      dLabelrad: 0,
      aLabelrad: 0,
    };
  },
  methods: {
    sendPoints() {
      this.$emit("sendPoints", [this.line_2d_s, this.line_2d_t, this.aLabeldeg, this.dLabeldeg, this.aLabelrad, this.dLabelrad, ]);
    }
  },
  mounted() {

    const sketch=(p5)=> {
      // These are your typical setup() and draw() methods
      /* eslint-disable no-param-reassign */
      let slider, slider2, a, d, C, S, A, c, E;
      // let line_2d_s = []
      // let line_2d_t = []
      let squareSize = 300;

      p5.setup = () =>{
        p5.createCanvas(500, 500, p5.WEBGL);
        createAllSliders();
        p5.angleMode( p5.RADIANS );
      //   assign inital values
        d = p5.radians(slider.value());
        a = p5.radians(slider2.value());
        C = p5.cos(a);
        S = p5.sin(a);
      }

      p5.draw = ()=> {
        p5.background(220);
        p5.normalMaterial();
        p5.rectMode(p5.CENTER);

        //
        d = p5.radians(slider.value());
        a = p5.radians(slider2.value());
        C = p5.cos(a);
        S = p5.sin(a);

        p5.push();
        p5.translate(0, slider.value(), 0);
        p5.rotateX(convertSlider(slider2));
        p5.square(0, 0, squareSize);
        p5.pop();
        p5.stroke(126);
        p5.line(0, 0, 240, 0);
        p5.line(0, 0, 0, -240);
        p5.stroke(255);
        p5.line(0, 0, -240, 0);
        p5.line(0, 0, 0, 240);
        p5.push();
        p5.translate(0, -75)
        p5.cone(70, 150, 24);
        p5.pop();
        p5.push();
        p5.translate(0, 75);
        p5.angleMode(p5.DEGREES);
        p5.rotateZ(180);
        p5.cone(70, 150, 24);
        p5.pop();
      }

      function convertSlider(sl) {
        return sl.value() - 90
      }

      function createAllSliders() {
        slider = p5.createSlider(-180, 180, 0, .01);
        slider.position(10, 10);
        slider.style('width', '80px');
        slider.input(resetPoints);

        slider2 = p5.createSlider(0, 180, 90, .01);
        slider2.position(10, 30);
        slider2.style('width', '80px');
        slider2.input(resetPoints);

      }

      const resetPoints = () => {
        this.line_2d_s = [];
        this.line_2d_t = [];
        this.aLabeldeg = p5.degrees(a);
        this.dLabeldeg = p5.degrees(d);
        this.aLabelrad = a;
        this.dLabelrad = d;
        let i;
        for (i = -250; i < 250; i++){
          this.line_2d_s.push(p5.degrees(l_s(i)));
          this.line_2d_t.push(p5.degrees(l_t(i)));
        }
        this.sendPoints();
      }


      function l_t(alpha) {
        if (d == 0 && a == 0) {
          return 0
        } else if (d == 0 && a != 0) {
          return d * p5.cos(alpha)
        } else if (0 <= a && a < p5.PI / 4) {
          return d * p5.cos(alpha)
        } else if (a == p5.PI / 4) {
          return alpha
        } else {
          A = 0.5 * d * (-1 / (S + C) - p5.sin(p5.PI / 4) / p5.sin(p5.PI / 4 + a));
          c = p5.sin(p5.PI / 4) / p5.sin(p5.PI / 4 + a) + A
          E = c / A
          return p5.cos(alpha) * (A / (1 - E * p5.sin(alpha)))
        }
      }

      function l_s(alpha){
        if(d==0 && a==0){
          return 0
        } else if ( d==0 && a!=0){
          return alpha
        } else if (0<= a && a< p5.PI/4){
          let r1 = d*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 + a))
          let r2 = d*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 - a))
          return -r1 + r2 + 0.5*(r1 + r2)*p5.sin(alpha)
        } else if (a == p5.PI/4 ) {
          return alpha**2
        } else {
          A = 0.5*d*(-1/(S+C) -p5.sin(p5.PI/4)/p5.sin(p5.PI/4 + a));
          c = p5.sin(p5.PI/4)/p5.sin(p5.PI/4 + a) + A
          E = c/A
          return p5.sin(alpha)*(A/(1-E*p5.sin(alpha)));

        }
      }

    }
    // Attach the canvas to the div
    /* eslint-disable no-new */
    new P5(sketch, 'canvas');
    this.$emit('incrementBy', 5);
  },
};
</script>

<style>
#canvas {

}
</style>
