<template>
  <div id="canvas"></div>
</template>

<script>
import P5 from 'p5';
export default {
  title: 'Conic Section',
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
      let slider, slider2, a, d, E, F;
      // let line_2d_s = []
      // let line_2d_t = []
      let squareSize = 450;

      p5.setup = () =>{
        p5.createCanvas(700, 500, p5.WEBGL);
        createAllSliders();
        p5.angleMode( p5.RADIANS );
      //   assign inital values
        d = convertSlider2(slider);
        a = p5.radians(slider2.value());
        p5.camera(0, 0, 600);
      }

      p5.draw = ()=> {
        p5.background(255);
        p5.lights();
        p5.normalMaterial();
        p5.rectMode(p5.CENTER);
        p5.orbitControl();

        //
        d = convertSlider2(slider);
        a = p5.radians(slider2.value());


        p5.push();
        p5.translate(0, slider.value(), 0);
        p5.rotateX(convertSlider(slider2));
        p5.ambientMaterial(212,239,252);
        p5.square(0, 0, squareSize);
        p5.pop();
        p5.stroke(126);
        // p5.line(0, 0, 240, 0);
        // p5.line(0, 0, 0, -240);
        p5.stroke(255);
        // p5.line(0, 0, -240, 0);
        // p5.line(0, 0, 0, 240);
        p5.push();
        p5.translate(0, -75);
        p5.ambientMaterial(0,62,116);
        p5.cone(150, 150, 24);
        p5.pop();
        p5.push();
        p5.translate(0, 75);
        p5.angleMode(p5.DEGREES);
        p5.rotateZ(180);
        p5.ambientMaterial(0,62,116);
        p5.cone(150, 150, 24);
        p5.pop();
      }

      function convertSlider(s) {
        return s.value() - 90
      }

      function convertSlider2(sl) {
        return sl.value()/150
      }

      function createAllSliders() {
        slider = p5.createSlider(-150, 150, 0, 1);
        slider.position(10, 10);
        slider.style('width', '80px');
        slider.addClass('styleSlider');
        slider.input(resetPoints);

        slider2 = p5.createSlider(-90, 90, 10, 1);
        slider2.position(10, 30);
        slider2.style('width', '80px');
        slider2.addClass('styleSlider');
        slider2.input(resetPoints);

      }

      const resetPoints = () => {
        this.line_2d_s = [];
        this.line_2d_t = [];
        this.aLabeldeg = p5.degrees(a);
        this.dLabeldeg = d;
        this.aLabelrad = a;
        this.dLabelrad = d;
        let i;
        if (a != 0 && d != 0 && a/d<0){
          for (i = -250; i < 250; i++){
            this.line_2d_t.push(p5.degrees(l_t( p5.abs(a), p5.abs(d), i)));
            this.line_2d_s.push(-p5.degrees(l_s( p5.abs(a), p5.abs(d), i)));
          }
        } else {
          for (i = -250; i < 250; i++){
            this.line_2d_t.push(p5.degrees(l_t( p5.abs(a), p5.abs(d), i)));
            this.line_2d_s.push(-p5.degrees(l_s( p5.abs(a), p5.abs(d), i)));
          }
        }

        if(p5.abs(a) >= p5.PI/4 && d == 0){
          for (i = -250; i < 250; i++){
            this.line_2d_t.push(-p5.degrees(l_t( p5.abs(a), p5.abs(d), i)));
            this.line_2d_s.push(p5.degrees(l_s( p5.abs(a), p5.abs(d), i)));
          }
        }


        this.sendPoints();
      }

      function A(x,X) {

        return (2**0.5)*X/p5.sin(p5.pi/4 + x)
      }

      function C(x,X) {
        F = (X/(2**0.5 + 1))*(p5.sin(p5.PI/2 - x)/p5.cos(x))
        return A(x,X) + F
      }
      function l_t(x, X, alpha) {
        if (X==0 && 0<= x && x<p5.PI/4){
          return 0
        } else if (X == 0 && x >= p5.PI/4){
          return alpha
        } else if (X>0 &&  0 <= x && x < p5.PI/4){
          return X*p5.cos(alpha)
        } else if (X>0 && x == p5.PI/4){
          return alpha
        } else if (X>0 && x > p5.PI/4){
            E = C(x,X)/A(x,X)
            return p5.cos(alpha)*(E*C(x,X)/(1-E*p5.sin(alpha)))
        }
      }

      function l_s(x, X, alpha){
        if (X == 0 && x < p5.PI/4 && p5.PI/4 == 0){
          return 0
        } else if (X == 0 && x >= p5.PI/4){
          return (1/p5.sin(-p5.PI/4 + x + 0.01))*alpha
        } else if (X>0 && 0 <= x && x < p5.PI/4){
          let r1 = X*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 + x))
          let r2 = X*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 - x))
          return 0.5*(r1 - r2 + (r1 + r2)*p5.sin(alpha))
        } else if (X>0 && x == p5.PI/4){
          return -0.5*X**(-1)*alpha**2
        } else if (X>0 && x> p5.PI/4){
          E = C(x,X)/A(x,X)
          return p5.sin(alpha)*(E*C(x,X)/(1-E*p5.sin(alpha)))
        }
      }


      // function l_t(alpha) {
      //   if (d == 0 && a == 0) {
      //     return 0
      //   } else if (d == 0 && a != 0) {
      //     return d * p5.cos(alpha)
      //   } else if (0 <= a && a < p5.PI / 4) {
      //     return d * p5.cos(alpha)
      //   } else if (a == p5.PI / 4) {
      //     return alpha
      //   } else {
      //     A = 0.5 * d * (-1 / (S + C) - p5.sin(p5.PI / 4) / p5.sin(p5.PI / 4 + a));
      //     c = p5.sin(p5.PI / 4) / p5.sin(p5.PI / 4 + a) + A
      //     E = c / A
      //     return p5.cos(alpha) * (A / (1 - E * p5.sin(alpha)))
      //   }
      // }

      // function l_s(alpha){
      //   if(d==0 && a==0){
      //     return 0
      //   } else if ( d==0 && a!=0){
      //     return alpha
      //   } else if (0<= a && a< p5.PI/4){
      //     let r1 = d*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 + a))
      //     let r2 = d*p5.sin(p5.PI/4)/(p5.sin(p5.PI/4 - a))
      //     return -r1 + r2 + 0.5*(r1 + r2)*p5.sin(alpha)
      //   } else if (a == p5.PI/4 ) {
      //     return alpha**2
      //   } else {
      //     A = 0.5*d*(-1/(S+C) -p5.sin(p5.PI/4)/p5.sin(p5.PI/4 + a));
      //     c = p5.sin(p5.PI/4)/p5.sin(p5.PI/4 + a) + A
      //     E = c/A
      //     return p5.sin(alpha)*(A/(1-E*p5.sin(alpha)));

        // }
      // }

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
.styleSlider {
  -webkit-appearance: none;
  width: 100%;
  height: 10px;
  border-radius: 5px;
    background: #002147;
  outline: none;
  opacity: 0.7;
  -webkit-transition: .2s;
  transition: opacity .2s;
}

.styleSlider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 15px;
  height: 15px;
  border-radius: 50%;

  background: #0091D4;
  cursor: pointer;
}


</style>
