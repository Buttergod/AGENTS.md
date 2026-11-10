# AGENTS Guidelines
- Act as a world-class Senior Web Developer with extensive full-stack experience. Your goal is to construct an exceptional, - production-ready website that exceeds my original requirements.
- Write clean, robust, and completely mistake-free code.
- Elevate my specifications by applying top-tier UI/UX, web security, performance optimization, and accessibility best practices.
- Proactively identify edge cases, refine architecture, and deliver optimal, highly scalable solutions."

```
+-------------------------------------------------------+
|  [ Search (/) ] - Page 1 - Page 2 - Page 3  [ Logo ]  |
|        Home   About   Services   Contact   [CTA]      |
|_______________________________________________________|
|                         HEADER                        |
+-------------------------------------------------------+
|                     HERO SECTION                      |
|          Main Headline & Value Proposition            |
|                   [ Action Button ]                   |
+----------------------------------+--------------------+
|           MAIN CONTENT           |      SIDEBAR       |
|  - Primary Content / Features    |  - Search Bar      |
|  - Product Cards / Images        |  - Quick Links     |
+----------------------------------+--------------------+
|                        FOOTER                         |
| Privacy Policy | Terms of Service | Social | © 2026   |
+-------------------------------------------------------+
```

## 1. USER EXPERIENCE



## 2. USER INTERFACE



## 3. SECURITY



## 4. FUNCTIONS 



## 5. CODE
### Add the features listed below:

## Integrate the <DitherVeil /> component from React Bits

You are helping integrate an open-source React component into an existing application.

### Component: DitherVeil
### Variant: JavaScript + CSS
### Dependencies: ogl

---

### Usage Example
```jsx
import DitherVeil from './DitherVeil';

<div style={{ width: '100%', height: '600px', position: 'relative' }}>
  <DitherVeil
    src="https://images.unsplash.com/photo-1737071371043-761e02b1ef95?q=80&w=1400&auto=format&fit=crop"
    pattern="floyd"
    pixelSize={2}
    inkColor="#120f17"
    paperColor="#f4f1ea"
    revealRadius={200}
    softness={0.6}
    linger={1}
  />
</div>
```

### Props
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| src | string | 'https://images.unsplash.com/...' | Image to veil. Must be served with CORS headers. |
| fit | 'contain' | 'cover' | 'contain' | Show the whole image on the ink, or crop it to fill the container. |
| pattern | 'bayer' | 'noise' | 'atkinson' | 'floyd' | 'lines' | 'floyd' | How the image is broken into dots: an ordered Bayer grid, blue noise stipple, Atkinson or Floyd-Steinberg error diffusion, or diagonal engraving lines. |
| palette | 'duotone' | 'rgb' | 'duotone' | Duotone maps brightness between the ink and paper colours. RGB dithers each channel for a retro colour look. |
| pixelSize | number | 2 | Size of each dither cell, in px. |
| levels | number | 2 | Tones per channel. 2 is pure 1-bit, higher values add in-between shades. |
| inkColor | string | '#120f17' | Colour of the darkest tone. |
| paperColor | string | '#f4f1ea' | Colour of the lightest tone. |
| contrast | number | 1.15 | Tonal contrast applied before dithering. |
| brightness | number | 0 | Shifts the image lighter or darker before dithering. |
| revealRadius | number | 200 | Radius of the full-colour window around the cursor, in px. |
| softness | number | 0.6 | How much of the reveal edge dissolves through the dither. 0 is a hard cut. |
| linger | number | 1 | Seconds the revealed trail takes to knit back into dither. 0 turns the trail off. |
| rimColor | string | '#a78bfa' | Colour of the cells along the dissolving edge. |
| rim | number | 0 | Thickness of the coloured rim on the dissolving edge. 0 hides it. |
| reverse | boolean | false | Start in full colour and dither wherever the cursor goes. |
| wander | boolean | false | Let the reveal drift around on its own while the pointer is away. |
| clickBurst | boolean | true | Clicking sends a ring of colour rippling out across the image. |
| className | string | '' | Extra classes on the container. |
| style | CSSProperties | - | Inline styles on the container. |

### Full Component Source
```jsx
'use client';

import { useEffect, useRef } from 'react';
import { Renderer, Program, Mesh, Triangle, Texture, RenderTarget } from 'ogl';

import './DitherVeil.css';

const DEFAULT_SRC = 'https://images.unsplash.com/photo-1737071371043-761e02b1ef95?q=80&w=1400&auto=format&fit=crop';

const ORDERED = { bayer: 0, noise: 1, lines: 2 };

const KERNELS = {
  atkinson: [
    [1, 0, 1 / 8],
    [2, 0, 1 / 8],
    [-1, 1, 1 / 8],
    [0, 1, 1 / 8],
    [1, 1, 1 / 8],
    [0, 2, 1 / 8]
  ],
  floyd: [
    [1, 0, 7 / 16],
    [-1, 1, 3 / 16],
    [0, 1, 5 / 16],
    [1, 1, 1 / 16]
  ]
};

const MASK_SCALE = 0.5;
const MAX_BURSTS = 4;
const BURST_SECONDS = 1.2;
const HOLD = 1.6;
const INTRO_MS = 1100;

const hexToRgb = hex => {
  let h = String(hex || '').replace('#', '');
  if (h.length === 3) h = h.replace(/./g, c => c + c);
  const n = parseInt(h.slice(0, 6), 16);
  return Number.isNaN(n) ? [0, 0, 0] : [((n >> 16) & 255) / 255, ((n >> 8) & 255) / 255, (n & 255) / 255];
};

const fitScale = (w, h, iw, ih, contain) => {
  const ratio = w / h / (iw / ih);
  if (contain) return ratio > 1 ? [ratio, 1] : [1, 1 / ratio];
  return ratio > 1 ? [1, 1 / ratio] : [ratio, 1];
};

const wanderAt = (t, w, h) => [
  w * (0.5 + 0.33 * Math.sin(t * 0.53) + 0.08 * Math.sin(t * 1.31 + 0.6)),
  h * (0.5 + 0.28 * Math.sin(t * 0.71 + 1.1) + 0.07 * Math.cos(t * 1.57))
];

const measureEdge = (context, image) => {
  const size = 32;
  context.canvas.width = size;
  context.canvas.height = size;
  context.drawImage(image, 0, 0, size, size);
  const data = context.getImageData(0, 0, size, size).data;
  const sum = [0, 0, 0];
  const squares = [0, 0, 0];
  let count = 0;
  for (let y = 0; y < size; y++) {
    for (let x = 0; x < size; x++) {
      if (x > 0 && y > 0 && x < size - 1 && y < size - 1) continue;
      for (let c = 0; c < 3; c++) {
        const v = data[(y * size + x) * 4 + c] / 255;
        sum[c] += v;
        squares[c] += v * v;
      }
      count++;
    }
  }
  const matte = [sum[0] / count, sum[1] / count, sum[2] / count];
  const spread =
    matte.reduce((total, mean, c) => total + Math.sqrt(Math.max(0, squares[c] / count - mean * mean)), 0) / 3;
  return { matte, plain: spread < 0.06 };
};

let blueNoise = null;

const getBlueNoise = () => {
  if (blueNoise) return blueNoise;
  const size = 64;
  const n = size * size;
  const wrap = size - 1;
  const taps = [];
  for (let dy = -6; dy <= 6; dy++) {
    for (let dx = -6; dx <= 6; dx++) taps.push(dx, dy, Math.exp(-(dx * dx + dy * dy) / 4.5));
  }
  const energy = new Float32Array(n);
  const on = new Uint8Array(n);
  const splat = (i, sign) => {
    const x = i % size;
    const y = (i - x) / size;
    for (let k = 0; k < taps.length; k += 3) {
      energy[((y + taps[k + 1]) & wrap) * size + ((x + taps[k]) & wrap)] += sign * taps[k + 2];
    }
  };
  const find = (state, highest) => {
    let best = 0;
    let value = highest ? -Infinity : Infinity;
    for (let i = 0; i < n; i++) {
      if (on[i] !== state) continue;
      if (highest ? energy[i] > value : energy[i] < value) {
        value = energy[i];
        best = i;
      }
    }
    return best;
  };
  let seed = 7;
  const random = () => {
    seed = (seed * 16807) % 2147483647;
    return seed / 2147483647;
  };
  const initial = Math.round(n * 0.1);
  for (let placed = 0; placed < initial; ) {
    const i = Math.floor(random() * n);
    if (on[i]) continue;
    on[i] = 1;
    splat(i, 1);
    placed++;
  }
  for (let guard = 0; guard < n; guard++) {
    const cluster = find(1, true);
    on[cluster] = 0;
    splat(cluster, -1);
    const gap = find(0, false);
    on[gap] = 1;
    splat(gap, 1);
    if (gap === cluster) break;
  }
  const seedOn = on.slice();
  const seedEnergy = energy.slice();
  const rank = new Uint16Array(n);
  for (let r = initial - 1; r >= 0; r--) {
    const cluster = find(1, true);
    on[cluster] = 0;
    splat(cluster, -1);
    rank[cluster] = r;
  }
  on.set(seedOn);
  energy.set(seedEnergy);
  for (let r = initial; r < n; r++) {
    const gap = find(0, false);
    on[gap] = 1;
    splat(gap, 1);
    rank[gap] = r;
  }
  const data = new Uint8Array(n * 4);
  for (let i = 0; i < n; i++) {
    const v = Math.floor((rank[i] / n) * 256);
    data[i * 4] = v;
    data[i * 4 + 1] = v;
    data[i * 4 + 2] = v;
    data[i * 4 + 3] = 255;
  }
  blueNoise = data;
  return data;
};

const diffuse = (pixels, cols, rows, kernel, levels, rgb, grade) => {
  const channels = rgb ? 3 : 1;
  const values = new Float32Array(cols * rows * channels);
  for (let i = 0; i < cols * rows; i++) {
    const r = pixels[i * 4] / 255;
    const g = pixels[i * 4 + 1] / 255;
    const b = pixels[i * 4 + 2] / 255;
    if (rgb) {
      values[i * 3] = grade(r);
      values[i * 3 + 1] = grade(g);
      values[i * 3 + 2] = grade(b);
    } else {
      values[i] = grade(0.2126 * r + 0.7152 * g + 0.0722 * b);
    }
  }
  const steps = levels - 1;
  const out = new Uint8Array(cols * rows * 4);
  for (let y = 0; y < rows; y++) {
    const dir = y & 1 ? -1 : 1;
    for (let i = 0; i < cols; i++) {
      const x = dir > 0 ? i : cols - 1 - i;
      const p = y * cols + x;
      for (let c = 0; c < channels; c++) {
        const old = values[p * channels + c];
        const q = Math.min(steps, Math.max(0, Math.round(old * steps))) / steps;
        const err = old - q;
        const byte = Math.round(q * 255);
        if (rgb) out[p * 4 + c] = byte;
        else out[p * 4] = out[p * 4 + 1] = out[p * 4 + 2] = byte;
        for (let k = 0; k < kernel.length; k++) {
          const nx = x + kernel[k][0] * dir;
          const ny = y + kernel[k][1];
          if (nx < 0 || nx >= cols || ny >= rows) continue;
          values[(ny * cols + nx) * channels + c] += err * kernel[k][2];
        }
      }
      out[p * 4 + 3] = 255;
    }
  }
  return out;
};

const vertex = `#version 300 es
in vec2 position;
in vec2 uv;
out vec2 vUv;
void main() {
  vUv = uv;
  gl_Position = vec4(position, 0.0, 1.0);
}
`;

const maskFragment = `#version 300 es
precision highp float;

uniform sampler2D tPrev;
uniform vec2 uSize;
uniform vec2 uFrom;
uniform vec2 uTo;
uniform float uRadius;
uniform float uSoftness;
uniform float uStrength;
uniform float uFade;
uniform float uHold;

in vec2 vUv;
out vec4 fragColor;

float strokeDistance(vec2 p, vec2 a, vec2 b) {
  vec2 ab = b - a;
  float h = clamp(dot(p - a, ab) / max(dot(ab, ab), 0.0001), 0.0, 1.0);
  return length(p - a - ab * h);
}

void main() {
  vec2 p = vec2(vUv.x, 1.0 - vUv.y) * uSize;
  float trail = max(texture(tPrev, vUv).r - uFade, 0.0);
  float band = max(uRadius * uSoftness, 1.0) * uHold;
  float d = strokeDistance(p, uFrom, uTo);
  trail = max(trail, clamp((uRadius - d) / band, 0.0, 1.0) * uStrength);
  fragColor = vec4(trail, 0.0, 0.0, 1.0);
}
`;

const viewFragment = `#version 300 es
precision highp float;
precision highp int;

uniform sampler2D tImage;
uniform sampler2D tMask;
uniform sampler2D tNoise;
uniform sampler2D tDiffused;
uniform vec2 uResolution;
uniform vec2 uCover;
uniform float uLod;
uniform float uCell;
uniform int uPattern;
uniform int uPalette;
uniform float uLevels;
uniform vec3 uInk;
uniform vec3 uPaper;
uniform vec3 uRimColor;
uniform float uRim;
uniform float uContrast;
uniform float uBrightness;
uniform float uReverse;
uniform float uIntro;
uniform float uHold;
uniform vec3 uMatte;
uniform float uKey;
uniform vec2 uSize;
uniform vec4 uBursts[4];
uniform float uBurstWidth;

in vec2 vUv;
out vec4 fragColor;

float bayer(vec2 cell) {
  ivec2 p = ivec2(mod(cell, 8.0));
  int v = p.x ^ p.y;
  int m = ((v & 1) << 5) | ((p.y & 1) << 4) | ((v & 2) << 2) | ((p.y & 2) << 1) | ((v & 4) >> 1) | ((p.y & 4) >> 2);
  return (float(m) + 0.5) / 64.0;
}

float blueNoise(vec2 cell) {
  return (texelFetch(tNoise, ivec2(mod(cell, 64.0)), 0).r * 255.0 + 0.5) / 256.0;
}

float engraving(vec2 cell) {
  float period = 6.0;
  float f = (mod(cell.x + cell.y, period) + 0.5) / period;
  return clamp(abs(f * 2.0 - 1.0) + (bayer(cell) - 0.5) * (2.0 / period), 0.0, 1.0);
}

vec2 imageUv(vec2 uv) {
  return (uv - 0.5) * uCover + 0.5;
}

float within(vec2 p) {
  vec2 s = step(vec2(0.0), p) * step(p, vec2(1.0));
  return s.x * s.y;
}

vec3 grade(vec3 c) {
  return pow(clamp((c - 0.5) * uContrast + 0.5 + uBrightness, 0.0, 1.0), vec3(1.6));
}

float shockwave(vec2 p) {
  float value = 0.0;
  for (int i = 0; i < 4; i++) {
    vec4 burst = uBursts[i];
    if (burst.w <= 0.0) continue;
    float offset = distance(p, burst.xy) - burst.z;
    float edge = offset > 0.0 ? offset / (uBurstWidth * 0.35) : -offset / uBurstWidth;
    value = max(value, clamp(1.0 - edge, 0.0, 1.0) * burst.w);
  }
  return value;
}

vec3 toned(vec3 c) {
  return uPalette == 1 ? grade(c) : grade(vec3(dot(c, vec3(0.2126, 0.7152, 0.0722))));
}

vec3 quantize(vec3 v, float t) {
  float steps = max(uLevels - 1.0, 1.0);
  vec3 s = v * steps;
  vec3 base = floor(s);
  return min(base + step(vec3(t), s - base), vec3(steps)) / steps;
}

void main() {
  vec2 px = vec2(gl_FragCoord.x, uResolution.y - gl_FragCoord.y);
  vec2 cell = floor(px / uCell);
  vec2 center = (cell + 0.5) * uCell;
  vec2 cellUv = vec2(center.x / uResolution.x, 1.0 - center.y / uResolution.y);

  vec2 sampleUv = imageUv(cellUv);
  float framed = within(sampleUv);
  vec3 level;
  if (uPattern == 3) {
    level = texelFetch(tDiffused, ivec2(cell), 0).rgb;
  } else {
    vec3 c = mix(uMatte, textureLod(tImage, sampleUv, uLod).rgb, framed);
    float t = uPattern == 1 ? blueNoise(cell) : (uPattern == 2 ? engraving(cell) : bayer(cell));
    level = quantize(toned(c), t);
  }

  vec3 color = mix(uInk, mix(uInk, uPaper, level), max(framed, uKey));
  vec3 backdrop = mix(uInk, uPaper, toned(uMatte));
  vec2 photoUv = imageUv(vUv);
  vec3 raw = texture(tImage, photoUv).rgb;
  float plain = uKey * (1.0 - smoothstep(0.05, 0.22, distance(raw, uMatte)));
  vec3 photo = mix(mix(uInk, backdrop, uKey), mix(raw, backdrop, plain), within(photoUv));

  vec2 point = vec2(cellUv.x, 1.0 - cellUv.y) * uSize;
  float mask = max(clamp(texture(tMask, cellUv).r * uHold, 0.0, 1.0), shockwave(point));
  float shown = mix(mask, 1.0 - mask, uReverse);
  float order = bayer(cell.yx);
  float low = order * (1.0 - uRim);
  color = mix(color, uRimColor, step(low, shown) * step(0.001, uRim));
  color = mix(color, photo, step(low + uRim, shown));

  vec2 aspect = vec2(uResolution.x / uResolution.y, 1.0);
  float spread = length((cellUv - 0.5) * aspect) / length(aspect * 0.5);
  float appear = step(spread * 0.72 + bayer(cell + vec2(3.0, 5.0)) * 0.28, uIntro * 1.001);
  fragColor = vec4(mix(uInk, color, appear), 1.0);
}
`;

const DitherVeil = ({
  src = DEFAULT_SRC,
  fit = 'contain',
  pattern = 'floyd',
  pixelSize = 2,
  levels = 2,
  palette = 'duotone',
  inkColor = '#120f17',
  paperColor = '#f4f1ea',
  contrast = 1.15,
  brightness = 0,
  revealRadius = 200,
  softness = 0.6,
  linger = 1,
  rimColor = '#a78bfa',
  rim = 0,
  reverse = false,
  wander = false,
  clickBurst = true,
  className = '',
  style
}) => {
  const containerRef = useRef(null);
  const settingsRef = useRef(null);
  const wakeRef = useRef(() => {});

  useEffect(() => {
    settingsRef.current = {
      fit,
      pattern,
      pixelSize,
      levels,
      palette,
      inkColor,
      paperColor,
      contrast,
      brightness,
      revealRadius,
      softness,
      linger,
      rimColor,
      rim,
      reverse,
      wander,
      clickBurst
    };
    wakeRef.current();
  });

  useEffect(() => {
    const container = containerRef.current;
    if (!container) return undefined;

    const reducedMotion = window.matchMedia?.('(prefers-reduced-motion: reduce)').matches;
    const renderer = new Renderer({ dpr: Math.min(window.devicePixelRatio || 1, 2), alpha: false, antialias: false });
    const gl = renderer.gl;
    const canvas = gl.canvas;
    canvas.style.display = 'block';
    canvas.style.width = '100%';
    canvas.style.height = '100%';
    container.appendChild(canvas);

    const floatMask = renderer.isWebgl2 && !!renderer.getExtension('EXT_color_buffer_float');
    const geometry = new Triangle(gl);

    const dataTexture = () =>
      new Texture(gl, {
        image: new Uint8Array(4),
        width: 1,
        height: 1,
        generateMipmaps: false,
        flipY: false,
        minFilter: gl.NEAREST,
        magFilter: gl.NEAREST
      });
    const imageTexture = new Texture(gl, { minFilter: gl.LINEAR_MIPMAP_LINEAR, magFilter: gl.LINEAR });
    const noiseTexture = dataTexture();
    const diffusedTexture = dataTexture();

    const createMask = (w, h) =>
      new RenderTarget(gl, {
        width: w,
        height: h,
        depth: false,
        type: floatMask ? gl.HALF_FLOAT : gl.UNSIGNED_BYTE,
        internalFormat: floatMask ? gl.RGBA16F : gl.RGBA,
        minFilter: gl.LINEAR,
        magFilter: gl.LINEAR
      });
    const destroyMask = target => {
      gl.deleteFramebuffer(target.buffer);
      gl.deleteTexture(target.texture.texture);
    };
    let masks = [createMask(2, 2), createMask(2, 2)];

    const maskUniforms = {
      tPrev: { value: masks[0].texture },
      uSize: { value: [1, 1] },
      uFrom: { value: [0, 0] },
      uTo: { value: [0, 0] },
      uRadius: { value: 1 },
      uSoftness: { value: 0.5 },
      uStrength: { value: 0 },
      uFade: { value: 1 },
      uHold: { value: HOLD }
    };
    const viewUniforms = {
      tImage: { value: imageTexture },
      tMask: { value: masks[0].texture },
      tNoise: { value: noiseTexture },
      tDiffused: { value: diffusedTexture },
      uResolution: { value: [1, 1] },
      uCover: { value: [1, 1] },
      uLod: { value: 0 },
      uCell: { value: 3 },
      uPattern: { value: 0 },
      uPalette: { value: 0 },
      uLevels: { value: 2 },
      uInk: { value: [0, 0, 0] },
      uPaper: { value: [1, 1, 1] },
      uRimColor: { value: [1, 1, 1] },
      uRim: { value: 0 },
      uContrast: { value: 1 },
      uBrightness: { value: 0 },
      uReverse: { value: 0 },
      uIntro: { value: 0 },
      uHold: { value: HOLD },
      uMatte: { value: [0, 0, 0] },
      uKey: { value: 0 },
      uSize: { value: [1, 1] },
      uBursts: { value: Array.from({ length: MAX_BURSTS * 4 }, () => 0) },
      uBurstWidth: { value: 60 }
    };
    const program = (fragment, uniforms) =>
      new Program(gl, { vertex, fragment, uniforms, depthTest: false, depthWrite: false });
    const maskMesh = new Mesh(gl, { geometry, program: program(maskFragment, maskUniforms) });
    const viewMesh = new Mesh(gl, { geometry, program: program(viewFragment, viewUniforms) });

    const sampler = document.createElement('canvas');
    const samplerContext = sampler.getContext('2d', { willReadFrequently: true });

    let image = null;
    let introStart = 0;
    let diffusedKey = '';
    let diffusionBlocked = false;
    let width = 1;
    let height = 1;
    let visible = true;
    let raf = 0;
    let last = performance.now();
    let trailUntil = 0;
    let presence = 0;
    let drift = 0;
    const pointer = { x: 0, y: 0, inside: false, fresh: true, placed: false };
    const brush = { x: 0, y: 0, px: 0, py: 0 };
    const bursts = [];

    let resizePending = false;
    const applyLayout = () => {
      width = Math.max(1, container.clientWidth);
      height = Math.max(1, container.clientHeight);
      renderer.setSize(width, height);
      viewUniforms.uResolution.value = [canvas.width, canvas.height];
      maskUniforms.uSize.value = [width, height];
      viewUniforms.uSize.value = [width, height];
      const mw = Math.max(2, Math.round(width * MASK_SCALE));
      const mh = Math.max(2, Math.round(height * MASK_SCALE));
      if (mw !== masks[0].width || mh !== masks[0].height) {
        const previous = masks;
        masks = [createMask(mw, mh), createMask(mw, mh)];
        maskUniforms.tPrev.value = previous[0].texture;
        maskUniforms.uStrength.value = 0;
        maskUniforms.uFade.value = 0;
        renderer.render({ scene: maskMesh, target: masks[0] });
        previous.forEach(destroyMask);
      }
      if (!pointer.placed) {
        pointer.x = width / 2;
        pointer.y = height / 2;
      }
    };

    const layout = () => {
      resizePending = true;
      wake();
    };

    const updateDiffusion = (cell, s) => {
      if (!image || !samplerContext) return;
      const cols = Math.ceil(canvas.width / cell);
      const rows = Math.ceil(canvas.height / cell);
      const key = [
        s.pattern,
        s.levels,
        s.palette,
        s.contrast,
        s.brightness,
        s.fit,
        cols,
        rows,
        canvas.width,
        canvas.height
      ].join('|');
      if (key === diffusedKey) return;
      diffusedKey = key;
      const [cx, cy] = viewUniforms.uCover.value;
      const iw = image.naturalWidth;
      const ih = image.naturalHeight;
      sampler.width = cols;
      sampler.height = rows;
      samplerContext.imageSmoothingEnabled = true;
      samplerContext.imageSmoothingQuality = 'high';
      const [mr, mg, mb] = viewUniforms.uMatte.value;
      samplerContext.fillStyle = `rgb(${mr * 255}, ${mg * 255}, ${mb * 255})`;
      samplerContext.fillRect(0, 0, cols, rows);
      const sx = (0.5 - 0.5 * cx) * iw;
      const sy = (0.5 - 0.5 * cy) * ih;
      const sw = ((cols * cell) / canvas.width) * cx * iw;
      const sh = ((rows * cell) / canvas.height) * cy * ih;
      const x0 = Math.max(sx, 0);
      const y0 = Math.max(sy, 0);
      const x1 = Math.min(sx + sw, iw);
      const y1 = Math.min(sy + sh, ih);
      if (x1 > x0 && y1 > y0) {
        samplerContext.drawImage(
          image,
          x0,
          y0,
          x1 - x0,
          y1 - y0,
          ((x0 - sx) / sw) * cols,
          ((y0 - sy) / sh) * rows,
          ((x1 - x0) / sw) * cols,
          ((y1 - y0) / sh) * rows
        );
      }
      const grade = v => Math.pow(Math.min(1, Math.max(0, (v - 0.5) * s.contrast + 0.5 + s.brightness)), 1.6);
      const pixels = samplerContext.getImageData(0, 0, cols, rows).data;
      diffusedTexture.image = diffuse(pixels, cols, rows, KERNELS[s.pattern], s.levels, s.palette === 'rgb', grade);
      diffusedTexture.width = cols;
      diffusedTexture.height = rows;
      diffusedTexture.needsUpdate = true;
    };

    const frame = now => {
      raf = 0;
      if (resizePending) {
        resizePending = false;
        applyLayout();
      }
      const s = settingsRef.current;
      if (!s) return;
      const dt = Math.min(0.05, Math.max(0, (now - last) / 1000));
      last = now;

      const wanderOn = s.wander && !reducedMotion;
      let targetX = pointer.x;
      let targetY = pointer.y;
      if (!pointer.inside && wanderOn) {
        drift = Math.min(1, drift + dt / 1.2);
        const [wx, wy] = wanderAt(now / 1000, width, height);
        const k = drift * drift * (3 - 2 * drift);
        targetX += (wx - targetX) * k;
        targetY += (wy - targetY) * k;
      } else {
        drift = 0;
      }

      presence += ((pointer.inside || wanderOn ? 1 : 0) - presence) * (1 - Math.exp(-dt / 0.16));

      if (pointer.fresh) {
        brush.x = brush.px = targetX;
        brush.y = brush.py = targetY;
        pointer.fresh = false;
      } else {
        const follow = 1 - Math.exp(-dt / 0.035);
        brush.x += (targetX - brush.x) * follow;
        brush.y += (targetY - brush.y) * follow;
      }

      const burstData = viewUniforms.uBursts.value;
      burstData.fill(0);
      for (let i = bursts.length - 1; i >= 0; i--) {
        if ((now - bursts[i].start) / 1000 >= BURST_SECONDS) bursts.splice(i, 1);
      }
      bursts.forEach((b, i) => {
        const k = Math.max(0, (now - b.start) / 1000 / BURST_SECONDS);
        const reach =
          Math.hypot(Math.max(b.x, width - b.x), Math.max(b.y, height - b.y)) + viewUniforms.uBurstWidth.value;
        burstData[i * 4] = b.x;
        burstData[i * 4 + 1] = b.y;
        burstData[i * 4 + 2] = reach * Math.sin((k * Math.PI) / 2);
        burstData[i * 4 + 3] = 1 - k * k * k;
      });

      if (presence > 0.002) trailUntil = now + s.linger * 1000 + 150;

      const minFade = floatMask ? 0 : 1.5 / 255;
      const fade = s.linger > 0 ? dt / s.linger : 1;
      maskUniforms.tPrev.value = masks[0].texture;
      maskUniforms.uFrom.value = [brush.px, brush.py];
      maskUniforms.uTo.value = [brush.x, brush.y];
      maskUniforms.uRadius.value = s.revealRadius * (0.45 + 0.55 * presence);
      maskUniforms.uSoftness.value = s.softness;
      maskUniforms.uStrength.value = presence;
      maskUniforms.uFade.value = Math.max(fade, minFade);
      viewUniforms.uBurstWidth.value = Math.max(60, s.revealRadius * 0.9);
      renderer.render({ scene: maskMesh, target: masks[1] });
      masks.reverse();
      brush.px = brush.x;
      brush.py = brush.y;

      const cell = Math.max(1, Math.round(s.pixelSize * renderer.dpr));
      if (image) {
        viewUniforms.uCover.value = fitScale(
          canvas.width,
          canvas.height,
          image.naturalWidth,
          image.naturalHeight,
          s.fit === 'contain'
        );
      }
      let patternIndex = ORDERED[s.pattern] ?? 0;
      if (KERNELS[s.pattern]) {
        patternIndex = 0;
        if (image && !diffusionBlocked) {
          try {
            updateDiffusion(cell, s);
            patternIndex = 3;
          } catch {
            diffusionBlocked = true;
          }
        }
      }
      if (patternIndex === 1 && noiseTexture.width !== 64) {
        noiseTexture.image = getBlueNoise();
        noiseTexture.width = 64;
        noiseTexture.height = 64;
        noiseTexture.needsUpdate = true;
      }

      const texelsPerPixel = image ? (viewUniforms.uCover.value[0] * image.naturalWidth) / canvas.width : 1;
      viewUniforms.tMask.value = masks[0].texture;
      viewUniforms.uCell.value = cell;
      viewUniforms.uLod.value = Math.log2(Math.max(cell * texelsPerPixel, 1));
      viewUniforms.uPattern.value = patternIndex;
      viewUniforms.uPalette.value = s.palette === 'rgb' ? 1 : 0;
      viewUniforms.uLevels.value = Math.max(2, Math.round(s.levels));
      viewUniforms.uInk.value = hexToRgb(s.inkColor);
      viewUniforms.uPaper.value = hexToRgb(s.paperColor);
      viewUniforms.uRimColor.value = hexToRgb(s.rimColor);
      viewUniforms.uRim.value = Math.min(Math.max(s.rim, 0), 0.95);
      viewUniforms.uContrast.value = s.contrast;
      viewUniforms.uBrightness.value = s.brightness;
      viewUniforms.uReverse.value = s.reverse ? 1 : 0;
      const intro = image ? Math.min(1, (now - introStart) / INTRO_MS) : 0;
      viewUniforms.uIntro.value = 1 - Math.pow(1 - intro, 2);
      renderer.render({ scene: viewMesh });

      const busy =
        pointer.inside || wanderOn || presence > 0.002 || bursts.length > 0 || now < trailUntil || (image && intro < 1);
      if (busy && visible) raf = requestAnimationFrame(frame);
    };

    const wake = () => {
      if (raf || !visible) return;
      last = performance.now();
      raf = requestAnimationFrame(frame);
    };
    wakeRef.current = wake;

    const img = new Image();
    img.crossOrigin = 'anonymous';
    img.decoding = 'async';
    img.onload = () => {
      image = img;
      imageTexture.image = img;
      try {
        if (samplerContext) {
          const edge = measureEdge(samplerContext, img);
          viewUniforms.uMatte.value = edge.matte;
          viewUniforms.uKey.value = edge.plain ? 1 : 0;
        }
      } catch {}
      introStart = performance.now();
      wake();
    };
    img.src = src;

    const locate = e => {
      const rect = container.getBoundingClientRect();
      pointer.x = e.clientX - rect.left;
      pointer.y = e.clientY - rect.top;
      pointer.placed = true;
    };
    const onMove = e => {
      locate(e);
      if (!pointer.inside) {
        pointer.inside = true;
        pointer.fresh = true;
      }
      wake();
    };
    const onLeave = () => {
      pointer.inside = false;
      wake();
    };
    const onDown = e => {
      onMove(e);
      if (!settingsRef.current?.clickBurst || (e.pointerType === 'mouse' && e.button !== 0)) return;
      bursts.push({ x: pointer.x, y: pointer.y, start: performance.now() });
      if (bursts.length > MAX_BURSTS) bursts.shift();
    };
    container.addEventListener('pointermove', onMove, { passive: true });
    container.addEventListener('pointerenter', onMove, { passive: true });
    container.addEventListener('pointerdown', onDown, { passive: true });
    container.addEventListener('pointerleave', onLeave, { passive: true });
    container.addEventListener('pointercancel', onLeave, { passive: true });

    const resizeObserver = new ResizeObserver(() => {
      layout();
      wake();
    });
    resizeObserver.observe(container);
    const intersectionObserver = new IntersectionObserver(([entry]) => {
      visible = entry.isIntersecting;
      wake();
    });
    intersectionObserver.observe(container);

    layout();
    wake();

    return () => {
      cancelAnimationFrame(raf);
      wakeRef.current = () => {};
      resizeObserver.disconnect();
      intersectionObserver.disconnect();
      img.onload = null;
      container.removeEventListener('pointermove', onMove);
      container.removeEventListener('pointerenter', onMove);
      container.removeEventListener('pointerdown', onDown);
      container.removeEventListener('pointerleave', onLeave);
      container.removeEventListener('pointercancel', onLeave);
      gl.getExtension('WEBGL_lose_context')?.loseContext();
      if (canvas.parentNode) canvas.parentNode.removeChild(canvas);
    };
  }, [src]);

  return <div ref={containerRef} className={`dither-veil ${className}`.trim()} style={style} />;
};

export default DitherVeil;

```

### Component CSS
```css
.dither-veil {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
  touch-action: pan-y;
}

```

### Integration Instructions
1. Install any listed dependencies.
2. Copy the component source into the appropriate directory in the project.
3. Import the CSS file alongside the component.
4. Import and render the component using the usage example above as a starting point.
5. Adjust props as needed for the specific use case — refer to the props table for all available options.

### More from React Bits
The full library index, including everything reactbits.dev offers, is at https://reactbits.dev/llms.txt — fetch it if this component is not the right fit or the project needs more pieces.

## Integrate the <TargetCursor /> component from React Bits

You are helping integrate an open-source React component into an existing application.

### Component: TargetCursor
### Variant: JavaScript + CSS


---

### Usage Example
```jsx
import TargetCursor from './TargetCursor';

export default function App() {
  return (
    <div>
      <TargetCursor spinDuration={2} hideDefaultCursor parallaxOn />

      <h1>Hover over the elements below</h1>
      <button className="cursor-target" data-cursor-label="Open">Click me!</button>
      <div className="cursor-target">Hover target</div>
    </div>
  );
}
```

### Props
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| targetSelector | string | '.cursor-target' | CSS selector for the elements the brackets lock onto. |
| cursorColor | string | '#ffffff' | Color of the brackets and dot. |
| cursorColorOnTarget | string | - | Color the cursor blends to while locked onto a target. |
| spinDuration | number | 2 | Seconds per turn while the cursor roams. 0 stops the spin. |
| hoverDuration | number | 0.2 | How long the brackets take to snap onto a target, in seconds. |
| parallaxOn | boolean | true | Lets the locked brackets lean slightly toward the cursor. |
| hideDefaultCursor | boolean | true | Hides the system cursor while this cursor is shown. |
| size | number | 36 | Size of the reticle while roaming, in px. |
| cornerSize | number | 12 | Length of each bracket arm, in px. |
| thickness | number | 3 | Stroke width of the brackets, in px. |
| padding | number | 6 | Gap between a locked target and the brackets, in px. |
| matchRadius | boolean | true | Rounds the brackets to follow the target's corner radius. |
| dotSize | number | 4 | Size of the center dot, in px. 0 hides it. |
| blendMode | string | 'difference' | CSS blend mode of the cursor. Difference keeps it visible on any background. |
| showLabel | boolean | true | Shows the data-cursor-label text of a locked target above the brackets. |
| clickEffect | boolean | true | Squeezes the brackets and dot while the mouse button is held. |
| container | RefObject | HTMLElement | - | Limits the cursor to one element. By default it works across the whole page. |

### Full Component Source
```jsx
'use client';

import { useEffect, useRef, useState } from 'react';
import { createPortal } from 'react-dom';
import './TargetCursor.css';

const CORNER_ANGLES = [0, 90, 180, 270];
const QUARTER = Math.PI / 2;

const spring = (state, goal, stiffness, damping, dt) => {
  const steps = 2;
  const h = dt / steps;
  for (let i = 0; i < steps; i++) {
    state.v += (stiffness * (goal - state.x) - damping * state.v) * h;
    state.x += state.v * h;
  }
};

const resolveElement = value => (value && 'current' in value ? value.current : value) || null;

const TargetCursor = ({
  targetSelector = '.cursor-target',
  cursorColor = '#ffffff',
  cursorColorOnTarget,
  spinDuration = 2,
  hoverDuration = 0.2,
  parallaxOn = true,
  hideDefaultCursor = true,
  size = 36,
  cornerSize = 12,
  thickness = 3,
  padding = 6,
  matchRadius = true,
  dotSize = 4,
  blendMode = 'difference',
  showLabel = true,
  clickEffect = true,
  container
}) => {
  const [host, setHost] = useState(null);
  const rootRef = useRef(null);
  const cornerRefs = useRef([]);
  const dotRef = useRef(null);
  const labelRef = useRef(null);
  const wakeRef = useRef(null);
  const settings = {
    targetSelector,
    cursorColor,
    cursorColorOnTarget,
    spinDuration,
    hoverDuration: Math.max(0.05, hoverDuration),
    parallaxOn,
    hideDefaultCursor,
    size: Math.max(4, size),
    cornerSize: Math.max(2, cornerSize),
    thickness: Math.max(0.5, thickness),
    padding,
    matchRadius,
    dotSize: Math.max(0, dotSize),
    showLabel,
    clickEffect,
    container
  };
  const settingsRef = useRef(settings);
  settingsRef.current = settings;

  useEffect(() => {
    const finePointer = window.matchMedia('(hover: hover) and (pointer: fine)');
    const update = () => setHost(finePointer.matches ? document.body : null);
    update();
    finePointer.addEventListener('change', update);
    return () => finePointer.removeEventListener('change', update);
  }, []);

  useEffect(() => {
    const root = rootRef.current;
    if (!host || !root) return undefined;

    const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    const style = document.createElement('style');
    document.head.appendChild(style);

    const pointer = { x: 0, y: 0, inside: false, seen: false, pressed: false };
    const centerX = { x: 0, v: 0 };
    const centerY = { x: 0, v: 0 };
    const halfW = { x: 0, v: 0 };
    const halfH = { x: 0, v: 0 };
    const radius = { x: 0, v: 0 };
    const turn = { x: 0, v: 0 };
    const lock = { x: 0, v: 0 };
    const press = { x: 0, v: 0 };
    const presence = { x: 0, v: 0 };
    let target = null;
    let targetRadius = 0;
    let quarter = 0;
    let spinSpeed = 0;
    let raf = 0;
    let last = 0;
    let lastLabel = '';
    let lastColor = '';
    let lastScope = null;

    const scopeOf = () => resolveElement(settingsRef.current.container);

    const hideCursor = () => {
      const s = settingsRef.current;
      const scope = scopeOf();
      if (scope !== lastScope) {
        lastScope?.removeAttribute('data-target-cursor-scope');
        scope?.setAttribute('data-target-cursor-scope', '');
        lastScope = scope;
      }
      const selector = scope ? '[data-target-cursor-scope]' : 'html';
      const rule =
        s.hideDefaultCursor && pointer.inside ? `${selector}, ${selector} * { cursor: none !important; }` : '';
      if (style.textContent !== rule) style.textContent = rule;
    };

    const findTarget = (x, y, element) => {
      const s = settingsRef.current;
      const node = element || document.elementFromPoint(x, y);
      const found = node?.closest?.(s.targetSelector) || null;
      const scope = scopeOf();
      if (found && scope && !scope.contains(found)) return null;
      return found;
    };

    const lockOn = next => {
      if (next === target) return;
      target = next;
      if (target) {
        const angle = turn.x;
        quarter = Math.round(angle / QUARTER);
        const corner = parseFloat(getComputedStyle(target).borderTopLeftRadius) || 0;
        targetRadius = corner;
        spinSpeed = 0;
      }
    };

    const frame = now => {
      raf = 0;
      const s = settingsRef.current;
      const dt = last ? Math.min(0.05, (now - last) / 1000) : 1 / 60;
      last = now;

      if (target && !target.isConnected) lockOn(null);
      const rect = target ? target.getBoundingClientRect() : null;
      const locked = !!rect && pointer.inside;
      const snapW = 4.6 / s.hoverDuration;
      const snapK = snapW * snapW;
      const snapC = 2 * 0.62 * snapW;

      spring(presence, pointer.inside ? 1 : 0, 260, 32, dt);
      spring(lock, locked ? 1 : 0, snapK, 2 * snapW, dt);
      spring(press, pointer.pressed && s.clickEffect ? 1 : 0, 900, 40, dt);

      let goalX = pointer.x;
      let goalY = pointer.y;
      let goalW = s.size / 2;
      let goalH = s.size / 2;
      let goalR = Math.min(2, s.cornerSize / 4);
      if (locked) {
        const cx = rect.left + rect.width / 2;
        const cy = rect.top + rect.height / 2;
        const lean = s.parallaxOn ? 0.08 : 0;
        goalX = cx + (pointer.x - cx) * lean;
        goalY = cy + (pointer.y - cy) * lean;
        const odd = Math.abs(quarter) % 2 === 1;
        const w = rect.width / 2 + s.padding;
        const h = rect.height / 2 + s.padding;
        goalW = odd ? h : w;
        goalH = odd ? w : h;
        goalR = s.matchRadius ? Math.min(targetRadius + Math.max(0, s.padding), s.cornerSize) : 0;
      }

      if (!pointer.inside && presence.x < 0.01) pointer.seen = false;
      if (!pointer.seen) {
        centerX.x = goalX;
        centerY.x = goalY;
        halfW.x = goalW;
        halfH.x = goalH;
        pointer.seen = pointer.inside;
      }

      const follow = locked ? snapK : 2200;
      const followC = locked ? snapC : 2 * Math.sqrt(2200);
      spring(centerX, goalX, follow, followC, dt);
      spring(centerY, goalY, follow, followC, dt);
      spring(halfW, goalW, snapK, snapC, dt);
      spring(halfH, goalH, snapK, snapC, dt);
      spring(radius, goalR, snapK, 2 * snapW, dt);

      if (locked) {
        spring(turn, quarter * QUARTER, snapK, snapC, dt);
      } else {
        const speed = s.spinDuration > 0 && !reduce ? (Math.PI * 2) / s.spinDuration : 0;
        spinSpeed += (speed - spinSpeed) * (1 - Math.exp(-dt * 3));
        turn.v = spinSpeed;
        turn.x += spinSpeed * dt;
      }

      const squeeze = 1 - press.x * 0.14;
      const cos = Math.cos(turn.x);
      const sin = Math.sin(turn.x);
      const w = Math.max(0, halfW.x) * squeeze;
      const h = Math.max(0, halfH.x) * squeeze;
      const local = [
        [-w, -h],
        [w, -h],
        [w, h],
        [-w, h]
      ];
      const r = Math.max(0, Math.min(radius.x, s.cornerSize));
      const degrees = (turn.x * 180) / Math.PI;
      let left = Infinity;
      let top = Infinity;
      cornerRefs.current.forEach((corner, i) => {
        if (!corner) return;
        const [lx, ly] = local[i];
        const x = centerX.x + lx * cos - ly * sin;
        const y = centerY.x + lx * sin + ly * cos;
        left = Math.min(left, x);
        top = Math.min(top, y);
        corner.style.transform = `translate3d(${x.toFixed(2)}px, ${y.toFixed(2)}px, 0) rotate(${(CORNER_ANGLES[i] + degrees).toFixed(2)}deg)`;
        corner.style.width = `${s.cornerSize}px`;
        corner.style.height = `${s.cornerSize}px`;
        corner.style.borderWidth = `${s.thickness}px 0 0 ${s.thickness}px`;
        corner.style.borderTopLeftRadius = `${r.toFixed(2)}px`;
      });

      const dot = dotRef.current;
      if (dot) {
        const dotScale = 1 - press.x * 0.35;
        dot.style.transform = `translate3d(${pointer.x}px, ${pointer.y}px, 0) translate(-50%, -50%) scale(${dotScale.toFixed(3)})`;
        dot.style.width = `${s.dotSize}px`;
        dot.style.height = `${s.dotSize}px`;
      }

      const label = labelRef.current;
      if (label) {
        const text = s.showLabel && target ? target.getAttribute('data-cursor-label') || '' : '';
        if (text && text !== lastLabel) label.textContent = text;
        if (text) lastLabel = text;
        const shown = text ? Math.max(0, Math.min(1, lock.x)) : 0;
        label.style.transform = `translate3d(${left.toFixed(2)}px, ${(top - 8).toFixed(2)}px, 0) translateY(-100%) translateY(${((1 - shown) * 4).toFixed(2)}px)`;
        label.style.opacity = shown.toFixed(3);
      }

      const mix = s.cursorColorOnTarget ? Math.round(Math.max(0, Math.min(1, lock.x)) * 100) : 0;
      const color = mix ? `color-mix(in oklab, ${s.cursorColorOnTarget} ${mix}%, ${s.cursorColor})` : s.cursorColor;
      if (color !== lastColor) {
        root.style.setProperty('--target-cursor-color', color);
        lastColor = color;
      }
      root.style.opacity = Math.max(0, Math.min(1, presence.x)).toFixed(3);

      const resting =
        !pointer.inside &&
        presence.x < 0.002 &&
        Math.abs(presence.v) < 0.002 &&
        Math.abs(lock.x) < 0.002 &&
        Math.abs(press.x) < 0.002;
      const spinning = pointer.inside && !locked && spinSpeed > 0.0001;
      const moving =
        Math.abs(centerX.v) + Math.abs(centerY.v) + Math.abs(halfW.v) + Math.abs(halfH.v) + Math.abs(turn.v) > 0.01 ||
        Math.abs(goalX - centerX.x) + Math.abs(goalY - centerY.x) > 0.05 ||
        Math.abs(lock.v) + Math.abs(press.v) + Math.abs(presence.v) > 0.001;
      if (!resting && (spinning || moving || locked)) raf = requestAnimationFrame(frame);
      else last = 0;
    };

    const wake = () => {
      if (!raf) raf = requestAnimationFrame(frame);
    };
    wakeRef.current = () => {
      hideCursor();
      wake();
    };

    const insideScope = (x, y, element) => {
      const scope = scopeOf();
      if (!scope) return true;
      if (element && scope.contains(element)) return true;
      const box = scope.getBoundingClientRect();
      return x >= box.left && x <= box.right && y >= box.top && y <= box.bottom;
    };

    const onPointerMove = event => {
      if (event.pointerType && event.pointerType !== 'mouse') return;
      pointer.x = event.clientX;
      pointer.y = event.clientY;
      const inside = insideScope(pointer.x, pointer.y, event.target);
      if (inside !== pointer.inside) {
        pointer.inside = inside;
        hideCursor();
      }
      lockOn(inside ? findTarget(pointer.x, pointer.y, event.target) : null);
      wake();
    };

    const onPointerDown = event => {
      if (event.pointerType && event.pointerType !== 'mouse') return;
      pointer.pressed = true;
      wake();
    };

    const onPointerUp = () => {
      pointer.pressed = false;
      wake();
    };

    const onLeave = () => {
      pointer.inside = false;
      pointer.pressed = false;
      lockOn(null);
      hideCursor();
      wake();
    };

    const onScroll = () => {
      if (!pointer.inside) return;
      lockOn(findTarget(pointer.x, pointer.y));
      wake();
    };

    window.addEventListener('pointermove', onPointerMove, { passive: true });
    window.addEventListener('pointerdown', onPointerDown, { passive: true });
    window.addEventListener('pointerup', onPointerUp, { passive: true });
    window.addEventListener('scroll', onScroll, { passive: true, capture: true });
    document.documentElement.addEventListener('pointerleave', onLeave);
    window.addEventListener('blur', onLeave);

    return () => {
      cancelAnimationFrame(raf);
      wakeRef.current = null;
      window.removeEventListener('pointermove', onPointerMove);
      window.removeEventListener('pointerdown', onPointerDown);
      window.removeEventListener('pointerup', onPointerUp);
      window.removeEventListener('scroll', onScroll, { capture: true });
      document.documentElement.removeEventListener('pointerleave', onLeave);
      window.removeEventListener('blur', onLeave);
      lastScope?.removeAttribute('data-target-cursor-scope');
      style.remove();
    };
  }, [host]);

  useEffect(() => {
    wakeRef.current?.();
  });

  if (!host) return null;

  return createPortal(
    <div ref={rootRef} className="target-cursor" style={{ mixBlendMode: blendMode, opacity: 0 }} aria-hidden="true">
      {CORNER_ANGLES.map((angle, i) => (
        <span
          key={angle}
          ref={element => {
            cornerRefs.current[i] = element;
          }}
          className="target-cursor__corner"
        />
      ))}
      <span ref={dotRef} className="target-cursor__dot" />
      <span ref={labelRef} className="target-cursor__label" />
    </div>,
    host
  );
};

export default TargetCursor;

```

### Component CSS
```css
.target-cursor {
  position: fixed;
  inset: 0;
  z-index: 2147483647;
  pointer-events: none;
}

.target-cursor__corner {
  position: absolute;
  top: 0;
  left: 0;
  box-sizing: border-box;
  border-style: solid;
  border-color: var(--target-cursor-color, #ffffff);
  transform-origin: 0 0;
  will-change: transform;
}

.target-cursor__dot {
  position: absolute;
  top: 0;
  left: 0;
  border-radius: 50%;
  background: var(--target-cursor-color, #ffffff);
  will-change: transform;
}

.target-cursor__label {
  position: absolute;
  top: 0;
  left: 0;
  color: var(--target-cursor-color, #ffffff);
  font-size: 12px;
  font-weight: 500;
  line-height: 1;
  white-space: nowrap;
  opacity: 0;
  will-change: transform, opacity;
}

```

### Integration Instructions
1. Install any listed dependencies.
2. Copy the component source into the appropriate directory in the project.
3. Import the CSS file alongside the component.
4. Import and render the component using the usage example above as a starting point.
5. Adjust props as needed for the specific use case — refer to the props table for all available options.

### More from React Bits
The full library index, including everything reactbits.dev offers, is at https://reactbits.dev/llms.txt — fetch it if this component is not the right fit or the project needs more pieces.

### Use Lenis for smooth scrolling
```
npm i lenis

yarn add lenis

pnpm add lenis

import Lenis from 'lenis'

// Initialize Lenis
const lenis = new Lenis({
  autoRaf: true,
});

// Listen for the scroll event and log the event data
lenis.on('scroll', (e) => {
  console.log(e);
});

// Initialize Lenis
const lenis = new Lenis();

// Use requestAnimationFrame to continuously update the scroll
function raf(time) {
  lenis.raf(time);
  requestAnimationFrame(raf);
}

requestAnimationFrame(raf);

import 'lenis/dist/lenis.css'

<link rel="stylesheet" href="https://unpkg.com/lenis@1.3.27/dist/lenis.css">

// Initialize a new Lenis instance for smooth scrolling
const lenis = new Lenis();

// Synchronize Lenis scrolling with GSAP's ScrollTrigger plugin
lenis.on('scroll', ScrollTrigger.update);

// Add Lenis's requestAnimationFrame (raf) method to GSAP's ticker
// This ensures Lenis's smooth scroll animation updates on each GSAP tick
gsap.ticker.add((time) => {
  lenis.raf(time * 1000); // Convert time from seconds to milliseconds
});

// Disable lag smoothing in GSAP to prevent any delay in scroll animations
gsap.ticker.lagSmoothing(0);


<link rel="stylesheet" href="https://unpkg.com/lenis@1.3.27/dist/lenis.css">
<script src="https://unpkg.com/lenis@1.3.27/dist/lenis.min.js"></script> 
<script>new Lenis({ autoRaf: true, autoToggle: true, anchors: true, allowNestedScroll: true, naiveDimensions: true, stopInertiaOnNavigate: true })</script>

const lenis = new Lenis({
  allowNestedScroll: true,
})

<div data-lenis-prevent>scrollable content</div>

<div id="modal">scrollable content</div>

const lenis = new Lenis({
  prevent: (node) => node.id === 'modal',
})

new Lenis({
  anchors: true
})

new Lenis({
  anchors: {
    offset: 100,
    onComplete: ()=>{
      console.log('scrolled to anchor')
    }
  }
})

const lenis = new Lenis({
  respectReducedMotion: false,
})
```



## ONCE YOU ARE DONE:
### CHECK CODE 5 TIMES
### PUSH AND PULL INTO REPO
### COMMIT ALL CHANGES
