---
name: algorithmic-art
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Algorithmic Art

Create algorithmic art using p5.js with seeded randomness and interactive parameter exploration.

## Capabilities

- Generate flow fields and particle systems
- Create recursive patterns and fractals
- Build interactive visual experiments
- Use seeded randomness for reproducibility
- Export high-resolution images

## p5.js Template

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
</head>
<body>
<script>
// Seed for reproducibility
const SEED = 12345;

function setup() {
  createCanvas(800, 600);
  randomSeed(SEED);
  noiseSeed(SEED);
  noLoop();
}

function draw() {
  background(20);
  // Your art code here
}

function keyPressed() {
  if (key === 's') {
    saveCanvas('artwork', 'png');
  }
}
</script>
</body>
</html>
```

## Common Patterns

### Flow Field
```javascript
let cols, rows;
let scale = 20;
let particles = [];
let flowfield;

function setup() {
  createCanvas(800, 600);
  cols = floor(width / scale);
  rows = floor(height / scale);
  flowfield = new Array(cols * rows);

  for (let i = 0; i < 1000; i++) {
    particles.push(new Particle());
  }
  background(20);
}

function draw() {
  let yoff = 0;
  for (let y = 0; y < rows; y++) {
    let xoff = 0;
    for (let x = 0; x < cols; x++) {
      let index = x + y * cols;
      let angle = noise(xoff, yoff) * TWO_PI * 2;
      let v = p5.Vector.fromAngle(angle);
      v.setMag(1);
      flowfield[index] = v;
      xoff += 0.1;
    }
    yoff += 0.1;
  }

  for (let particle of particles) {
    particle.follow(flowfield);
    particle.update();
    particle.edges();
    particle.show();
  }
}

class Particle {
  constructor() {
    this.pos = createVector(random(width), random(height));
    this.vel = createVector(0, 0);
    this.acc = createVector(0, 0);
    this.maxspeed = 2;
    this.prevPos = this.pos.copy();
  }

  follow(vectors) {
    let x = floor(this.pos.x / scale);
    let y = floor(this.pos.y / scale);
    let index = x + y * cols;
    let force = vectors[index];
    this.applyForce(force);
  }

  applyForce(force) {
    this.acc.add(force);
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxspeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }

  show() {
    stroke(255, 5);
    strokeWeight(1);
    line(this.pos.x, this.pos.y, this.prevPos.x, this.prevPos.y);
    this.prevPos = this.pos.copy();
  }

  edges() {
    if (this.pos.x > width) { this.pos.x = 0; this.prevPos = this.pos.copy(); }
    if (this.pos.x < 0) { this.pos.x = width; this.prevPos = this.pos.copy(); }
    if (this.pos.y > height) { this.pos.y = 0; this.prevPos = this.pos.copy(); }
    if (this.pos.y < 0) { this.pos.y = height; this.prevPos = this.pos.copy(); }
  }
}
```

### Recursive Tree
```javascript
function setup() {
  createCanvas(600, 600);
  background(20);
  stroke(255);
  translate(width/2, height);
  branch(120);
}

function branch(len) {
  strokeWeight(map(len, 10, 120, 1, 8));
  line(0, 0, 0, -len);
  translate(0, -len);

  if (len > 10) {
    push();
    rotate(PI/6 + random(-0.1, 0.1));
    branch(len * 0.7);
    pop();

    push();
    rotate(-PI/6 + random(-0.1, 0.1));
    branch(len * 0.7);
    pop();
  }
}
```

### Circle Packing
```javascript
let circles = [];
let maxAttempts = 1000;

function setup() {
  createCanvas(600, 600);
  background(20);
  noLoop();
}

function draw() {
  for (let i = 0; i < 500; i++) {
    addCircle();
  }

  for (let c of circles) {
    noFill();
    stroke(random(200, 255), random(100, 200), random(150, 255));
    strokeWeight(2);
    ellipse(c.x, c.y, c.r * 2);
  }
}

function addCircle() {
  for (let attempts = 0; attempts < maxAttempts; attempts++) {
    let x = random(width);
    let y = random(height);
    let r = random(5, 50);

    let valid = true;
    for (let c of circles) {
      let d = dist(x, y, c.x, c.y);
      if (d < r + c.r + 2) {
        valid = false;
        break;
      }
    }

    if (valid) {
      circles.push({x, y, r});
      return;
    }
  }
}
```

### Noise Landscape
```javascript
let cols, rows;
let scl = 20;
let w = 1400;
let h = 1000;
let flying = 0;
let terrain = [];

function setup() {
  createCanvas(600, 600, WEBGL);
  cols = w / scl;
  rows = h / scl;

  for (let x = 0; x < cols; x++) {
    terrain[x] = [];
  }
}

function draw() {
  flying -= 0.05;
  let yoff = flying;

  for (let y = 0; y < rows; y++) {
    let xoff = 0;
    for (let x = 0; x < cols; x++) {
      terrain[x][y] = map(noise(xoff, yoff), 0, 1, -100, 100);
      xoff += 0.1;
    }
    yoff += 0.1;
  }

  background(0);
  stroke(255);
  noFill();

  translate(0, 50);
  rotateX(PI/3);
  translate(-w/2, -h/2);

  for (let y = 0; y < rows - 1; y++) {
    beginShape(TRIANGLE_STRIP);
    for (let x = 0; x < cols; x++) {
      vertex(x * scl, y * scl, terrain[x][y]);
      vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);
    }
    endShape();
  }
}
```

## Color Palettes for Generative Art

```javascript
// Palette 1: Sunset
const sunset = ['#FF6B6B', '#FEC89A', '#FFD93D', '#6BCB77', '#4D96FF'];

// Palette 2: Ocean
const ocean = ['#0F4C75', '#3282B8', '#BBE1FA', '#1B262C', '#00A8CC'];

// Palette 3: Forest
const forest = ['#1A4314', '#4E9F3D', '#8FD14F', '#F0EBE3', '#3B6064'];

// Usage
function getRandomColor(palette) {
  return palette[floor(random(palette.length))];
}
```

## Best Practices

1. **Use seeds** - randomSeed() and noiseSeed() for reproducibility
2. **Layer effects** - Build complexity through layering
3. **Limit iterations** - Balance detail with performance
4. **Export high-res** - Use pixelDensity(2) or higher
5. **Document parameters** - Comment what each value controls
6. **Create variations** - Change seed to explore possibilities

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
