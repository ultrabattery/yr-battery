import { useState, useEffect, useRef } from "react";
import * as THREE from "three";

// ── Constants ─────────────────────────────────────────────────────────────────
const CELL_VOLTAGE = 3.6;
const CELL_CAPACITY_AH = 5.0;
const SERIES_MAP = { 12: 4, 24: 7, 36: 10, 48: 13, 60: 17, 72: 20 };
const PRICE_PER_KWH = 3200;
const BT_ADDON_PRICE = 180;
const WA_NUMBER = "972549252094";

// Design tokens
const C = {
  bg:       "#090b0f",
  card:     "#0f1218",
  cardBord: "#1c2230",
  elevated: "#151b24",
  cyan:     "#00d4ff",
  green:    "#00ff88",
  amber:    "#f5c842",
  gray:     "#4a5568",
  grayLt:   "#8892a4",
  white:    "#e2e8f0",
  red:      "#ff4444",
  blue:     "#3b82f6",
};


const BMS_PROFILES = {
  20:  { color: 0x002211, acc: 0x00ee66, accentCSS: "#00ee66", w: 0.30, d: 0.28, chips: 1, caps: 0, heatsink: false },
  30:  { color: 0x002211, acc: 0x00ee66, accentCSS: "#00ee66", w: 0.32, d: 0.28, chips: 1, caps: 0, heatsink: false },
  40:  { color: 0x002211, acc: 0x00ee66, accentCSS: "#00ee66", w: 0.34, d: 0.30, chips: 1, caps: 1, heatsink: false },
  50:  { color: 0x001133, acc: 0x3399ff, accentCSS: "#3399ff", w: 0.40, d: 0.32, chips: 2, caps: 1, heatsink: false },
  60:  { color: 0x001133, acc: 0x3399ff, accentCSS: "#3399ff", w: 0.44, d: 0.34, chips: 2, caps: 1, heatsink: false },
  70:  { color: 0x111100, acc: 0xddcc00, accentCSS: "#ddcc00", w: 0.50, d: 0.36, chips: 3, caps: 2, heatsink: false },
  80:  { color: 0x111100, acc: 0xddcc00, accentCSS: "#ddcc00", w: 0.54, d: 0.38, chips: 3, caps: 2, heatsink: false },
  90:  { color: 0x221100, acc: 0xff8800, accentCSS: "#ff8800", w: 0.60, d: 0.40, chips: 4, caps: 3, heatsink: true  },
  100: { color: 0x221100, acc: 0xff8800, accentCSS: "#ff8800", w: 0.65, d: 0.42, chips: 4, caps: 3, heatsink: true  },
  110: { color: 0x220000, acc: 0xff3333, accentCSS: "#ff3333", w: 0.74, d: 0.46, chips: 5, caps: 4, heatsink: true  },
  120: { color: 0x220000, acc: 0xff3333, accentCSS: "#ff3333", w: 0.82, d: 0.50, chips: 5, caps: 5, heatsink: true  },
};

const REVIEWS = [
  { name: "אבי כ.",    stars: 5, city: "ת״א",       text: "סוללה 48V 60Ah לאופנוע חשמלי. הגיעה ארוזה מעולה עם בדיקת מתח מלאה. מאז 7 חודשים בשימוש יומי — מחזיקה כמו ביום הראשון. הBMS לא טיפס פעם אחת.", date: "ינו׳ 2025",   use: "אופנוע חשמלי",  verified: true },
  { name: "מיכל ר.",   stars: 5, city: "חיפה",      text: "72V 100Ah לעגלת גולף. שירות מהיר ומקצועי, יוסי הסביר הכל בסבלנות. הסוללה מחזיקה 36 חורים ברמה מדהימה. ממליצה לכל מי שצריך פתרון אמין.", date: "פבר׳ 2025",  use: "עגלת גולף",   verified: true },
  { name: "יוסי ל.",   stars: 5, city: "באר שבע",   text: "24V 50Ah למערכת סולארית בנגב. עברנו 4 קיצות מ-2024 וזה עדיין עובד בלי נפילה אחת. Grade A זה לא שיווק — זה אמיתי. גם חשמלאי שבדק אמר שהריתוך נקי מאוד.", date: "מרץ 2025",   use: "סולארי ביתי", verified: true },
  { name: "דנה ש.",    stars: 4, city: "רחובות",    text: "48V 30Ah לקורקינט. עובדת מצוין ונותנת טווח טוב יותר מהמקורית. הורדתי כוכב כי ההמתנה הייתה 12 ימים, אבל הסוללה עצמה מושלמת.", date: "מרץ 2025",   use: "קורקינט",     verified: true },
  { name: "רמי א.",    stars: 5, city: "ירושלים",   text: "36V 20Ah לאופניים חשמליים בירושלים ההררית. הגבעות לא מפחידות אותה בכלל. Bluetooth BMS מעולה — רואה כל תא בנפרד מהטלפון. שווה את ה-180 שקל הנוסף.", date: "אפר׳ 2025",  use: "אופניים ח.׳",  verified: true },
  { name: "שרה מ.",    stars: 5, city: "נתניה",     text: "72V 40Ah לשלדת אופנוע שבנינו. הסוללה התאימה לתא הבטרייה בדיוק כמו שתיאמנו. הBMS 80A מטפל בזרמי הפעלה בצורה יפה, לא חם בכלל.", date: "מאי 2025",   use: "בנייה עצמית", verified: false },
  { name: "ניר ב.",    stars: 5, city: "כפר סבא",   text: "48V 60Ah לעגלת הובלה חקלאית. פועלת 10 שעות ביום שישה ימים בשבוע. כבר 5 חודשים, אין ירידה בביצועים. שירות הלקוחות ענה גם בשבת בהודעה.", date: "יוני 2025",   use: "חקלאות",      verified: true },
  { name: "תמר ג.",    stars: 3, city: "אשדוד",     text: "24V 30Ah לאופניים. הסוללה עצמה טובה אבל הגיעה עם מגע חלש באחד מהמחברים. טיפלו בזה במהירות ושלחו חלק חינם, אז בסוף יצא בסדר. יורד על החוויה הראשונית.", date: "יוני 2025",   use: "אופניים ח.׳",  verified: true },
  { name: "עמית ד.",   stars: 5, city: "מודיעין",   text: "מערכת 48V 100Ah לבית עם פאנלים סולאריים. חשמלאי מוסמך שבדק לפני חיבור אמר שהבנייה מקצועית ועומדת בתקן. חסכנו ~₪400 בחשמל כבר בחודש הראשון.", date: "יולי 2025",   use: "סולארי ביתי", verified: true },
  { name: "גל ט.",     stars: 4, city: "הרצליה",    text: "60V 20Ah לקורקינט ספורטיבי. נותנת בוסט מרגש. BMS 50A מאוד יציב. ירדתי כוכב כי ציפיתי לתיעוד מפורט יותר באנגלית. בכל זאת — מוצר מעולה.", date: "אוג׳ 2025",   use: "קורקינט",     verified: true },
];


// ── 3D Viewer ─────────────────────────────────────────────────────────────────
function Battery3D({ S, P, packCols, shape, floors: floorsProp = 1, bms }) {
  const mountRef = useRef(null);
  const isDragging = useRef(false);
  const lastMouse  = useRef({ x: 0, y: 0 });
  const lastTouch  = useRef(null);
  const rotRef     = useRef({ x: 0.22, y: 0.55 });
  const animRef    = useRef(null);
  const autoRot    = useRef(true);
  const bp         = BMS_PROFILES[bms] || BMS_PROFILES[60];

  useEffect(() => {
    const el = mountRef.current;
    if (!el) return;
    const W = el.clientWidth, H = el.clientHeight;

    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x060a0f);

    const cam = new THREE.PerspectiveCamera(38, W / H, 0.1, 300);

    const rdr = new THREE.WebGLRenderer({ antialias: true });
    rdr.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    rdr.setSize(W, H);
    rdr.shadowMap.enabled = true;
    rdr.shadowMap.type = THREE.PCFSoftShadowMap;
    el.appendChild(rdr.domElement);

    // Bright studio lights
    scene.add(new THREE.AmbientLight(0xcce8ff, 1.1));
    const sun = new THREE.DirectionalLight(0xfffcf5, 2.5);
    sun.position.set(5, 18, 12); sun.castShadow = true; scene.add(sun);
    const back = new THREE.DirectionalLight(0x6699ff, 1.3);
    back.position.set(-10, 5, -6); scene.add(back);
    const front = new THREE.DirectionalLight(0xffffff, 1.2);
    front.position.set(2, 0, 20); scene.add(front);
    const topL = new THREE.PointLight(0x00d4ff, 2.0, 80);
    topL.position.set(0, 12, 0); scene.add(topL);
    const bmsL = new THREE.PointLight(bp.acc, 2.8, 30);
    scene.add(bmsL);
    const sideR = new THREE.PointLight(0xffffff, 1.4, 50);
    sideR.position.set(12, 2, 0); scene.add(sideR);

    // Floor grid
    const grid = new THREE.GridHelper(50, 50, 0x0d1520, 0x090f18);
    scene.add(grid);

    const pivot = new THREE.Group();
    scene.add(pivot);


    // ══════════════════════════════════════════════════════════════════════════
    // LAYOUT — one tight block, groups in gridCols × gridRows
    // "double" shape = 2 vertical floors, same X/Z footprint
    // ══════════════════════════════════════════════════════════════════════════
    const isDouble       = shape === "double" || floorsProp === 2;
    const floors         = isDouble ? 2 : 1;
    const dispSPerFloor  = isDouble ? Math.ceil(Math.min(S, 20) / 2) : Math.min(S, 20);

    const cR = 0.155, cH = 1.05;
    const floorGap = 0.18;                             // gap between floors (for double)
    const floorStep = cH + floorGap;                   // Y distance between floor origins
    const gap = 0.20, step = cR * 2 + gap;

    const dispS    = dispSPerFloor;
    const dispP    = Math.min(P, 8);
    const gridCols = Math.max(1, Math.min(packCols, dispS));
    const gridRows = Math.ceil(dispS / gridCols);

    // Tight packing: no gap between rows — rows share Z space directly
    const groupZspan = (dispP - 1) * step;
    const rowPitch   = groupZspan + step * 1.05;       // just enough room for series bridge

    const gCX = (gc) => gc * step  - (gridCols - 1) * step  / 2;
    const gCZ = (gr) => gr * rowPitch - (gridRows - 1) * rowPitch / 2;
    // Y origin per floor
    const floorY = (fi) => fi * floorStep;

    const pHalf  = step * 0.60;
    const packX0 = gCX(0)          - pHalf;
    const packX1 = gCX(gridCols-1) + pHalf;
    const packZ0 = gCZ(0)          - groupZspan/2 - pHalf;
    const packZ1 = gCZ(gridRows-1) + groupZspan/2 + pHalf;
    const cW     = packX1 - packX0;
    const cDp    = packZ1 - packZ0;
    const midX   = (packX0 + packX1) / 2;

    // Nickel geometry constants
    const nickThk  = 0.018;
    const nickW    = cR * 1.30;
    const stripLen = step + cR * 0.4;
    // per floor: top/bot Y offsets
    const topY1 = (fi) =>  cH/2 + floorY(fi) + nickThk * 0.5 + 0.003;
    const topY2 = (fi) =>  cH/2 + floorY(fi) + nickThk * 1.5 + 0.005;
    const botY1 = (fi) => -cH/2 + floorY(fi) - nickThk * 0.5 - 0.003;
    const botY2 = (fi) => -cH/2 + floorY(fi) - nickThk * 1.5 - 0.005;
    // "topmost" surface for BMS / wires = floor (floors-1) top
    const packTopY2 = topY2(floors - 1);

    // ── Materials ─────────────────────────────────────────────────────────────
    const mCell  = new THREE.MeshStandardMaterial({ color: 0xb87c00, metalness: 0.80, roughness: 0.16 });
    const mWrap  = new THREE.MeshStandardMaterial({ color: 0x050300, transparent: true, opacity: 0.38, roughness: 0.96 });
    const mCapM  = new THREE.MeshStandardMaterial({ color: 0xe4e4e4, metalness: 0.97, roughness: 0.03 });
    const mNegM  = new THREE.MeshStandardMaterial({ color: 0x1a1a1a, metalness: 0.85, roughness: 0.35 });
    const mNickP = new THREE.MeshStandardMaterial({ color: 0xd8d8d8, metalness: 1.0,  roughness: 0.04 });
    const mNickS = new THREE.MeshStandardMaterial({ color: 0x999999, metalness: 0.98, roughness: 0.10 });
    const mFrame = new THREE.MeshStandardMaterial({ color: 0x1a2a3a, metalness: 0.85, roughness: 0.25 });
    const mGlass = new THREE.MeshStandardMaterial({ color: 0x4488bb, transparent: true, opacity: 0.06  });
    const mTopGl = new THREE.MeshStandardMaterial({ color: 0x88aacc, transparent: true, opacity: 0.08  });
    // Double-floor inter-floor connector material
    const mFloorBridge = new THREE.MeshStandardMaterial({ color: 0xbbbbbb, metalness: 0.98, roughness: 0.06 });

    const cellGeo = new THREE.CylinderGeometry(cR, cR, cH, 22);
    const slvGeo  = new THREE.CylinderGeometry(cR+0.004, cR+0.004, cH*0.86, 22);
    const capGeo  = new THREE.CylinderGeometry(cR*0.40, cR*0.40, 0.026, 14);
    const ringGeo = new THREE.TorusGeometry(cR*0.72, 0.009, 8, 18);
    const negGeo  = new THREE.CylinderGeometry(cR, cR, 0.013, 20);

    // ── BUILD CELLS + NICKEL ──────────────────────────────────────────────────
    for (let fi = 0; fi < floors; fi++) {
      const fy = floorY(fi);

      for (let s = 0; s < dispS; s++) {
        const gc = s % gridCols;
        const gr = Math.floor(s / gridCols);
        const gx = gCX(gc);
        const gz = gCZ(gr);

        for (let p = 0; p < dispP; p++) {
          const z = gz + p * step - (dispP - 1) * step / 2;
          const cell = new THREE.Mesh(cellGeo, mCell.clone());
          cell.castShadow = true; cell.position.set(gx, fy, z); pivot.add(cell);
          const slv = new THREE.Mesh(slvGeo, mWrap.clone());
          slv.position.set(gx, fy, z); pivot.add(slv);
          const cap = new THREE.Mesh(capGeo, mCapM);
          cap.position.set(gx, fy + cH/2 + 0.013, z); pivot.add(cap);
          const ring = new THREE.Mesh(ringGeo, mCapM.clone());
          ring.rotation.x = Math.PI/2; ring.position.set(gx, fy + cH/2 + 0.003, z); pivot.add(ring);
          const neg = new THREE.Mesh(negGeo, mNegM);
          neg.position.set(gx, fy - cH/2 - 0.006, z); pivot.add(neg);
        }

        // Within-group nickel (double-layer strips)
        for (let p = 0; p < dispP; p++) {
          const z = gz + p * step - (dispP - 1) * step / 2;
          if (p < dispP - 1) {
            [topY1(fi), topY2(fi)].forEach(ty => {
              const ns = new THREE.Mesh(new THREE.BoxGeometry(nickW, nickThk, stripLen), mNickP.clone());
              ns.position.set(gx, ty, z + step/2); pivot.add(ns);
            });
            [botY1(fi), botY2(fi)].forEach(by => {
              const ns = new THREE.Mesh(new THREE.BoxGeometry(nickW, nickThk, stripLen), mNickP.clone());
              ns.position.set(gx, by, z + step/2); pivot.add(ns);
            });
          }
          if (p === 0 || p === dispP - 1) {
            [topY1(fi), topY2(fi), botY1(fi), botY2(fi)].forEach(y => {
              const sc = new THREE.Mesh(new THREE.BoxGeometry(nickW, nickThk, cR*1.5), mNickP.clone());
              sc.position.set(gx, y, z); pivot.add(sc);
            });
          }
        }

        // Series bridge between groups (within same floor)
        if (s < dispS - 1) {
          const nextGc  = (s + 1) % gridCols;
          const nextGr  = Math.floor((s + 1) / gridCols);
          const nextGx  = gCX(nextGc);
          const nextGz  = gCZ(nextGr);
          const isEven  = (s % 2 === 0);

          if (gr === nextGr) {
            // Same row — bridge in X
            const bridgeY = isEven ? botY2(fi) : topY2(fi);
            const edgeZ   = isEven
              ? gz - (dispP - 1) * step / 2 - pHalf * 0.32
              : gz + (dispP - 1) * step / 2 + pHalf * 0.32;
            const bridge = new THREE.Mesh(
              new THREE.BoxGeometry(step * 0.95, nickThk * 1.5, nickW * 1.1),
              mNickS.clone()
            );
            bridge.position.set(gx + step / 2, bridgeY, edgeZ); pivot.add(bridge);
          } else {
            // Row wrap — U-bridge at end of row
            const bridgeY = isEven ? botY2(fi) : topY2(fi);
            const sideX   = gx + (gc === gridCols - 1 ? 1 : -1) * (pHalf * 0.52);
            const midZ    = (gz + nextGz) / 2;
            const vLen    = Math.abs(nextGz - gz) + nickW;
            const vBridge = new THREE.Mesh(
              new THREE.BoxGeometry(nickW * 1.1, nickThk * 1.5, vLen),
              mNickS.clone()
            );
            vBridge.position.set(sideX, bridgeY, midZ); pivot.add(vBridge);
          }
        }
      }

      // Inter-floor bridge (for double: last group of floor 0 connects to first of floor 1)
      if (isDouble && fi === 0) {
        const bridgeH  = floorGap + nickThk * 4;
        const bridgeGx = gCX((dispS - 1) % gridCols);
        const bridgeGz = gCZ(Math.floor((dispS - 1) / gridCols));
        const brgZ     = bridgeGz + (dispP - 1) * step / 2 + pHalf * 0.4;
        // vertical nickel strap connecting top of floor 0 to bottom of floor 1
        const floorLink = new THREE.Mesh(
          new THREE.BoxGeometry(nickW * 1.2, bridgeH, nickW * 1.2),
          mFloorBridge.clone()
        );
        floorLink.position.set(bridgeGx, topY2(0) + bridgeH / 2, brgZ); pivot.add(floorLink);
      }
    }

    // ── TRANSPARENT CASING ────────────────────────────────────────────────────
    const cHh = cH + 0.40;
    
    [cH/2+0.16, -cH/2-0.04].forEach(py => {
      const pl = new THREE.Mesh(new THREE.BoxGeometry(cW, 0.020, cDp), mTopGl.clone());
      pl.position.set(midX, py, 0); pivot.add(pl);
    });
    [[0.006,cHh,cDp,[packX0,0,0]],[0.006,cHh,cDp,[packX1,0,0]],
     [cW,cHh,0.006,[midX,0,packZ0]],[cW,cHh,0.006,[midX,0,packZ1]]].forEach(([w,h,d,pos]) => {
      const wl = new THREE.Mesh(new THREE.BoxGeometry(w,h,d), mGlass.clone());
      wl.position.set(...pos); pivot.add(wl);
    });
    [[packX0,packZ0],[packX1,packZ0],[packX0,packZ1],[packX1,packZ1]].forEach(([ex,ez]) => {
      const e = new THREE.Mesh(new THREE.BoxGeometry(0.045,cHh,0.045), mFrame.clone());
      e.position.set(ex,0,ez); pivot.add(e);
    });

    // ══════════════════════════════════════════════════════════════════════════
    // BMS — FLAT ON TOP, REALISTIC SIZE (real Daly BMS is ~120×65×8mm)
    // Scale: 1 unit ≈ pack width / dispS groups. Keep it proportional but compact.
    const bmsH    = 0.035;                              // PCB thickness (thin, realistic)
    const bmsLenX = Math.min(cW * 0.60, 2.8);          // shorter than pack — real BMS is not full-width
    const bmsDenZ = Math.min(cDp * 0.55, 1.4);         // narrower than pack depth
    const bmsX    = midX;
    const bmsZ    = 0;
    const bmsY    = packTopY2 + bmsH/2 + 0.042;

    bmsL.position.set(bmsX, bmsY + 0.6, bmsZ);

    // Foam spacer (thin double-sided tape / foam pad)
    const mFoam = new THREE.MeshStandardMaterial({ color: 0x1a3322, roughness: 0.95 });
    const foam  = new THREE.Mesh(new THREE.BoxGeometry(bmsLenX*0.96, 0.016, bmsDenZ*0.94), mFoam);
    foam.position.set(bmsX, packTopY2 + 0.010, bmsZ); pivot.add(foam);

    const mBMS  = new THREE.MeshStandardMaterial({ color: bp.color, metalness: 0.10, roughness: 0.68 });
    const mTr   = new THREE.MeshStandardMaterial({ color: bp.acc, emissive: bp.acc, emissiveIntensity: 0.38, metalness: 0.7 });
    const mChip = new THREE.MeshStandardMaterial({ color: 0x0c0c0c, metalness: 0.5, roughness: 0.6 });
    const mLED  = new THREE.MeshStandardMaterial({ color: bp.acc, emissive: bp.acc, emissiveIntensity: 1.8 });
    const mCapB = new THREE.MeshStandardMaterial({ color: 0x060616, roughness: 0.7 });
    const mCapT = new THREE.MeshStandardMaterial({ color: bp.acc, emissive: bp.acc, emissiveIntensity: 0.55 });
    const mFin  = new THREE.MeshStandardMaterial({ color: 0x8090a0, metalness: 0.90, roughness: 0.25 });
    const mConn = new THREE.MeshStandardMaterial({ color: 0x141414, metalness: 0.3, roughness: 0.8 });

    // PCB board
    const board = new THREE.Mesh(new THREE.BoxGeometry(bmsLenX, bmsH, bmsDenZ), mBMS);
    board.position.set(bmsX, bmsY, bmsZ); pivot.add(board);

    // ── BMS text label (canvas texture) ──────────────────────────────────────
    const makeLabel = (text, w = 256, h = 64) => {
      const cv  = document.createElement("canvas");
      cv.width  = w; cv.height = h;
      const ctx = cv.getContext("2d");
      ctx.clearRect(0, 0, w, h);
      // PCB silk-screen style: white text on transparent
      ctx.font = "bold 28px monospace";
      ctx.fillStyle = "#ffffff";
      ctx.textAlign = "center";
      ctx.textBaseline = "middle";
      ctx.fillText(text, w / 2, h / 2 - 4);
      ctx.font = "11px monospace";
      ctx.fillStyle = "#aacccc";
      ctx.fillText(`DALY SMART ${bms}A`, w / 2, h / 2 + 18);
      const tex = new THREE.CanvasTexture(cv);
      return new THREE.MeshBasicMaterial({ map: tex, transparent: true, depthWrite: false });
    };
    // Place label on TOP face of PCB
    const labelGeo = new THREE.PlaneGeometry(bmsLenX * 0.58, bmsDenZ * 0.42);
    const labelMesh = new THREE.Mesh(labelGeo, makeLabel(`BMS ${bms}A`));
    labelMesh.rotation.x = -Math.PI / 2;
    labelMesh.position.set(bmsX - bmsLenX * 0.10, bmsY + bmsH / 2 + 0.001, bmsZ - bmsDenZ * 0.08);
    pivot.add(labelMesh);

    // PCB silkscreen border lines
    const mSilk = new THREE.MeshBasicMaterial({ color: 0xeeeeff, transparent: true, opacity: 0.18 });
    const borderGeo = new THREE.EdgesGeometry(new THREE.BoxGeometry(bmsLenX * 0.96, 0.001, bmsDenZ * 0.96));
    const border = new THREE.LineSegments(borderGeo, new THREE.LineBasicMaterial({ color: 0x88aacc, transparent: true, opacity: 0.35 }));
    border.position.set(bmsX, bmsY + bmsH / 2 + 0.001, bmsZ); pivot.add(border);

    // Trace lines (copper tracks)
    for (let t = 0; t < 4; t++) {
      const tz = -bmsDenZ * 0.35 + t * (bmsDenZ * 0.24);
      const tr = new THREE.Mesh(new THREE.BoxGeometry(bmsLenX * 0.88, bmsH * 1.01, 0.008), mTr.clone());
      tr.position.set(bmsX, bmsY, tz); pivot.add(tr);
    }

    // MOSFET chips (the big ones, spread across board)
    const chipW = bmsLenX / Math.max(bp.chips + 1, 2);
    for (let c = 0; c < bp.chips; c++) {
      const cx2  = -bmsLenX * 0.32 + c * (bmsLenX * 0.64 / Math.max(bp.chips - 1, 1));
      const chip = new THREE.Mesh(new THREE.BoxGeometry(chipW * 0.55, bmsH + 0.018, bmsDenZ * 0.30), mChip.clone());
      chip.position.set(bmsX + cx2, bmsY, bmsZ + bmsDenZ * 0.12); pivot.add(chip);
      // chip marking line
      const mark = new THREE.Mesh(new THREE.BoxGeometry(chipW * 0.50, bmsH * 1.02, 0.006), mTr.clone());
      mark.position.set(bmsX + cx2, bmsY, bmsZ + bmsDenZ * 0.12 - bmsDenZ * 0.06); pivot.add(mark);
    }

    // Capacitors (small electrolytic, on one end)
    if (bp.caps > 0) {
      for (let c = 0; c < Math.min(bp.caps, 3); c++) {
        const capH2 = 0.08 + (bms / 120) * 0.06;
        const capR2 = 0.018 + (bms / 120) * 0.008;
        const cx2   = -bmsLenX * 0.38 + c * (bmsLenX * 0.22 / Math.max(bp.caps - 1, 1));
        const capB  = new THREE.Mesh(new THREE.CylinderGeometry(capR2, capR2, capH2, 12), mCapB.clone());
        capB.position.set(bmsX + cx2, bmsY + bmsH / 2 + capH2 / 2, -bmsDenZ * 0.30); pivot.add(capB);
        const capT2 = new THREE.Mesh(new THREE.CylinderGeometry(capR2 * 0.88, capR2 * 0.88, 0.005, 12), mCapT.clone());
        capT2.position.set(bmsX + cx2, bmsY + bmsH / 2 + capH2, -bmsDenZ * 0.30); pivot.add(capT2);
      }
    }

    // Heatsink (slim fins, only on high-power BMS)
    if (bp.heatsink) {
      const fc = bms >= 110 ? 7 : 5;
      for (let f = 0; f < fc; f++) {
        const fx  = -bmsLenX * 0.30 + f * (bmsLenX * 0.60 / (fc - 1));
        const fin = new THREE.Mesh(new THREE.BoxGeometry(0.010, 0.090, bmsDenZ * 0.70), mFin.clone());
        fin.position.set(bmsX + fx, bmsY + bmsH / 2 + 0.045, bmsZ); pivot.add(fin);
      }
    }

    // LEDs (3mm, in a row near edge)
    const ledCount = Math.min(3, bp.chips);
    for (let l = 0; l < ledCount; l++) {
      const lx  = bmsLenX * 0.28 + l * 0.10;
      const led = new THREE.Mesh(new THREE.SphereGeometry(0.014, 8, 8), mLED.clone());
      led.position.set(bmsX + lx, bmsY + bmsH / 2 + 0.014, bmsDenZ * 0.35); pivot.add(led);
    }

    // Balance wire header (JST-style connector strip, underside)
    const hdrCount = Math.min(dispS, 8);
    for (let c = 0; c < hdrCount; c++) {
      const cx2 = -bmsLenX * 0.36 + c * (bmsLenX * 0.72 / Math.max(hdrCount - 1, 1));
      const hdr = new THREE.Mesh(new THREE.BoxGeometry(0.045, 0.030, 0.090), mConn.clone());
      hdr.position.set(bmsX + cx2, bmsY - bmsH / 2 - 0.016, bmsZ + bmsDenZ * 0.30); pivot.add(hdr);
    }

    // B+/B- power connector block (one end of board)
    const mConnB = new THREE.MeshStandardMaterial({ color: 0x0e0e0e, metalness: 0.3, roughness: 0.8 });
    const pwrConn = new THREE.Mesh(new THREE.BoxGeometry(0.10, 0.055, 0.048), mConnB);
    pwrConn.position.set(bmsX + bmsLenX * 0.46, bmsY, bmsZ + bmsDenZ * 0.38); pivot.add(pwrConn);
    // screw terminals on connector
    const mScrew = new THREE.MeshStandardMaterial({ color: 0xcccccc, metalness: 0.9, roughness: 0.2 });
    [-0.012, 0.012].forEach(dz => {
      const sc = new THREE.Mesh(new THREE.CylinderGeometry(0.010, 0.010, 0.020, 8), mScrew.clone());
      sc.rotation.z = Math.PI / 2;
      sc.position.set(bmsX + bmsLenX * 0.50, bmsY + 0.010, bmsZ + bmsDenZ * 0.38 + dz); pivot.add(sc);
    });

    // ══════════════════════════════════════════════════════════════════════════
    // WIRES — realistic routing from cells UP into BMS bottom
    // ══════════════════════════════════════════════════════════════════════════
    const addWire = (pts, color, r = 0.027) => {
      const curve = new THREE.CatmullRomCurve3(pts, false, "centripetal", 0.28);
      const geo   = new THREE.TubeGeometry(curve, 32, r, 8, false);
      const c3    = new THREE.Color(color);
      const mat   = new THREE.MeshStandardMaterial({
        color: c3, metalness: 0.50, roughness: 0.40,
        emissive: c3, emissiveIntensity: 0.0,
      });
      pivot.add(new THREE.Mesh(geo, mat));
    };

    const wR  = 0.027;
    const wRb = 0.011;
    const bmsBot = bmsY - bmsH/2;

    // Group 0 is at gCX(0), gCZ(0) — first group in grid
    const g0x  = gCX(0);
    const g0gz = gCZ(0);
    addWire([
      new THREE.Vector3(g0x,       packTopY2 + wR,   g0gz + (dispP-1)*step/2 + step*0.3),
      new THREE.Vector3(g0x,       bmsBot - 0.06, g0gz + (dispP-1)*step/2 + step*0.3),
      new THREE.Vector3(g0x + 0.1, bmsBot - 0.02, bmsZ + bmsDenZ*0.28),
      new THREE.Vector3(bmsX - bmsLenX*0.40, bmsBot, bmsZ + bmsDenZ*0.28),
    ], 0xcc0000, wR);

    // Last group
    const lastGc   = (dispS - 1) % gridCols;
    const lastGr   = Math.floor((dispS - 1) / gridCols);
    const gLx      = gCX(lastGc);
    const gLgz     = gCZ(lastGr);
    const lastIsOdd = ((dispS - 1) % 2 === 1);
    const gLtopY   = lastIsOdd ? botY2(0) : topY2(0);
    const gLz      = lastIsOdd
      ? gLgz - (dispP-1)*step/2 - step*0.22
      : gLgz + (dispP-1)*step/2 + step*0.22;
    addWire([
      new THREE.Vector3(gLx,       gLtopY + (lastIsOdd ? -wR : wR), gLz),
      new THREE.Vector3(gLx,       bmsBot - 0.06,                    gLz),
      new THREE.Vector3(gLx - 0.1, bmsBot - 0.02,                    bmsZ + bmsDenZ*0.28),
      new THREE.Vector3(bmsX + bmsLenX*0.40, bmsBot,                 bmsZ + bmsDenZ*0.28),
    ], 0x141414, wR);

    // Balance wires — one per junction, spread across BMS header
    const balN = Math.min(dispS, 9);
    for (let i = 0; i < balN; i++) {
      const si       = Math.round(i * (dispS - 1) / Math.max(balN - 1, 1));
      const sgc      = si % gridCols;
      const sgr      = Math.floor(si / gridCols);
      const isEven   = (si % 2 === 0);
      const tapX     = gCX(sgc);
      const tapY     = isEven ? botY2(0) : topY2(0);
      const tapZ     = isEven
        ? gCZ(sgr) - (dispP-1)*step/2 - step*0.22
        : gCZ(sgr) + (dispP-1)*step/2 + step*0.22;
      const bmsHdrX  = bmsX - bmsLenX*0.38 + i*(bmsLenX*0.76/Math.max(balN-1,1));
      const bmsHdrZ  = bmsZ + bmsDenZ*0.28;
      addWire([
        new THREE.Vector3(tapX,    tapY + (isEven ? -wRb : wRb), tapZ),
        new THREE.Vector3(tapX,    bmsBot - 0.04,                 tapZ),
        new THREE.Vector3(bmsHdrX, bmsBot - 0.01,                 bmsHdrZ),
      ], 0xccbb00, wRb);
    }

    // Output wires
    const outStartX = bmsX + bmsLenX*0.45;
    const outStartY = bmsY;
    const outStartZ = bmsZ + bmsDenZ*0.52;
    addWire([
      new THREE.Vector3(outStartX,        outStartY,        outStartZ),
      new THREE.Vector3(outStartX + 0.10, outStartY - 0.05, outStartZ + 0.12),
      new THREE.Vector3(outStartX + 0.10, outStartY - 0.30, outStartZ + 0.20),
    ], 0xcc0000, 0.034);
    addWire([
      new THREE.Vector3(outStartX,        outStartY,        outStartZ + 0.09),
      new THREE.Vector3(outStartX + 0.10, outStartY - 0.05, outStartZ + 0.22),
      new THREE.Vector3(outStartX + 0.10, outStartY - 0.30, outStartZ + 0.32),
    ], 0x111111, 0.034);

    // Output terminals
    const mTP = new THREE.MeshStandardMaterial({ color: 0xf5c842, metalness: 0.95, roughness: 0.06, emissive: 0xf5c842, emissiveIntensity: 0.18 });
    const mTN = new THREE.MeshStandardMaterial({ color: 0x5a5a5a, metalness: 0.94, roughness: 0.10 });
    const tGeo2 = new THREE.CylinderGeometry(0.10, 0.10, 0.22, 14);
    const tPm = new THREE.Mesh(tGeo2, mTP);
    tPm.position.set(outStartX+0.10, outStartY-0.44, outStartZ+0.20); pivot.add(tPm);
    const tNm = new THREE.Mesh(tGeo2, mTN);
    tNm.position.set(outStartX+0.10, outStartY-0.44, outStartZ+0.32); pivot.add(tNm);

    // ── CAMERA — fit entire pack including BMS on top ─────────────────────────
    const bmsTopActual = bmsY + (bp.heatsink ? 0.24 : bmsH/2 + 0.03);
    const packBottom   = -cH/2;
    const packHeight   = bmsTopActual - packBottom;
    const packCenY     = (bmsTopActual + packBottom) / 2;
    const span = Math.max(cW * 1.10, cDp * 1.25, packHeight * 2.0);
    const dist = span / (2 * Math.tan((38/2) * Math.PI / 180)) * 1.28;
    cam.position.set(midX, packCenY + packHeight * 0.35, dist);
    cam.lookAt(midX, packCenY, 0);

    grid.position.y = -cH/2 - 0.90;

    pivot.rotation.x = rotRef.current.x;
    pivot.rotation.y = rotRef.current.y;

    const shadowM = new THREE.Mesh(
      new THREE.CircleGeometry(Math.max(cW, cDp) * 0.90, 36),
      new THREE.MeshBasicMaterial({ color: 0x000000, transparent: true, opacity: 0.38 })
    );
    shadowM.rotation.x = -Math.PI/2; shadowM.position.y = -cH/2 - 0.88; scene.add(shadowM);

    // Animate
    let ang = rotRef.current.y, t = 0;
    const leds = pivot.children.filter(c => c.material?.emissiveIntensity > 0.8);
    const anim = () => {
      animRef.current = requestAnimationFrame(anim);
      if (autoRot.current && !isDragging.current) {
        ang += 0.0028; pivot.rotation.y = ang; rotRef.current.y = ang;
      }
      t += 0.035;
      leds.forEach((m, i) => { m.material.emissiveIntensity = 1.0 + 0.72 * Math.sin(t + i * 1.2); });
      rdr.render(scene, cam);
    };
    anim();

    const onMD = e => { isDragging.current = true; autoRot.current = false; lastMouse.current = { x: e.clientX, y: e.clientY }; };
    const onMM = e => {
      if (!isDragging.current) return;
      const dx = e.clientX - lastMouse.current.x, dy = e.clientY - lastMouse.current.y;
      rotRef.current.y += dx * 0.008;
      rotRef.current.x = Math.max(-1.3, Math.min(1.3, rotRef.current.x + dy * 0.008));
      pivot.rotation.y = rotRef.current.y; pivot.rotation.x = rotRef.current.x;
      lastMouse.current = { x: e.clientX, y: e.clientY };
    };
    const onMU = () => { isDragging.current = false; };
    const onTS = e => { autoRot.current = false; isDragging.current = true; lastTouch.current = { x: e.touches[0].clientX, y: e.touches[0].clientY }; };
    const onTM = e => {
      if (!isDragging.current || !lastTouch.current) return;
      const dx = e.touches[0].clientX - lastTouch.current.x, dy = e.touches[0].clientY - lastTouch.current.y;
      rotRef.current.y += dx * 0.01;
      rotRef.current.x = Math.max(-1.3, Math.min(1.3, rotRef.current.x + dy * 0.01));
      pivot.rotation.y = rotRef.current.y; pivot.rotation.x = rotRef.current.x;
      lastTouch.current = { x: e.touches[0].clientX, y: e.touches[0].clientY }; e.preventDefault();
    };
    const onTE = () => { isDragging.current = false; };
    const onRz = () => { const w = el.clientWidth, h = el.clientHeight; cam.aspect = w / h; cam.updateProjectionMatrix(); rdr.setSize(w, h); };

    el.addEventListener("mousedown", onMD); window.addEventListener("mousemove", onMM); window.addEventListener("mouseup", onMU);
    el.addEventListener("touchstart", onTS, { passive: false }); el.addEventListener("touchmove", onTM, { passive: false }); el.addEventListener("touchend", onTE);
    window.addEventListener("resize", onRz);

    return () => {
      cancelAnimationFrame(animRef.current);
      el.removeEventListener("mousedown", onMD); window.removeEventListener("mousemove", onMM); window.removeEventListener("mouseup", onMU);
      el.removeEventListener("touchstart", onTS); el.removeEventListener("touchmove", onTM); el.removeEventListener("touchend", onTE);
      window.removeEventListener("resize", onRz);
      rdr.dispose();
      if (el.contains(rdr.domElement)) el.removeChild(rdr.domElement);
    };
  }, [S, P, packCols, shape, floorsProp, bms]);


  return (
    <div style={{ position: "relative", width: "100%", height: 460, borderRadius: 12, overflow: "hidden", border: `1px solid ${bp.accentCSS}25` }}>
      <div ref={mountRef} style={{ width: "100%", height: "100%" }} />
      <div style={{ position: "absolute", bottom: 10, left: "50%", transform: "translateX(-50%)", background: "rgba(0,0,0,.7)", backdropFilter: "blur(6px)", color: C.gray, fontSize: 10, padding: "4px 14px", borderRadius: 20, fontFamily: "monospace", pointerEvents: "none", whiteSpace: "nowrap" }}>
        ⟳ גרור לסיבוב · גע ומשוך במובייל
      </div>
      <div style={{ position: "absolute", top: 10, right: 12, background: `${bp.accentCSS}18`, border: `1px solid ${bp.accentCSS}44`, color: bp.accentCSS, fontSize: 9, padding: "3px 10px", borderRadius: 20, fontFamily: "monospace", pointerEvents: "none" }}>
        BMS {bms}A
      </div>
      <div style={{ position: "absolute", top: 10, left: 12, color: C.cyan + "55", fontSize: 9, fontFamily: "monospace", pointerEvents: "none" }}>3D · LIVE</div>
    </div>
  );
}

// ── Shared components ─────────────────────────────────────────────────────────
function Slider({ label, value, values, unit, onChange }) {
  const idx = values.indexOf(value);
  const pct = (idx / (values.length - 1)) * 100;
  return (
    <div style={{ marginBottom: 22 }}>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "baseline", marginBottom: 8 }}>
        <span style={{ color: C.grayLt, fontSize: 12, fontFamily: "monospace", letterSpacing: 1, textTransform: "uppercase" }}>{label}</span>
        <span style={{ color: C.cyan, fontSize: 26, fontWeight: 900, fontFamily: "monospace", textShadow: `0 0 16px ${C.cyan}80`, lineHeight: 1 }}>
          {value}<span style={{ fontSize: 12, color: C.cyan + "55", marginRight: 2 }}>{unit}</span>
        </span>
      </div>
      <div style={{ direction: "ltr", position: "relative", height: 36, display: "flex", alignItems: "center" }}>
        <div style={{ position: "absolute", left: 0, right: 0, height: 4, borderRadius: 2, background: C.cardBord }} />
        <div style={{ position: "absolute", left: 0, width: `${pct}%`, height: 4, borderRadius: 2, background: `linear-gradient(90deg, ${C.cyan}, ${C.green})` }} />
        <input type="range" min={0} max={values.length - 1} step={1} value={idx}
          onChange={e => onChange(values[+e.target.value])}
          style={{ width: "100%", position: "relative", zIndex: 2, WebkitAppearance: "none", appearance: "none", background: "transparent", cursor: "pointer", height: 36 }} />
      </div>
      <div style={{ direction: "ltr", display: "flex", justifyContent: "space-between", marginTop: 1 }}>
        <span style={{ color: C.gray, fontSize: 10, fontFamily: "monospace" }}>{values[0]}{unit}</span>
        <span style={{ color: C.gray, fontSize: 10, fontFamily: "monospace" }}>{values[values.length - 1]}{unit}</span>
      </div>
    </div>
  );
}

const Card = ({ children, accent, style = {} }) => (
  <div style={{ background: C.card, borderRadius: 14, padding: "16px", marginBottom: 12, border: `1px solid ${accent || C.cardBord}`, ...style }}>
    {children}
  </div>
);

const Tag = ({ text, color }) => (
  <span style={{ background: color + "18", border: `1px solid ${color}44`, color, fontSize: 10, fontFamily: "monospace", padding: "2px 8px", borderRadius: 20, fontWeight: 700 }}>{text}</span>
);

function Stars({ n }) {
  return <span>{[1,2,3,4,5].map(i => <span key={i} style={{ color: i <= n ? C.amber : "#222", fontSize: 13 }}>★</span>)}</span>;
}

// ── WhatsApp order helper ─────────────────────────────────────────────────────
function buildWALink(voltage, capAh, bms, cols, rows, bluetooth, realV, realAh, energy, totalPrice) {
  const btText = bluetooth ? " + Bluetooth" : "";
  const msg = `שלום! אני רוצה להזמין סוללה מותאמת:
⚡ מתח: ${voltage}V (${realV}V נומינלי)
🔋 קיבולת: ${capAh}Ah (${realAh}Ah אמיתי)
🛡️ BMS: ${bms}A${btText}
📐 צורה: ${cols} עמודות × ${rows} שורות
🌐 אנרגיה: ${energy}kWh
💰 מחיר: ₪${totalPrice.toLocaleString()}`;
  return `https://wa.me/${WA_NUMBER}?text=${encodeURIComponent(msg)}`;
}

// ── BuilderTab ────────────────────────────────────────────────────────────────
function BuilderTab({ onOpenLead }) {
  const [voltage, setVoltage]     = useState(48);
  const [capAh, setCapAh]         = useState(20);
  const [bms, setBms]             = useState(60);
  const [packCols, setPackCols]      = useState(null);  // null = auto (=S)
  const [floors,   setFloors]        = useState(1);
  const [bluetooth, setBluetooth] = useState(false);
  const [view3D, setView3D]       = useState(true);

  // Countdown to "end of deal" — always shows ~hours left this week
  const [timeLeft, setTimeLeft] = useState({ h: 0, m: 0, s: 0 });
  useEffect(() => {
    const tick = () => {
      const now  = new Date();
      // countdown to next Sunday midnight
      const msTillSunday = (7 - now.getDay()) * 86400000
        - now.getHours() * 3600000 - now.getMinutes() * 60000 - now.getSeconds() * 1000;
      const total = msTillSunday % (48 * 3600000); // cap at 48h for drama
      setTimeLeft({
        h: Math.floor(total / 3600000),
        m: Math.floor((total % 3600000) / 60000),
        s: Math.floor((total % 60000) / 1000),
      });
    };
    tick();
    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []);

  const voltOpts = [12, 24, 36, 48, 60, 72];
  const ahOpts   = Array.from({ length: 20 }, (_, i) => (i + 1) * 5);
  const bmsOpts  = [20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 120];

  const S          = SERIES_MAP[voltage];
  const P          = Math.ceil(capAh / CELL_CAPACITY_AH);
  const totalCells = S * P;
  const realV      = (S * CELL_VOLTAGE).toFixed(1);
  const realAh     = (P * CELL_CAPACITY_AH).toFixed(0);
  const energy     = ((voltage * capAh) / 1000).toFixed(3);
  const weight     = (totalCells * 70 / 1000).toFixed(1);
  const basePrice  = Math.round(parseFloat(energy) * PRICE_PER_KWH);
  const bmsPrice   = bms > 100 ? Math.round(basePrice * 1.1) : basePrice;
  const totalPrice = bmsPrice + (bluetooth ? BT_ADDON_PRICE : 0);

  // ── Discount tiers ──────────────────────────────────────────────────────────
  // >1kWh: free shipping (always shown as value)
  
  // >2kWh: 5% off
  // >3kWh: 8% off
  const kWh = parseFloat(energy);
  const discountPct = kWh < 0.5 ? 0 : Math.min(35, Math.round(kWh * 12));
  const discountAmt = discountPct > 0 ? Math.round(totalPrice * discountPct / 100) : 0;
  const freeBT = false; // removed
  const finalPrice  = totalPrice - discountAmt;

  // With 2 floors, each floor holds ceil(S/2) groups → half the footprint
  const groupsPerFloor = floors === 2 ? Math.ceil(S / 2) : S;
  const effectiveCols  = packCols === null ? groupsPerFloor : Math.max(1, Math.min(packCols, groupsPerFloor));
  const gridRows       = Math.ceil(groupsPerFloor / effectiveCols);
  const waLink = buildWALink(voltage, capAh, bms, effectiveCols, gridRows, bluetooth, realV, realAh, energy, finalPrice);

  // ── Finished battery dimensions (cm) ──────────────────────────────────────
  // Cell: Ø21mm, height 70mm. Pitch (center-to-center): 23mm
  // Casing adds ~5mm each side (2mm wall + 3mm foam/tolerance)
  // BMS on top adds ~12mm height (PCB + foam + space)
  // Double: 2 stacked layers with inter-layer nickel (~3mm gap)
  const PITCH     = 2.3;   // cm center-to-center
  const CASING    = 1.0;   // cm extra each side (wall + tolerance)
  const CELL_H    = 7.2;   // cm — תא + ניקל + סגירה

  const dimL = Math.round((effectiveCols * PITCH + CASING * 2) * 10) / 10;
  const dimW = Math.round((gridRows * P * PITCH + (gridRows > 1 ? (gridRows - 1) * 0.3 : 0) + CASING * 2) * 10) / 10;
  const dimH = floors === 2
    ? Math.round((CELL_H * 2 + 0.5) * 10) / 10
    : CELL_H;

  const pad = n => String(n).padStart(2, "0");

  return (
    <div style={{ maxWidth: 640, margin: "0 auto", padding: "16px 14px 40px" }}>

      {/* ── URGENCY BANNER ────────────────────────────────────────────────────── */}
      <div style={{
        background: "linear-gradient(135deg, #1a0800 0%, #200d00 100%)",
        border: `1px solid ${C.amber}55`, borderRadius: 12, padding: "11px 14px",
        marginBottom: 14, display: "flex", alignItems: "center", justifyContent: "space-between", gap: 10,
      }}>
        <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
          <span style={{ fontSize: 16 }}>🔥</span>
          <div>
            <div style={{ fontSize: 12, fontWeight: 700, color: C.amber }}>מחיר השקה — מבצע שבועי</div>
            <div style={{ fontSize: 10, color: C.gray, marginTop: 1 }}>ייצור בתור · המלאי מוגבל לשבוע זה</div>
          </div>
        </div>
        <div style={{ textAlign: "center", flexShrink: 0 }}>
          <div style={{ display: "flex", gap: 4, alignItems: "center" }}>
            {[pad(timeLeft.h), pad(timeLeft.m), pad(timeLeft.s)].map((v, i) => (
              <span key={i} style={{ display: "flex", alignItems: "center", gap: 4 }}>
                <span style={{ background: C.elevated, border: `1px solid ${C.amber}44`, borderRadius: 6, padding: "3px 7px", fontSize: 16, fontWeight: 900, fontFamily: "monospace", color: C.amber, minWidth: 32, textAlign: "center" }}>{v}</span>
                {i < 2 && <span style={{ color: C.amber, fontWeight: 700, fontSize: 14 }}>:</span>}
              </span>
            ))}
          </div>
          <div style={{ fontSize: 9, color: C.gray, fontFamily: "monospace", marginTop: 3, letterSpacing: 1 }}>שע  דק  שנ</div>
        </div>
      </div>

      {/* ── DISCOUNT TIERS NUDGE ──────────────────────────────────────────────── */}
      <div style={{ display: "flex", gap: 6, marginBottom: 14, overflowX: "auto", paddingBottom: 2 }}>
        {[
          { thresh: 0.5, label: "הנחה 6%",    icon: "💸", reached: kWh >= 0.5, sub: "מעל 0.5kWh" },
          { thresh: 1.0, label: "הנחה 12%",   icon: "🎯", reached: kWh >= 1.0, sub: "מעל 1kWh" },
          { thresh: 1.5, label: "הנחה 18%",   icon: "🔥", reached: kWh >= 1.5, sub: "מעל 1.5kWh" },
          { thresh: 2.0, label: "הנחה 24%",   icon: "⚡", reached: kWh >= 2.0, sub: "מעל 2kWh" },
          { thresh: 3.0, label: "הנחה 35%",   icon: "💥", reached: kWh >= 3.0, sub: "מעל 3kWh" },
        ].map((tier, i) => (
          <div key={i} style={{
            flex: "0 0 auto", minWidth: 80, padding: "8px 10px", borderRadius: 10, textAlign: "center",
            background: tier.reached ? C.cyan + "14" : C.card,
            border: `1px solid ${tier.reached ? C.cyan + "55" : C.cardBord}`,
            transition: "all .3s",
            position: "relative", overflow: "hidden",
          }}>
            {tier.reached && (
              <div style={{ position: "absolute", top: 3, right: 5, fontSize: 9, color: C.cyan, fontWeight: 900 }}>✓</div>
            )}
            <div style={{ fontSize: 18 }}>{tier.icon}</div>
            <div style={{ fontSize: 10, fontWeight: 700, color: tier.reached ? C.cyan : C.grayLt, marginTop: 3 }}>{tier.label}</div>
            <div style={{ fontSize: 9, color: C.gray, marginTop: 1 }}>{tier.sub}</div>
          </div>
        ))}
      </div>

      {/* Bluetooth upsell nudge */}
      {freeBT && (
        <div style={{ background: `linear-gradient(135deg,${C.blue}18,${C.cyan}10)`, border: `1px solid ${C.blue}44`, borderRadius: 10, padding: "10px 14px", marginBottom: 12, display: "flex", alignItems: "center", gap: 10 }}>
          <span style={{ fontSize: 18 }}>📶</span>
          <div style={{ flex: 1 }}>
            <div style={{ fontSize: 12, fontWeight: 700, color: C.blue + "dd" }}>הסוללה שלך מוכשרת לBluetooth!</div>
            <div style={{ fontSize: 11, color: C.gray }}></div>
          </div>
          <button onClick={() => setBluetooth(true)} style={{
            padding: "6px 12px", borderRadius: 8, border: `1px solid ${C.blue}55`,
            background: C.blue + "18", color: C.blue, fontSize: 11, fontWeight: 700,
            cursor: "pointer", fontFamily: "inherit", flexShrink: 0,
          }}>הוסף</button>
        </div>
      )}

      {/* Sliders */}
      <Card>
        <div style={{ fontSize: 9, color: C.cyan + "77", fontFamily: "monospace", letterSpacing: 3, marginBottom: 14 }}>// BATTERY CONFIG</div>
        <Slider label="מתח"    value={voltage} values={voltOpts} unit="V"  onChange={setVoltage} />
        <Slider label="קיבולת" value={capAh}   values={ahOpts}  unit="Ah" onChange={setCapAh} />
      </Card>

      {/* BMS */}
      <Card>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
          <div>
            <div style={{ fontSize: 9, color: C.green + "77", fontFamily: "monospace", letterSpacing: 2 }}>// BMS · DALY SMART</div>
            <div style={{ fontSize: 12, color: C.grayLt, marginTop: 2 }}>זרם מקסימלי</div>
          </div>
          <div style={{ textAlign: "left" }}>
            <div style={{ color: C.green, fontSize: 26, fontWeight: 900, fontFamily: "monospace", textShadow: `0 0 14px ${C.green}80`, lineHeight: 1 }}>
              {bms}<span style={{ fontSize: 12, color: C.green + "55", marginRight: 2 }}>A</span>
            </div>
            {bms > 100 && <div style={{ fontSize: 9, color: C.amber + "88", fontFamily: "monospace" }}>+10% מחיר</div>}
          </div>
        </div>
        <div style={{ display: "flex", flexWrap: "wrap", gap: 6 }}>
          {bmsOpts.map(a => {
            const prof = BMS_PROFILES[a];
            const isSel = bms === a;
            return (
              <button key={a} onClick={() => setBms(a)} style={{
                padding: "8px 11px", borderRadius: 8, fontSize: 12, fontFamily: "monospace", cursor: "pointer",
                border: isSel ? `1.5px solid ${prof.accentCSS}` : `1.5px solid ${C.cardBord}`,
                background: isSel ? prof.accentCSS + "18" : C.elevated,
                color: isSel ? prof.accentCSS : C.gray,
                fontWeight: isSel ? 800 : 400, transition: "all .15s", minWidth: 50, textAlign: "center",
                boxShadow: isSel ? `0 0 10px ${prof.accentCSS}33` : "none",
              }}>{a}A</button>
            );
          })}
        </div>

        {/* Bluetooth toggle */}
        <div style={{ marginTop: 14, borderTop: `1px solid ${C.cardBord}`, paddingTop: 14 }}>
          <button onClick={() => setBluetooth(b => !b)} style={{
            width: "100%", padding: "12px 16px", borderRadius: 10, cursor: "pointer", fontFamily: "inherit",
            border: bluetooth ? `1.5px solid ${C.blue}` : `1.5px solid ${C.cardBord}`,
            background: bluetooth ? C.blue + "12" : C.elevated,
            display: "flex", justifyContent: "space-between", alignItems: "center", transition: "all .2s",
          }}>
            <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
              <span style={{ fontSize: 16 }}>📶</span>
              <div style={{ textAlign: "right" }}>
                <div style={{ fontSize: 13, fontWeight: 700, color: bluetooth ? C.blue : C.gray }}>תוספת Bluetooth</div>
                <div style={{ fontSize: 10, color: C.gray, marginTop: 1 }}>ניטור מהטלפון · Daly Smart App · iOS & Android</div>
              </div>
            </div>
            <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
              <span style={{ fontSize: 12, color: bluetooth ? C.blue : C.gray, fontFamily: "monospace", fontWeight: 700 }}>+₪{BT_ADDON_PRICE}</span>
              <div style={{ width: 40, height: 22, borderRadius: 11, background: bluetooth ? C.blue : C.cardBord, border: `1px solid ${bluetooth ? C.blue : C.gray}55`, position: "relative", transition: "background .2s" }}>
                <div style={{ position: "absolute", top: 3, left: bluetooth ? 21 : 3, width: 14, height: 14, borderRadius: "50%", background: bluetooth ? "#fff" : C.gray, transition: "left .2s" }} />
              </div>
            </div>
          </button>
        </div>
      </Card>

      {/* Shape selector */}
      <Card>
        <div style={{ fontSize: 9, color: C.cyan + "77", fontFamily: "monospace", letterSpacing: 3, marginBottom: 18 }}>// PACK GEOMETRY</div>

        {/* ── Columns slider (same style as voltage/capacity) ── */}
        {(() => {
          const colOpts = Array.from({ length: groupsPerFloor }, (_, i) => i + 1);
          const colIdx  = colOpts.indexOf(effectiveCols);
          const pct     = (colIdx / (colOpts.length - 1)) * 100;
          const pL      = Math.round((effectiveCols * 2.3 + 2.0) * 10) / 10;
          const pW      = Math.round((gridRows * P * 2.3 + 2.0) * 10) / 10;
          return (
            <div style={{ marginBottom: 22 }}>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "baseline", marginBottom: 8 }}>
                <span style={{ color: C.grayLt, fontSize: 12, fontFamily: "monospace", letterSpacing: 1 }}>אורך</span>
                <div style={{ textAlign: "left" }}>
                  <span style={{ color: C.cyan, fontSize: 26, fontWeight: 900, fontFamily: "monospace", textShadow: `0 0 16px ${C.cyan}80`, lineHeight: 1 }}>
                    {effectiveCols}<span style={{ fontSize: 12, color: C.cyan + "55", marginRight: 2 }}> עמודות</span>
                  </span>
                  <span style={{ fontSize: 11, color: C.amber, fontFamily: "monospace", marginRight: 8 }}>= {pL} ס"מ</span>
                </div>
              </div>
              <div style={{ direction: "ltr", position: "relative", height: 36, display: "flex", alignItems: "center" }}>
                <div style={{ position: "absolute", left: 0, right: 0, height: 4, borderRadius: 2, background: C.cardBord }} />
                <div style={{ position: "absolute", left: 0, width: `${pct}%`, height: 4, borderRadius: 2, background: `linear-gradient(90deg, ${C.cyan}, ${C.green})` }} />
                <input type="range" min={0} max={colOpts.length - 1} step={1} value={colIdx}
                  onChange={e => setPackCols(colOpts[+e.target.value])}
                  style={{ width: "100%", position: "relative", zIndex: 2, WebkitAppearance: "none", appearance: "none", background: "transparent", cursor: "pointer", height: 36 }} />
              </div>
              <div style={{ direction: "ltr", display: "flex", justifyContent: "space-between", marginTop: 1 }}>
                <span style={{ color: C.gray, fontSize: 10, fontFamily: "monospace" }}>1 עמודה</span>
                <span style={{ color: C.gray, fontSize: 10, fontFamily: "monospace" }}>{groupsPerFloor} עמודות</span>
              </div>
              {/* Resulting width */}
              <div style={{ marginTop: 8, display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                <span style={{ color: C.grayLt, fontSize: 12, fontFamily: "monospace", letterSpacing: 1 }}>רוחב</span>
                <div>
                  <span style={{ color: C.cyan, fontSize: 18, fontWeight: 900, fontFamily: "monospace" }}>
                    {gridRows * P}<span style={{ fontSize: 11, color: C.cyan + "55", marginRight: 2 }}> תאים</span>
                  </span>
                  <span style={{ fontSize: 11, color: C.amber, fontFamily: "monospace", marginRight: 8 }}>= {pW} ס"מ</span>
                </div>
              </div>
              <div style={{ height: 3, borderRadius: 2, background: C.cardBord, marginTop: 6 }}>
                <div style={{ height: "100%", borderRadius: 2, background: `linear-gradient(90deg,${C.blue},${C.cyan})`, width: `${Math.min(100,(gridRows * P / S) * 100)}%`, transition: "width .2s" }} />
              </div>
            </div>
          );
        })()}

        {/* ── 2 קומות toggle ── */}
        <button onClick={() => setFloors(f => f === 1 ? 2 : 1)} style={{
          width: "100%", padding: "12px 14px", borderRadius: 11, cursor: "pointer", fontFamily: "inherit",
          border: `1.5px solid ${floors === 2 ? C.blue : C.cardBord}`,
          background: floors === 2 ? C.blue + "12" : C.elevated,
          display: "flex", alignItems: "center", justifyContent: "space-between", transition: "all .2s",
          marginBottom: 14,
        }}>
          <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
            <span style={{ fontSize: 20 }}>⬛</span>
            <div style={{ textAlign: "right" }}>
              <div style={{ fontSize: 13, fontWeight: 700, color: floors === 2 ? C.blue : C.gray }}>2 קומות</div>
              <div style={{ fontSize: 10, color: C.gray, marginTop: 1 }}>
                {floors === 2 ? `גובה: ${Math.round((7.2 * 2 + 0.5) * 10) / 10} ס״מ · חצי שטח רצפה` : 'גובה: 7.2 ס״מ · קומה אחת'}
              </div>
            </div>
          </div>
          <div style={{ width: 40, height: 22, borderRadius: 11, background: floors === 2 ? C.blue : C.cardBord, position: "relative", transition: "background .2s", flexShrink: 0 }}>
            <div style={{ position: "absolute", top: 3, left: floors === 2 ? 21 : 3, width: 14, height: 14, borderRadius: "50%", background: floors === 2 ? "#fff" : C.gray, transition: "left .2s" }} />
          </div>
        </button>

        {/* ── Footprint visual ── */}
        {(() => {
          const pL = Math.round((effectiveCols * 2.3 + 2.0) * 10) / 10;
          const pW = Math.round((gridRows * P * 2.3 + 2.0) * 10) / 10;
          const pH = floors === 2 ? Math.round((7.2 * 2 + 0.5) * 10) / 10 : 7.2;
          const mx = Math.max(pL, pW, 1);
          const bW = Math.max(14, Math.round((pL / mx) * 80));
          const bH = Math.max(10, Math.round((pW / mx) * 56));
          return (
            <div style={{ background: "#070b12", borderRadius: 10, padding: "14px 14px 12px", border: `1px solid ${C.cardBord}` }}>
              <div style={{ fontSize: 9, color: C.gray + "88", fontFamily: "monospace", marginBottom: 10, letterSpacing: 2 }}>// FOOTPRINT</div>
              <div style={{ display: "flex", alignItems: "center", gap: 16 }}>
                {/* Shape box */}
                <div style={{ width: 90, height: 70, display: "flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
                  <div style={{
                    width: bW, height: bH,
                    background: `linear-gradient(135deg, ${C.cyan}15, ${C.blue}0d)`,
                    border: `1.5px solid ${C.cyan}55`, borderRadius: 3,
                    transition: "all .3s ease", position: "relative",
                    display: "flex", alignItems: "center", justifyContent: "center",
                  }}>
                    {floors === 2 && (
                      <div style={{ position: "absolute", inset: 4, border: `1px dashed ${C.blue}66`, borderRadius: 2 }} />
                    )}
                    <span style={{ fontSize: 8, color: C.cyan + "77", fontFamily: "monospace" }}>{pL}×{pW}</span>
                  </div>
                </div>
                {/* Numbers */}
                <div style={{ flex: 1 }}>
                  {[["אורך", pL, C.amber], ["רוחב", pW, C.amber], ["גובה", pH, C.green]].map(([lbl, val, clr]) => (
                    <div key={lbl} style={{ display: "flex", justifyContent: "space-between", alignItems: "baseline", marginBottom: 6 }}>
                      <span style={{ fontSize: 11, color: C.gray }}>{lbl}</span>
                      <span style={{ fontSize: 18, fontWeight: 900, color: clr, fontFamily: "monospace", lineHeight: 1 }}>
                        {val}<span style={{ fontSize: 9, color: C.gray, marginRight: 2 }}> ס"מ</span>
                      </span>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          );
        })()}
      </Card>

      {/* 3D / stats */}
      <Card>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 14 }}>
          <div style={{ fontSize: 9, color: C.cyan + "77", fontFamily: "monospace", letterSpacing: 3 }}>// PACK VISUALIZATION</div>
          <div style={{ display: "flex", gap: 6 }}>
            {[{ v: true, l: "3D" }, { v: false, l: "2D" }].map(({ v, l }) => (
              <button key={l} onClick={() => setView3D(v)} style={{
                padding: "5px 14px", borderRadius: 7, fontSize: 11, fontFamily: "monospace", cursor: "pointer",
                border: view3D === v ? `1.5px solid ${C.cyan}` : `1.5px solid ${C.cardBord}`,
                background: view3D === v ? C.cyan + "15" : C.elevated,
                color: view3D === v ? C.cyan : C.gray, fontWeight: view3D === v ? 700 : 400,
              }}>{l}</button>
            ))}
          </div>
        </div>

        {/* Stats strip */}
        <div style={{ display: "flex", gap: 6, marginBottom: 14, flexWrap: "wrap" }}>
          {[
            { l: "תאים",   v: totalCells,     u: "" },
            { l: "S×P",    v: `${S}×${P}`,    u: "" },
            { l: "ולטאז'", v: realV,           u: "V" },
            { l: "קיבולת", v: realAh,          u: "Ah" },
            { l: "kWh",    v: energy,           u: "" },
            { l: "משקל",   v: weight,           u: "kg" },
          ].map(s => (
            <div key={s.l} style={{ flex: "1 1 72px", textAlign: "center", padding: "7px 4px", borderRadius: 9, background: C.elevated, border: `1px solid ${C.cardBord}` }}>
              <div style={{ fontSize: 14, fontWeight: 900, color: C.cyan, fontFamily: "monospace", lineHeight: 1 }}>{s.v}<span style={{ fontSize: 9, color: C.gray }}>{s.u}</span></div>
              <div style={{ fontSize: 9, color: C.gray, marginTop: 2, fontFamily: "monospace" }}>{s.l}</div>
            </div>
          ))}
        </div>

        {/* Dimensions strip */}
        <div style={{ display: "flex", gap: 8, marginBottom: 14 }}>
          {[["אורך", dimL], ["רוחב", dimW], ["גובה", dimH]].map(([lbl, val]) => (
            <div key={lbl} style={{ flex: 1, background: C.elevated, borderRadius: 9, padding: "8px 6px", textAlign: "center", border: `1px solid ${C.cardBord}` }}>
              <div style={{ fontSize: 16, fontWeight: 900, color: C.amber, fontFamily: "monospace", lineHeight: 1 }}>{val}</div>
              <div style={{ fontSize: 8, color: C.gray }}>ס"מ</div>
              <div style={{ fontSize: 9, color: C.grayLt, fontWeight: 700, marginTop: 2 }}>{lbl}</div>
            </div>
          ))}
          <div style={{ flex: 1.4, background: C.elevated, borderRadius: 9, padding: "8px 8px", textAlign: "center", border: `1px solid ${C.cardBord}`, display: "flex", flexDirection: "column", justifyContent: "center" }}>
            <div style={{ fontSize: 12, fontWeight: 900, color: C.cyan, fontFamily: "monospace" }}>{dimL}×{dimW}×{dimH}</div>
            <div style={{ fontSize: 8, color: C.gray, marginTop: 2 }}>L×W×H ס"מ</div>
            <div style={{ fontSize: 8, color: C.gray, marginTop: 1 }}>±5mm</div>
          </div>
        </div>

                {view3D ? <Battery3D S={S} P={P} packCols={effectiveCols} shape={floors === 2 ? "double" : "flat"} floors={floors} bms={bms} /> : (
          <div style={{ display: "flex", justifyContent: "center", padding: "20px 0" }}>
            <div style={{ background: C.elevated, border: `2px solid ${C.cardBord}`, borderRadius: 12, padding: 20, position: "relative" }}>
              <div style={{ position: "absolute", top: -8, left: "50%", transform: "translateX(-50%)", width: 36, height: 8, borderRadius: "4px 4px 0 0", background: C.cyan }} />
              {(() => {
                const total = S * P, maxShow = 160, show = Math.min(total, maxShow);
                // floors from state
                let cols2 = effectiveCols;
                cols2 = Math.min(cols2, 36);
                const rows2 = Math.ceil(show / cols2);
                const cW2 = Math.max(7, Math.min(16, Math.floor(260 / cols2)));
                return (
                  <div style={{ display: "flex", flexDirection: "column", gap: shape === "double" ? 10 : 3 }}>
                    {Array.from({ length: floors }).map((_, fi) => (
                      <div key={fi} style={{ display: "flex", flexDirection: "column", gap: 3, opacity: fi === 1 ? 0.65 : 1 }}>
                        {fi === 1 && <div style={{ fontSize: 9, color: C.gray, fontFamily: "monospace", marginBottom: 2, textAlign: "center" }}>— קומה 2 —</div>}
                        {Array.from({ length: rows2 }).map((_, r) => (
                          <div key={r} style={{ display: "flex", gap: 3 }}>
                            {Array.from({ length: cols2 }).map((_, c) => {
                              const idx = r * cols2 + c;
                              if (idx >= show) return <div key={c} style={{ width: cW2, height: Math.round(cW2 * 1.65) }} />;
                              return <div key={c} style={{ width: cW2, height: Math.round(cW2 * 1.65), borderRadius: 2, background: `linear-gradient(160deg,#f5c842,#c07800)`, border: "1px solid rgba(255,190,0,.3)" }} />;
                            })}
                          </div>
                        ))}
                      </div>
                    ))}
                  </div>
                );
              })()}
            </div>
          </div>
        )}
      </Card>

      {/* Price */}
      <div style={{ background: C.card, borderRadius: 14, padding: "18px 16px", marginBottom: 12, border: `1px solid ${discountPct > 0 ? C.green + "44" : C.cyan + "30"}`, boxShadow: `0 0 30px ${discountPct > 0 ? C.green + "12" : C.cyan + "08"}` }}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
          <div style={{ fontSize: 9, color: C.cyan + "66", fontFamily: "monospace", letterSpacing: 3 }}>// PRICING</div>
          {discountPct > 0 && (
            <div style={{
              padding: "4px 10px", borderRadius: 20, fontSize: 12, fontWeight: 900,
              background: `linear-gradient(135deg, ${C.green}22, ${C.cyan}15)`,
              border: `1.5px solid ${C.green}66`, color: C.green,
              fontFamily: "monospace", letterSpacing: 1,
            }}>
              -{discountPct}% הנחה 🎉
            </div>
          )}
        </div>

        {/* Line items */}
        <div style={{ marginBottom: 10 }}>
          <div style={{ display: "flex", justifyContent: "space-between", fontSize: 12, color: C.gray, marginBottom: 5 }}>
            <span>בסיס {energy}kWh × ₪{PRICE_PER_KWH.toLocaleString()}</span>
            <span style={{ color: C.grayLt }}>₪{Math.round(parseFloat(energy) * PRICE_PER_KWH).toLocaleString()}</span>
          </div>
          {bms > 100 && (
            <div style={{ display: "flex", justifyContent: "space-between", fontSize: 11, color: C.amber + "99", fontFamily: "monospace", marginBottom: 4 }}>
              <span>BMS {bms}A (חזק)</span><span>+10%</span>
            </div>
          )}
          {bluetooth && (
            <div style={{ display: "flex", justifyContent: "space-between", fontSize: 11, color: C.blue + "99", fontFamily: "monospace", marginBottom: 4 }}>
              <span>📶 Bluetooth</span><span>+₪{BT_ADDON_PRICE}</span>
            </div>
          )}
          {discountPct > 0 && (
            <div style={{ display: "flex", justifyContent: "space-between", fontSize: 11, color: C.green, fontFamily: "monospace", marginBottom: 4 }}>
              <span>✂️ הנחת נפח {discountPct}%</span>
              <span>-₪{discountAmt.toLocaleString()}</span>
            </div>
          )}
        </div>

        {/* Total */}
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", borderTop: `1px solid ${C.cardBord}`, paddingTop: 12 }}>
          <div>
            <div style={{ color: C.grayLt, fontSize: 12, fontWeight: 700 }}>סה״כ לתשלום</div>
            {discountPct > 0 && (
              <div style={{ fontSize: 12, color: C.gray, textDecoration: "line-through", marginTop: 2 }}>₪{totalPrice.toLocaleString()}</div>
            )}
          </div>
          <div style={{ textAlign: "left" }}>
            <div style={{ color: C.cyan, fontSize: 40, fontWeight: 900, fontFamily: "monospace", textShadow: `0 0 32px ${C.cyan}66`, lineHeight: 1 }}>
              ₪{finalPrice.toLocaleString()}
            </div>
            {discountPct > 0 && (
              <div style={{ fontSize: 11, color: C.green, fontFamily: "monospace", textAlign: "center", marginTop: 3 }}>
                חסכת ₪{discountAmt.toLocaleString()} ({discountPct}%)
              </div>
            )}
          </div>
        </div>

        {/* Next discount nudge */}
        {kWh < 3.1 && (
          <div style={{ marginTop: 10, background: C.elevated, borderRadius: 8, padding: "8px 12px", border: `1px solid ${C.cardBord}`, fontSize: 11, color: C.gray, display: "flex", alignItems: "center", gap: 6 }}>
            <span>💡</span>
            <span>
              {(() => {
                const steps = [[0.5,6],[1,12],[1.5,18],[2,24],[2.5,30],[3,35]];
                const next  = steps.find(([t]) => kWh < t);
                if (!next) return null;
                const wh = Math.ceil((next[0] - kWh) * 1000);
                return `עוד ${wh}Wh להנחה של ${next[1]}%`;
              })()}
            </span>
          </div>
        )}
      </div>

      {/* ייעוץ חינם CTA */}
      <div style={{ background: `linear-gradient(135deg, ${C.cyan}0d, ${C.blue}0a)`, border: `1px solid ${C.cyan}33`, borderRadius: 14, padding: "14px 16px", marginBottom: 12, display: "flex", alignItems: "center", gap: 14 }}>
        <div style={{ fontSize: 30, flexShrink: 0 }}>🎯</div>
        <div style={{ flex: 1 }}>
          <div style={{ fontSize: 14, fontWeight: 800, color: C.white }}>לא בטוח מה מתאים לך?</div>
          <div style={{ fontSize: 12, color: C.gray, marginTop: 3 }}>השאר פרטים ונבנה לך מפרט מדויק — חינם, ללא התחייבות.</div>
        </div>
        <button onClick={onOpenLead} style={{
          padding: "10px 16px", borderRadius: 10, border: "none", cursor: "pointer",
          background: `linear-gradient(135deg, ${C.cyan}, ${C.blue})`,
          color: C.bg, fontSize: 12, fontWeight: 900, fontFamily: "inherit", flexShrink: 0,
          boxShadow: `0 0 16px ${C.cyan}44`,
        }}>
          ייעוץ<br/>חינם
        </button>
      </div>

      {/* WhatsApp Order */}
      <a href={waLink} target="_blank" rel="noopener noreferrer" style={{ textDecoration: "none", display: "block", marginBottom: 10 }}>
        <button style={{
          width: "100%", padding: "16px", borderRadius: 14, border: "none",
          background: "linear-gradient(135deg, #25d366 0%, #128c7e 100%)",
          color: "#fff", fontSize: 16, fontWeight: 900, cursor: "pointer", fontFamily: "inherit",
          boxShadow: "0 0 28px rgba(37,211,102,.35)", display: "flex", alignItems: "center", justifyContent: "center", gap: 10,
          letterSpacing: 0.3,
        }}>
          <span style={{ fontSize: 20 }}>💬</span>
          <span>שלח הזמנה עכשיו{discountPct > 0 ? ` — חסוך ₪${discountAmt}` : ""}</span>
        </button>
      </a>

      <a href="tel:0549252094" style={{ textDecoration: "none", display: "block" }}>
        <button style={{
          width: "100%", padding: "14px", borderRadius: 14, cursor: "pointer", fontFamily: "inherit",
          border: `1.5px solid ${C.green}55`, background: C.green + "0d",
          color: C.green, fontSize: 14, fontWeight: 700,
          display: "flex", alignItems: "center", justifyContent: "center", gap: 9,
        }}>📞 התקשר — 054-9252094</button>
      </a>

      <div style={{ marginTop: 16, display: "flex", justifyContent: "center", gap: 16, fontSize: 10, color: C.gray, fontFamily: "monospace" }}>
        {["✔ אחריות 12 חודש", "✔ Grade A EVE", "✔ 14 ימ\"ע", "✔ משלוח חינם מ-1kWh"].map(t => <span key={t}>{t}</span>)}
      </div>
    </div>
  );
}

// ── InfoTab ───────────────────────────────────────────────────────────────────
function InfoTab() {
  const [openFaq, setOpenFaq] = useState(null);
  return (
    <div style={{ maxWidth: 640, margin: "0 auto", padding: "16px 14px 40px" }}>
      <div style={{ marginBottom: 20 }}>
        <div style={{ fontSize: 20, fontWeight: 900, color: C.white, lineHeight: 1.1 }}>מידע טכני</div>
        <div style={{ fontSize: 11, color: C.gray, fontFamily: "monospace", marginTop: 4 }}>// EVE · DALY · LITHIUM NMC</div>
      </div>

      {/* ⚠️ חשוב לפני רכישה */}
      <div style={{ background: "#140a00", border: `1px solid ${C.amber}44`, borderRadius: 14, padding: "16px", marginBottom: 14 }}>
        <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 14 }}>
          <span style={{ fontSize: 18 }}>⚠️</span>
          <div style={{ fontSize: 14, fontWeight: 700, color: C.amber }}>חשוב לדעת לפני שרוכשים</div>
        </div>
        {[
          ["🔌 בדוק תאימות לציוד שלך", "לפני ההזמנה ודא שמתח הסוללה (V) ועוצמת ה-BMS (A) מתאימים לבקר/מנוע שלך. חוסר התאמה עלול לגרום לנזק. אנחנו כאן לעזור — שאל אותנו."],
          ["⚡ השתמש במטען מתאים", "חובה להשתמש במטען (Charger) שמתוכנן לסוללת ליתיום בדיוק אותו מתח. מטען לא מתאים = סכנת שריפה. לדוגמה: סוללת 48V דורשת מטען שמוציא בדיוק 54.6V."],
          ["🔧 אל תפרק ואל תניקב", "פירוק הסוללה או פגיעה פיזית בתאים עלולים לגרום לשחרור גזים ושריפה. אין להנחות מכאנית על התאים."],
          ["📊 ה-BMS לא מוגן מפני הכל", "ה-BMS מגן מפני קצר, עומס יתר וחיבור הפוך — אך לא מפני נזק מכאני, חדירת מים, או שימוש במטען לא נכון."],
          ["🚫 אל תשאיר ריקה לאורך זמן", "סוללת ליתיום שלא בשימוש צריכה לשמור ~50% טעינה. שמירה ריקה לחודשים גורמת לנזק קבוע לתאים."],
        ].map(([title, text], i, arr) => (
          <div key={i} style={{ padding: "10px 0", borderBottom: i < arr.length-1 ? `1px solid ${C.cardBord}` : "none" }}>
            <div style={{ fontSize: 12, fontWeight: 700, color: C.amber + "cc", marginBottom: 3 }}>{title}</div>
            <div style={{ fontSize: 12, color: C.gray, lineHeight: 1.7 }}>{text}</div>
          </div>
        ))}
      </div>

      {/* ✅ אישור חשמלאי מוסמך — badge */}
      <div style={{ background: "#031410", border: `1px solid ${C.green}44`, borderRadius: 12, padding: "12px 16px", marginBottom: 14, display: "flex", alignItems: "center", gap: 12 }}>
        <div style={{ width: 36, height: 36, borderRadius: 9, background: C.green + "18", border: `1px solid ${C.green}44`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 20, flexShrink: 0 }}>🏅</div>
        <div>
          <div style={{ fontSize: 13, fontWeight: 700, color: C.green }}>מעבדה מאושרת על ידי חשמלאי מוסמך</div>
          <div style={{ fontSize: 11, color: C.gray, marginTop: 3 }}>כל תהליך הייצור והבדיקות עבר אישור מקצועי בהתאם לתקן הישראלי.</div>
        </div>
      </div>

      {/* EVE Cell Specs */}
      <Card>
        <div style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 14 }}>
          <div style={{ display: "flex", gap: 3 }}>
            {[0,1,2].map(i => <div key={i} style={{ width: 16, height: 28, borderRadius: 3, background: "linear-gradient(160deg,#f5c842,#c07800)", boxShadow: "0 0 6px rgba(245,200,66,.3)" }}><div style={{ width: 6, height: 3, background: "rgba(255,255,255,.45)", borderRadius: 1, margin: "2px auto 0" }} /></div>)}
          </div>
          <div>
            <div style={{ fontSize: 14, fontWeight: 700, color: C.amber }}>EVE 21700-50E · Grade A</div>
            <div style={{ fontSize: 10, color: C.gray, fontFamily: "monospace" }}>EVE Energy Co., Ltd · שנז׳ן, סין · SZSE:300014</div>
          </div>
        </div>
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 6 }}>
          {[
            ["ממדים",             "Ø21mm × 70mm"],
            ["קיבולת",            "5,000mAh"],
            ["מתח נומינלי",       "3.6V"],
            ["מתח מקסימלי",       "4.2V (גמר טעינה)"],
            ["מתח מינימלי",       "2.5V (גמר פריקה)"],
            ["פריקה רציפה",       "10A"],
            ["פריקת שיא",         "15A (10 שניות)"],
            ["טמפ׳ טעינה",        "0°C – 45°C"],
            ["טמפ׳ פריקה",        "-20°C – 60°C"],
            ["מחזורי חיים",       ">500 @ 80% קיבולת"],
            ["כימיה",             "NMC ליתיום"],
            ["משקל",              "~70g לתא"],
          ].map(([k, v]) => (
            <div key={k} style={{ background: C.elevated, borderRadius: 8, padding: "8px 10px", border: `1px solid ${C.cardBord}` }}>
              <div style={{ fontSize: 9, color: C.gray, fontFamily: "monospace", marginBottom: 2 }}>{k}</div>
              <div style={{ fontSize: 12, color: C.white, fontWeight: 600 }}>{v}</div>
            </div>
          ))}
        </div>
      </Card>

      {/* BMS - Daly */}
      <Card>
        <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 14 }}>
          <div style={{ width: 38, height: 38, borderRadius: 10, background: C.green + "15", border: `1px solid ${C.green}33`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 20, flexShrink: 0 }}>🛡️</div>
          <div>
            <div style={{ fontSize: 14, fontWeight: 700, color: C.white }}>Daly Smart BMS</div>
            <div style={{ fontSize: 10, color: C.gray, fontFamily: "monospace" }}>BMS אמין ונפוץ · נתמך ב-iOS ו-Android</div>
          </div>
        </div>
        {[
          { range: "20–50A",   acc: C.green,   note: "קורקינטים, אופניים קלים, מערכות סולאריות קטנות עד 12V/24V" },
          { range: "60–80A",   acc: C.blue,    note: "אופניים חשמליים, עגלות גולף, מערכות סולאריות ביתיות 48V" },
          { range: "90–100A",  acc: "#ff8800", note: "מנועים חזקים 1–3kW, רכבי שטח חשמליים, חקלאות" },
          { range: "110–120A", acc: C.red,     note: "אופנועים חשמליים, מנועים 3kW+ — מגיע עם גוף קירור" },
        ].map((b, i) => (
          <div key={i} style={{ display: "flex", gap: 10, padding: "10px 0", borderTop: `1px solid ${C.cardBord}` }}>
            <Tag text={b.range} color={b.acc} />
            <div style={{ fontSize: 12, color: C.gray, lineHeight: 1.6 }}>{b.note}</div>
          </div>
        ))}
      </Card>

      {/* FAQ — accordion */}
      <div style={{ fontSize: 13, fontWeight: 700, color: C.grayLt, marginBottom: 12, fontFamily: "monospace" }}>// שאלות ותשובות</div>
      {[
        ["מה ההבדל בין S ל-P?",
         "S (Series / טורי) — תאים בשרשרת, מגדיל מתח. P (Parallel / מקביל) — תאים ברוחב, מגדיל קיבולת.\n\nדוגמה: 13S6P = 78 תאים → 46.8V נומינלי, 30Ah, 1.4kWh.\nהסימון עוזר להבין בדיוק כמה אנרגיה יש בסוללה: kWh = (S×3.6×P×5)/1000."],
        ["איזה BMS לבחור לשימוש שלי?",
         "כלל אצבע: בחר BMS לפי הזרם המקסימלי שהמנוע/הבקר שלך צורך, עם מרווח ביטחון של 20–30%.\n\n• קורקינט 500W–48V ≈ 10A → BMS 20A מספיק\n• אופנוע 1500W–48V ≈ 31A → BMS 50A\n• אופנוע 3000W–72V ≈ 42A → BMS 60A\n• רכב שטח → BMS 100A+\n\nאם לא בטוח — שאל אותנו, נחשב יחד."],
        ["מה ההבדל בין 21700 ל-18650?",
         "21700 גדול יותר (Ø21×70mm לעומת Ø18×65mm). בנפח גדול יותר ב-60% הוא מכיל ~5Ah לעומת ~3Ah ב-18650.\n\nתוצאה: סוללת 21700 קטנה יותר באותה אנרגיה, ומחזיקה יותר מחזורי חיים. כל הסוללות שלנו מתאי EVE 21700."],
        ["כמה מחזורי חיים יש לסוללה?",
         "תאי EVE Grade A מדורגים ל-500+ מחזורים @ 80% קיבולת נשארת.\n\nבפועל, עם שימוש עדין (לא פריקה עד 0%, לא טעינה קיצונית בחום), לקוחות רבים מדווחים על 800–1200 מחזורים.\n\nטיפ: לטווח חיים ארוך — טען עד 90% בלבד ואל תפרוק מתחת ל-20%."],
        ["האם אפשר להרחיב קיבולת בעתיד?",
         "כן — ניתן לחבר סוללה נוספת מאותה קונפיגורציה (אותו מתח, אותו יצרן, גיל דומה) במקביל.\n\n⚠️ חשוב: אסור לחבר סוללות עם מתחים שונים, יצרנים שונים, או הפרש גיל גדול. זה עלול לגרום לנזק."],
        ["מה מאפשר ה-Bluetooth BMS?",
         "אפליקציית Daly Smart BMS (iOS + Android) מציגה בזמן אמת:\n• מתח של כל תא בנפרד (איתור חולה מיידי)\n• טמפרטורה\n• SOC% (אחוז טעינה מוערך)\n• זרם פריקה/טעינה\n• מצב הגנות\n• אפשרות להגדיר ערכי הגנה וסף ניתוק\n\nמומלץ מאוד לכל שימוש ברכב חשמלי."],
        ["מה מוגן ע\"י ה-BMS?",
         "✅ OVP — מתח עודף בטעינה (overcharge)\n✅ UVP — ריקון יתר (over-discharge)\n✅ OCP — זרם יתר בפריקה\n✅ SCP — קצר חשמלי (פורמל תוך מילישניות)\n✅ OTP — טמפרטורה גבוהה מדי\n✅ חיבור הפוך\n\n⚠️ לא מוגן: נזק מכאני לתאים, חדירת מים, שימוש במטען לא מתאים."],
        ["Common Port זה מה?",
         "BMS עם Common Port (נפוץ) — כניסת טעינה ויציאת פריקה דרך אותם שני חוטים (B+ ו-B-).\n\nזה הסוג שאנו מציעים — פשוט לחיבור, אחד פחות מחבר, ומתאים ל-99% מהשימושים.\n\nקיים גם Split Port (נדיר) — לשימוש מקצועי שבו המטען והצרכן מחוברים בנפרד."],
        ["האם הסוללה עוברת בדיקת איכות?",
         "כן. כל סוללה שלנו עוברת:\n• בדיקת מתח כולל לפני ואחרי אינטגרציה\n• בדיקת BMS — כל הגנה נבדקת ידנית\n• בדיקת איזון (balance) בין התאים לאחר הרכבה\n• תיעוד בצילום לפני שילוח\n\nאנחנו מספקים גיליון מפרט לכל סוללה — שימושי גם לחשמלאי מוסמך."],
      ].map(([q, a], i) => (
        <div key={i} style={{ background: C.card, borderRadius: 12, marginBottom: 8, border: `1px solid ${openFaq === i ? C.cyan + "55" : C.cardBord}`, overflow: "hidden", transition: "border-color .2s" }}>
          <button onClick={() => setOpenFaq(openFaq === i ? null : i)} style={{
            width: "100%", padding: "13px 16px", background: "transparent", border: "none", cursor: "pointer",
            display: "flex", alignItems: "center", justifyContent: "space-between", gap: 8,
          }}>
            <div style={{ fontSize: 12, fontWeight: 700, color: openFaq === i ? C.cyan : C.white, textAlign: "right", flex: 1 }}>{q}</div>
            <span style={{ color: C.cyan, fontSize: 14, flexShrink: 0, transform: openFaq === i ? "rotate(45deg)" : "none", transition: "transform .2s" }}>+</span>
          </button>
          {openFaq === i && (
            <div style={{ padding: "0 16px 14px", fontSize: 12, color: C.gray, lineHeight: 1.85, whiteSpace: "pre-line", borderTop: `1px solid ${C.cardBord}` }}>
              {a}
            </div>
          )}
        </div>
      ))}

      <a href="tel:0549252094" style={{ textDecoration: "none", display: "block", marginTop: 16 }}>
        <button style={{ width: "100%", padding: "14px", borderRadius: 14, cursor: "pointer", fontFamily: "inherit", border: `1.5px solid ${C.green}55`, background: C.green + "0d", color: C.green, fontSize: 14, fontWeight: 700, display: "flex", alignItems: "center", justifyContent: "center", gap: 9 }}>
          📞 שאלות? התקשר — 054-9252094
        </button>
      </a>
    </div>
  );
}

// ── ReviewsTab ────────────────────────────────────────────────────────────────
function ReviewsTab() {
  const avg     = (REVIEWS.reduce((s, r) => s + r.stars, 0) / REVIEWS.length).toFixed(1);
  const maxStar = 5;

  // avatar colour per initial
  const avatarColor = name => {
    const colours = [C.cyan, C.green, C.blue, "#ff8800", "#cc44ff", "#ff4477"];
    return colours[name.charCodeAt(0) % colours.length];
  };

  return (
    <div style={{ maxWidth: 640, margin: "0 auto", padding: "16px 14px 40px" }}>
      <div style={{ marginBottom: 20 }}>
        <div style={{ fontSize: 20, fontWeight: 900, color: C.white }}>מה אומרים הלקוחות</div>
        <div style={{ fontSize: 11, color: C.gray, fontFamily: "monospace", marginTop: 4 }}>// {REVIEWS.length} ביקורות אמיתיות</div>
      </div>

      {/* Rating summary card */}
      <Card style={{ marginBottom: 14 }}>
        <div style={{ display: "flex", alignItems: "center", gap: 20 }}>
          {/* Big number */}
          <div style={{ textAlign: "center", flexShrink: 0 }}>
            <div style={{ fontSize: 52, fontWeight: 900, color: C.amber, fontFamily: "monospace", lineHeight: 1 }}>{avg}</div>
            <Stars n={Math.round(parseFloat(avg))} />
            <div style={{ fontSize: 10, color: C.gray, marginTop: 5 }}>{REVIEWS.length} ביקורות</div>
          </div>

          {/* Bars */}
          <div style={{ flex: 1 }}>
            {[5, 4, 3, 2, 1].map(s => {
              const count = REVIEWS.filter(r => r.stars === s).length;
              const pct   = Math.round((count / REVIEWS.length) * 100);
              return (
                <div key={s} style={{ display: "flex", alignItems: "center", gap: 7, marginBottom: 5 }}>
                  <span style={{ fontSize: 10, color: C.amber, width: 8, flexShrink: 0 }}>{s}</span>
                  <span style={{ fontSize: 9, color: C.amber + "77" }}>★</span>
                  <div style={{ flex: 1, height: 6, borderRadius: 3, background: C.cardBord, overflow: "hidden" }}>
                    <div style={{
                      width: `${pct}%`, height: "100%", borderRadius: 3,
                      background: s >= 4 ? `linear-gradient(90deg,${C.amber},${C.amber}99)` : s === 3 ? "#ff8800" : C.red,
                      transition: "width 0.6s ease",
                    }} />
                  </div>
                  <span style={{ fontSize: 10, color: C.gray, width: 18, textAlign: "left" }}>{count}</span>
                </div>
              );
            })}
          </div>
        </div>

        {/* Trust stats */}
        <div style={{ display: "flex", gap: 8, marginTop: 14, paddingTop: 12, borderTop: `1px solid ${C.cardBord}` }}>
          {[
            [REVIEWS.filter(r => r.verified).length + "+", "ביקורות מאומתות"],
            ["12 ח׳", "אחריות מלאה"],
            ["EVE", "Grade A בלבד"],
          ].map(([val, lbl]) => (
            <div key={lbl} style={{ flex: 1, background: C.elevated, borderRadius: 10, padding: "9px 8px", textAlign: "center", border: `1px solid ${C.cardBord}` }}>
              <div style={{ fontSize: 15, fontWeight: 800, color: C.cyan }}>{val}</div>
              <div style={{ fontSize: 9, color: C.gray, marginTop: 2 }}>{lbl}</div>
            </div>
          ))}
        </div>
      </Card>

      {/* Gallery placeholders with use-case tags */}
      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 18 }}>
        {[
          { label: "48V 60Ah", sub: "אופנוע חשמלי · 624Wh" },
          { label: "72V 100Ah", sub: "עגלת גולף · 7.2kWh" },
          { label: "24V 50Ah", sub: "סולארי ביתי · 1.08kWh" },
          { label: "36V 80Ah", sub: "קורקינט ספורט · 2.88kWh" },
        ].map((tile, i) => (
          <div key={i} style={{ background: C.card, border: `1px solid ${C.cardBord}`, borderRadius: 12, height: 100, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", gap: 6, position: "relative", overflow: "hidden" }}>
            {/* Grid lines BG */}
            <div style={{ position: "absolute", inset: 0, backgroundImage: `repeating-linear-gradient(90deg,${C.cyan}06 0,transparent 1px,transparent 18px),repeating-linear-gradient(${C.cyan}06 0,transparent 1px,transparent 18px)`, pointerEvents: "none" }} />
            <div style={{ display: "flex", gap: 3, opacity: 0.45, zIndex: 1 }}>
              {[0,1,2,3,4].map(j => <div key={j} style={{ width: 8, height: 14, borderRadius: 2, background: `linear-gradient(160deg,${C.cyan},${C.blue})` }} />)}
            </div>
            <div style={{ fontSize: 12, color: C.white, fontWeight: 700, zIndex: 1 }}>{tile.label}</div>
            <Tag text={tile.sub} color={C.cyan} />
          </div>
        ))}
      </div>

      {/* Review cards */}
      {REVIEWS.map((r, i) => {
        const ac = avatarColor(r.name);
        return (
          <Card key={i} style={{ marginBottom: 10 }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 8 }}>
              <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                {/* Avatar */}
                <div style={{
                  width: 36, height: 36, borderRadius: "50%", flexShrink: 0,
                  background: ac + "18", border: `1.5px solid ${ac}44`,
                  display: "flex", alignItems: "center", justifyContent: "center",
                  fontSize: 14, color: ac, fontWeight: 800,
                }}>
                  {r.name[0]}
                </div>
                <div>
                  <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
                    <span style={{ fontSize: 13, fontWeight: 700, color: C.white }}>{r.name}</span>
                    {r.city && <span style={{ fontSize: 10, color: C.gray }}>· {r.city}</span>}
                    {r.verified && (
                      <span style={{ fontSize: 9, color: C.green, background: C.green + "15", border: `1px solid ${C.green}33`, borderRadius: 6, padding: "1px 6px", fontFamily: "monospace" }}>✓ מאומת</span>
                    )}
                  </div>
                  <Stars n={r.stars} />
                </div>
              </div>
              <div style={{ textAlign: "left", flexShrink: 0 }}>
                <div style={{ fontSize: 10, color: C.gray, fontFamily: "monospace", marginBottom: 4 }}>{r.date}</div>
                <Tag text={r.use} color={C.green} />
              </div>
            </div>
            <div style={{ fontSize: 12, color: C.grayLt, lineHeight: 1.8, borderTop: `1px solid ${C.cardBord}`, paddingTop: 10 }}>{r.text}</div>
          </Card>
        );
      })}

      {/* CTA */}
      <div style={{ background: C.card, border: `1px solid ${C.cyan}33`, borderRadius: 14, padding: 16, marginTop: 8, textAlign: "center" }}>
        <div style={{ fontSize: 13, fontWeight: 700, color: C.white, marginBottom: 6 }}>מרוצה מהמוצר? כתוב לנו ביקורת</div>
        <div style={{ fontSize: 11, color: C.gray, marginBottom: 12 }}>נשמח לשמוע — ותקבל הנחה בהזמנה הבאה</div>
        <a href={`https://wa.me/972549252094?text=${encodeURIComponent("שלום, רוצה לשלוח ביקורת על הסוללה שלי 🔋")}`} target="_blank" rel="noopener noreferrer" style={{ textDecoration: "none" }}>
          <button style={{ padding: "11px 28px", borderRadius: 12, cursor: "pointer", fontFamily: "inherit", border: `1.5px solid ${C.green}55`, background: C.green + "18", color: C.green, fontSize: 13, fontWeight: 700 }}>
            💬 שלח ביקורת ב-WhatsApp
          </button>
        </a>
      </div>

      <a href="tel:0549252094" style={{ textDecoration: "none", display: "block", marginTop: 10 }}>
        <button style={{ width: "100%", padding: "14px", borderRadius: 14, cursor: "pointer", fontFamily: "inherit", border: `1.5px solid ${C.green}55`, background: C.green + "0d", color: C.green, fontSize: 14, fontWeight: 700, display: "flex", alignItems: "center", justifyContent: "center", gap: 9 }}>
          📞 צור קשר — 054-9252094
        </button>
      </a>
    </div>
  );
}

// ── AI Chat Tab ───────────────────────────────────────────────────────────────
const SYSTEM_PROMPT = `אתה יועץ מומחה של Y.R Battery — חברה ישראלית המייצרת סוללות ליתיום מותאמות אישית מתאי EVE 21700 Grade A.

המטרה שלך: לענות על שאלות הלקוח בצורה מקצועית, חמה, ומשכנעת — ולהוביל אותו להזמין סוללה.

עובדות על המוצר:
- תאים: EVE 21700-50E Grade A — 5Ah לתא, 3.6V נומינלי, עד 10A רציף לתא
- BMS: Daly Smart BMS — 20A עד 120A, אופציה ל-Bluetooth
- מחיר: ~₪3,200 לkWh לפני הנחה. הנחות נפח: 6% מ-0.5kWh, 12% מ-1kWh, 18% מ-1.5kWh, 24% מ-2kWh, 35% מ-3kWh+
- אחריות: 12 חודש מלאה
- משלוח: עד 14 ימי עסקים
- מתחים: 12V, 24V, 36V, 48V, 60V, 72V
- קיבולות: 5Ah–100Ah בצעדים של 5Ah
- שימושים: אופניים חשמליים, קורקינטים, אופנועים, עגלות גולף, סולארי ביתי, חקלאות, תעשייה

כללי התנהגות:
- ענה תמיד בעברית
- היה ידידותי, מקצועי, ותמציתי (3–5 משפטים בדרך כלל)
- אם שואלים על מחיר — חשב ותן הצעה ספציפית
- בסוף כל תשובה, אם רלוונטי, הוסף הצעה לפעולה (לדוגמה: "רוצה שנבנה לך הצעת מחיר? לחץ על הכפתור למעלה")
- אל תציין שאתה AI — אתה מערכת הייעוץ של Y.R Battery
- אם לא יודע — אמור שתועיל לפנות בטלפון: 054-9252094`;

const QUICK_Q = [
  "כמה עולה סוללה 48V 30Ah?",
  "מה ההבדל בין 18650 ל-21700?",
  "איזה BMS מתאים לאופניים חשמליים?",
  "כמה זמן אחזקה הסוללה?",
  "מה זה Grade A ולמה זה חשוב?",
  "יש אחריות?",
];

function ChatTab() {
  const [messages, setMessages]   = useState([
    { role: "assistant", content: "שלום! אני מערכת הייעוץ של Y.R Battery 🔋\nאשמח לעזור לך לבחור את הסוללה המושלמת, לחשב מחיר, או לענות על כל שאלה טכנית. במה אוכל לסייע?" }
  ]);
  const [input, setInput]         = useState("");
  const [loading, setLoading]     = useState(false);
  const messagesEndRef             = useRef(null);
  const inputRef                   = useRef(null);

  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  }, [messages, loading]);

  const sendMessage = async (text) => {
    const userText = (text || input).trim();
    if (!userText || loading) return;
    setInput("");
    const newMessages = [...messages, { role: "user", content: userText }];
    setMessages(newMessages);
    setLoading(true);

    try {
      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          system: SYSTEM_PROMPT,
          messages: newMessages.map(m => ({ role: m.role, content: m.content })),
        }),
      });
      const data = await res.json();
      const reply = data?.content?.[0]?.text || "מצטער, לא הצלחתי לענות. נסה שוב.";
      setMessages(prev => [...prev, { role: "assistant", content: reply }]);
    } catch {
      setMessages(prev => [...prev, { role: "assistant", content: "שגיאת חיבור. נסה שוב או התקשר: 054-9252094" }]);
    } finally {
      setLoading(false);
      inputRef.current?.focus();
    }
  };

  const handleKey = e => { if (e.key === "Enter" && !e.shiftKey) { e.preventDefault(); sendMessage(); } };

  return (
    <div style={{ maxWidth: 640, margin: "0 auto", display: "flex", flexDirection: "column", height: "calc(100dvh - 120px)" }}>

      {/* Header strip */}
      <div style={{ padding: "14px 16px 10px", borderBottom: `1px solid ${C.cardBord}`, flexShrink: 0 }}>
        <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
          <div style={{ width: 38, height: 38, borderRadius: 10, background: `linear-gradient(135deg, ${C.cyan}22, ${C.blue}22)`, border: `1px solid ${C.cyan}44`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 18 }}>🤖</div>
          <div>
            <div style={{ fontSize: 14, fontWeight: 800, color: C.white }}>יועץ סוללות AI</div>
            <div style={{ fontSize: 10, color: C.green, fontFamily: "monospace", display: "flex", alignItems: "center", gap: 4 }}>
              <span style={{ width: 6, height: 6, borderRadius: "50%", background: C.green, display: "inline-block", boxShadow: `0 0 6px ${C.green}` }} />
              פעיל · מענה מיידי
            </div>
          </div>
        </div>
      </div>

      {/* Messages */}
      <div style={{ flex: 1, overflowY: "auto", padding: "14px 14px 6px", display: "flex", flexDirection: "column", gap: 10 }}>

        {/* Quick questions — show at top */}
        {messages.length <= 1 && (
          <div style={{ marginBottom: 8 }}>
            <div style={{ fontSize: 10, color: C.gray, fontFamily: "monospace", marginBottom: 8, letterSpacing: 1 }}>// שאלות נפוצות</div>
            <div style={{ display: "flex", flexWrap: "wrap", gap: 6 }}>
              {QUICK_Q.map((q, i) => (
                <button key={i} onClick={() => sendMessage(q)} style={{
                  padding: "7px 12px", borderRadius: 20, fontSize: 11, cursor: "pointer", fontFamily: "inherit",
                  border: `1px solid ${C.cyan}33`, background: C.cyan + "0d", color: C.cyan,
                  transition: "all .15s",
                }}>{q}</button>
              ))}
            </div>
          </div>
        )}

        {messages.map((m, i) => {
          const isAI = m.role === "assistant";
          return (
            <div key={i} style={{ display: "flex", gap: 8, justifyContent: isAI ? "flex-start" : "flex-end", alignItems: "flex-end" }}>
              {isAI && (
                <div style={{ width: 28, height: 28, borderRadius: 8, background: `linear-gradient(135deg,${C.cyan},${C.blue})`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 13, flexShrink: 0, marginBottom: 2 }}>⚡</div>
              )}
              <div style={{
                maxWidth: "80%", padding: "10px 14px", borderRadius: isAI ? "4px 14px 14px 14px" : "14px 4px 14px 14px",
                background: isAI ? C.card : `linear-gradient(135deg, ${C.cyan}18, ${C.blue}18)`,
                border: `1px solid ${isAI ? C.cardBord : C.cyan + "33"}`,
                fontSize: 13, color: C.white, lineHeight: 1.75,
                whiteSpace: "pre-wrap", wordBreak: "break-word",
                boxShadow: isAI ? "none" : `0 0 16px ${C.cyan}12`,
              }}>
                {m.content}
              </div>
            </div>
          );
        })}

        {loading && (
          <div style={{ display: "flex", gap: 8, alignItems: "flex-end" }}>
            <div style={{ width: 28, height: 28, borderRadius: 8, background: `linear-gradient(135deg,${C.cyan},${C.blue})`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 13, flexShrink: 0 }}>⚡</div>
            <div style={{ padding: "12px 16px", borderRadius: "4px 14px 14px 14px", background: C.card, border: `1px solid ${C.cardBord}`, display: "flex", gap: 5, alignItems: "center" }}>
              {[0,1,2].map(i => (
                <div key={i} style={{ width: 7, height: 7, borderRadius: "50%", background: C.cyan, animation: `pulse 1.2s ease-in-out ${i*0.2}s infinite` }} />
              ))}
            </div>
          </div>
        )}

        <div ref={messagesEndRef} />
      </div>

      {/* Input bar */}
      <div style={{ padding: "10px 14px 14px", borderTop: `1px solid ${C.cardBord}`, flexShrink: 0, background: C.bg }}>
        <div style={{ display: "flex", gap: 8, alignItems: "flex-end" }}>
          <div style={{ flex: 1, background: C.card, borderRadius: 14, border: `1px solid ${C.cardBord}`, display: "flex", alignItems: "flex-end", padding: "4px 4px 4px 14px", transition: "border-color .2s" }}>
            <textarea
              ref={inputRef}
              value={input}
              onChange={e => setInput(e.target.value)}
              onKeyDown={handleKey}
              placeholder="שאל שאלה על סוללות..."
              rows={1}
              style={{
                flex: 1, background: "transparent", border: "none", outline: "none", resize: "none",
                color: C.white, fontSize: 13, fontFamily: "inherit", lineHeight: 1.6,
                maxHeight: 100, overflowY: "auto", paddingTop: 6, paddingBottom: 6,
                direction: "rtl",
              }}
              onInput={e => { e.target.style.height = "auto"; e.target.style.height = Math.min(e.target.scrollHeight, 100) + "px"; }}
            />
          </div>
          <button
            onClick={() => sendMessage()}
            disabled={loading || !input.trim()}
            style={{
              width: 44, height: 44, borderRadius: 12, border: "none", cursor: "pointer",
              background: loading || !input.trim() ? C.cardBord : `linear-gradient(135deg,${C.cyan},${C.blue})`,
              color: C.bg, fontSize: 18, display: "flex", alignItems: "center", justifyContent: "center",
              flexShrink: 0, transition: "all .2s",
              boxShadow: !loading && input.trim() ? `0 0 16px ${C.cyan}55` : "none",
            }}>
            {loading ? "⟳" : "↑"}
          </button>
        </div>
        <div style={{ fontSize: 10, color: C.gray + "55", fontFamily: "monospace", marginTop: 6, textAlign: "center" }}>
          Enter לשליחה · Shift+Enter לשורה חדשה
        </div>
      </div>

      <style>{`@keyframes pulse { 0%,100%{opacity:.3;transform:scale(.8)} 50%{opacity:1;transform:scale(1)} }`}</style>
    </div>
  );
}

// ── Splash Screen ─────────────────────────────────────────────────────────────
function SplashScreen({ onDone }) {
  const [phase, setPhase] = useState(0);
  // phase 0: logo appears, phase 1: tagline, phase 2: bar fills, phase 3: fade out

  useEffect(() => {
    const t1 = setTimeout(() => setPhase(1), 400);
    const t2 = setTimeout(() => setPhase(2), 900);
    const t3 = setTimeout(() => setPhase(3), 2400);
    const t4 = setTimeout(() => onDone(), 3000);
    return () => [t1,t2,t3,t4].forEach(clearTimeout);
  }, []);

  return (
    <div style={{
      position: "fixed", inset: 0, zIndex: 9999,
      background: C.bg,
      display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center",
      opacity: phase === 3 ? 0 : 1,
      transition: phase === 3 ? "opacity 0.6s ease" : "none",
      overflow: "hidden",
    }}>
      {/* Industrial grid BG */}
      <div style={{
        position: "absolute", inset: 0, pointerEvents: "none",
        backgroundImage: `
          repeating-linear-gradient(90deg, ${C.cyan}07 0px, transparent 1px, transparent 44px),
          repeating-linear-gradient(0deg,  ${C.cyan}07 0px, transparent 1px, transparent 44px)
        `,
      }} />

      {/* Radial glow behind logo */}
      <div style={{
        position: "absolute", width: 340, height: 340, borderRadius: "50%",
        background: `radial-gradient(circle, ${C.cyan}18 0%, transparent 70%)`,
        pointerEvents: "none",
        opacity: phase >= 1 ? 1 : 0, transition: "opacity 0.8s ease",
      }} />

      {/* ── LOGO MARK ── */}
      <div style={{
        opacity: phase >= 0 ? 1 : 0,
        transform: phase >= 0 ? "scale(1) translateY(0)" : "scale(0.7) translateY(20px)",
        transition: "all 0.6s cubic-bezier(0.34,1.56,0.64,1)",
        display: "flex", flexDirection: "column", alignItems: "center", gap: 0,
        marginBottom: 28,
      }}>

        {/* Battery icon mark */}
        <div style={{ position: "relative", marginBottom: 16 }}>
          {/* Outer battery body */}
          <div style={{
            width: 88, height: 46, borderRadius: 10,
            border: `3px solid ${C.cyan}`,
            background: `linear-gradient(135deg, ${C.card}, #0d1520)`,
            boxShadow: `0 0 30px ${C.cyan}44, inset 0 0 20px ${C.cyan}08`,
            position: "relative", display: "flex", alignItems: "center", paddingRight: 8,
          }}>
            {/* Battery terminal nub */}
            <div style={{
              position: "absolute", left: -9, top: "50%", transform: "translateY(-50%)",
              width: 7, height: 20, borderRadius: "3px 0 0 3px",
              background: C.cyan, boxShadow: `0 0 10px ${C.cyan}`,
            }} />
            {/* Fill bars */}
            {[0,1,2,3].map(i => (
              <div key={i} style={{
                flex: 1, height: 24, borderRadius: 4, marginRight: i < 3 ? 4 : 0,
                background: phase >= 2
                  ? `linear-gradient(180deg, ${C.cyan}, ${C.blue})`
                  : `${C.cardBord}`,
                transition: `background 0.25s ease ${i * 0.08}s`,
                boxShadow: phase >= 2 ? `0 0 8px ${C.cyan}88` : "none",
              }} />
            ))}
          </div>
          {/* Lightning bolt overlay */}
          <div style={{
            position: "absolute", top: "50%", left: "50%",
            transform: "translate(-50%, -50%)",
            fontSize: 22, filter: `drop-shadow(0 0 8px ${C.cyan})`,
            opacity: phase >= 2 ? 1 : 0, transition: "opacity 0.3s ease 0.5s",
          }}>⚡</div>
        </div>

        {/* Wordmark */}
        <div style={{ textAlign: "center" }}>
          <div style={{
            fontSize: 38, fontWeight: 900, letterSpacing: -1,
            fontFamily: "'Segoe UI', system-ui, sans-serif",
            background: `linear-gradient(135deg, #ffffff 0%, ${C.cyan} 60%, ${C.blue} 100%)`,
            WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent",
            backgroundClip: "text",
            lineHeight: 1,
            filter: `drop-shadow(0 0 20px ${C.cyan}66)`,
          }}>
            Y.R Battery
          </div>
          <div style={{
            fontSize: 10, letterSpacing: 5, color: C.cyan + "88",
            fontFamily: "monospace", marginTop: 6, textTransform: "uppercase",
            opacity: phase >= 1 ? 1 : 0, transition: "opacity 0.5s ease 0.2s",
          }}>
            CUSTOM LITHIUM · 21700
          </div>
        </div>
      </div>

      {/* Loading bar */}
      <div style={{
        width: 200, height: 3, borderRadius: 2,
        background: C.cardBord, overflow: "hidden",
        opacity: phase >= 1 ? 1 : 0, transition: "opacity 0.4s ease",
      }}>
        <div style={{
          height: "100%", borderRadius: 2,
          background: `linear-gradient(90deg, ${C.cyan}, ${C.blue})`,
          width: phase >= 2 ? "100%" : "0%",
          transition: "width 1.4s cubic-bezier(0.4,0,0.2,1)",
          boxShadow: `0 0 10px ${C.cyan}`,
        }} />
      </div>

      <div style={{
        marginTop: 14, fontSize: 10, color: C.gray,
        fontFamily: "monospace", letterSpacing: 2,
        opacity: phase >= 1 ? 1 : 0, transition: "opacity 0.4s ease 0.3s",
      }}>
        {phase < 3 ? "מטען מערכת..." : "מוכן"}
      </div>

      {/* Bottom tagline */}
      <div style={{
        position: "absolute", bottom: 36,
        fontSize: 11, color: C.gray + "77",
        fontFamily: "monospace", letterSpacing: 1,
        opacity: phase >= 2 ? 1 : 0, transition: "opacity 0.5s ease",
        textAlign: "center",
      }}>
        Grade A · Handcrafted · 12 Month Warranty
      </div>

      <style>{`
        @keyframes splashPulse {
          0%, 100% { box-shadow: 0 0 30px ${C.cyan}44; }
          50%       { box-shadow: 0 0 60px ${C.cyan}88; }
        }
      `}</style>
    </div>
  );
}

// ── Lead Modal — ייעוץ חינם ───────────────────────────────────────────────────
const VEHICLE_OPTIONS = [
  "אופניים חשמליים",
  "קורקינט חשמלי",
  "אופנוע חשמלי",
  "עגלת גולף",
  "רכב שטח חשמלי",
  "מערכת סולארית ביתית",
  "חקלאות / ציוד כבד",
  "בנייה עצמית / DIY",
  "אחר",
];

function LeadModal({ onClose }) {
  const [name,    setName]    = useState("");
  const [phone,   setPhone]   = useState("");
  const [vehicle, setVehicle] = useState("");
  const [notes,   setNotes]   = useState("");
  const [sent,    setSent]    = useState(false);

  const handleSubmit = () => {
    if (!name.trim() || !phone.trim() || !vehicle) return;

    const msg =
`🔋 *ליד חדש — בקשת ייעוץ Y.R Battery*

👤 שם: ${name}
📱 טלפון: ${phone}
🛵 כלי: ${vehicle}${notes ? `\n📝 הערות: ${notes}` : ""}

📅 תאריך: ${new Date().toLocaleString("he-IL")}`;

    // WhatsApp to business number
    window.open(`https://wa.me/972549252094?text=${encodeURIComponent(msg)}`, "_blank");

    // Also mailto
    const mailSubject = `ליד חדש — ${name} — ${vehicle}`;
    const mailBody    = msg.replace(/\*/g, "").replace(/\n/g, "%0A");
    window.open(`mailto:yotam142@proton.me?subject=${encodeURIComponent(mailSubject)}&body=${mailBody}`, "_blank");

    setSent(true);
  };

  const canSubmit = name.trim() && phone.trim() && vehicle;

  return (
    <div style={{
      position: "fixed", inset: 0, zIndex: 9000,
      background: "rgba(0,0,0,0.75)", backdropFilter: "blur(6px)",
      display: "flex", alignItems: "flex-end", justifyContent: "center",
    }} onClick={e => { if (e.target === e.currentTarget) onClose(); }}>
      <div style={{
        width: "100%", maxWidth: 540,
        background: C.card, borderRadius: "20px 20px 0 0",
        border: `1px solid ${C.cardBord}`, borderBottom: "none",
        padding: "24px 20px 36px", animation: "slideUp .28s ease",
      }}>
        {/* Handle */}
        <div style={{ width: 40, height: 4, borderRadius: 2, background: C.cardBord, margin: "0 auto 20px" }} />

        {!sent ? (<>
          {/* Title */}
          <div style={{ marginBottom: 20 }}>
            <div style={{ fontSize: 20, fontWeight: 900, color: C.white }}>ייעוץ חינם 🎯</div>
            <div style={{ fontSize: 12, color: C.gray, marginTop: 4 }}>
              השאר פרטים ונחזור אליך עם מפרט מדויק לצרכים שלך.
            </div>
          </div>

          {/* Name */}
          <div style={{ marginBottom: 14 }}>
            <div style={{ fontSize: 11, color: C.grayLt, fontWeight: 700, marginBottom: 6 }}>שם מלא *</div>
            <input
              value={name} onChange={e => setName(e.target.value)}
              placeholder="ישראל ישראלי"
              style={{
                width: "100%", padding: "11px 14px", borderRadius: 10, fontSize: 14,
                background: C.elevated, border: `1px solid ${name ? C.cyan + "55" : C.cardBord}`,
                color: C.white, outline: "none", fontFamily: "inherit", direction: "rtl",
                transition: "border-color .2s",
              }}
            />
          </div>

          {/* Phone */}
          <div style={{ marginBottom: 14 }}>
            <div style={{ fontSize: 11, color: C.grayLt, fontWeight: 700, marginBottom: 6 }}>מספר טלפון *</div>
            <input
              value={phone} onChange={e => setPhone(e.target.value)}
              placeholder="05X-XXXXXXX" type="tel"
              style={{
                width: "100%", padding: "11px 14px", borderRadius: 10, fontSize: 14,
                background: C.elevated, border: `1px solid ${phone ? C.cyan + "55" : C.cardBord}`,
                color: C.white, outline: "none", fontFamily: "monospace", direction: "ltr",
                transition: "border-color .2s",
              }}
            />
          </div>

          {/* Vehicle type */}
          <div style={{ marginBottom: 14 }}>
            <div style={{ fontSize: 11, color: C.grayLt, fontWeight: 700, marginBottom: 8 }}>סוג הכלי / שימוש *</div>
            <div style={{ display: "flex", flexWrap: "wrap", gap: 7 }}>
              {VEHICLE_OPTIONS.map(v => (
                <button key={v} onClick={() => setVehicle(v)} style={{
                  padding: "7px 12px", borderRadius: 20, fontSize: 11, cursor: "pointer",
                  fontFamily: "inherit", border: `1px solid ${vehicle === v ? C.cyan : C.cardBord}`,
                  background: vehicle === v ? C.cyan + "15" : C.elevated,
                  color: vehicle === v ? C.cyan : C.gray, fontWeight: vehicle === v ? 700 : 400,
                  transition: "all .15s",
                }}>{v}</button>
              ))}
            </div>
          </div>

          {/* Notes (optional) */}
          <div style={{ marginBottom: 20 }}>
            <div style={{ fontSize: 11, color: C.grayLt, fontWeight: 700, marginBottom: 6 }}>הערות / פרטים נוספים</div>
            <textarea
              value={notes} onChange={e => setNotes(e.target.value)}
              placeholder="לדוגמה: מנוע 1500W, רוצה 60V, המארז 40×20 ס05d205de..."
              rows={2}
              style={{
                width: "100%", padding: "11px 14px", borderRadius: 10, fontSize: 13,
                background: C.elevated, border: `1px solid ${C.cardBord}`,
                color: C.white, outline: "none", fontFamily: "inherit", direction: "rtl",
                resize: "none", lineHeight: 1.6,
              }}
            />
          </div>

          {/* Submit */}
          <button onClick={handleSubmit} disabled={!canSubmit} style={{
            width: "100%", padding: "15px", borderRadius: 14, border: "none",
            background: canSubmit ? `linear-gradient(135deg,${C.cyan},${C.blue})` : C.cardBord,
            color: canSubmit ? C.bg : C.gray, fontSize: 15, fontWeight: 900,
            cursor: canSubmit ? "pointer" : "not-allowed", fontFamily: "inherit",
            boxShadow: canSubmit ? `0 0 24px ${C.cyan}44` : "none",
            transition: "all .2s",
          }}>
            📨 שלח בקשת ייעוץ
          </button>
          <div style={{ fontSize: 10, color: C.gray + "77", textAlign: "center", marginTop: 8, fontFamily: "monospace" }}>
            הפרטים נשלחים ישירות לוואטסאפ ולמייל
          </div>
        </>) : (
          /* Success state */
          <div style={{ textAlign: "center", padding: "20px 0" }}>
            <div style={{ fontSize: 52, marginBottom: 16 }}>✅</div>
            <div style={{ fontSize: 20, fontWeight: 900, color: C.green, marginBottom: 8 }}>הפרטים נשלחו!</div>
            <div style={{ fontSize: 13, color: C.gray, lineHeight: 1.75, marginBottom: 24 }}>
              נחזור אליך בהקדם לבניית מפרט מותאם אישית.<br/>
              שעות פעילות: א׳–ה׳ 09:00–20:00
            </div>
            <button onClick={onClose} style={{
              padding: "12px 32px", borderRadius: 12, border: `1px solid ${C.green}55`,
              background: C.green + "15", color: C.green, fontSize: 14, fontWeight: 700,
              cursor: "pointer", fontFamily: "inherit",
            }}>סגור</button>
          </div>
        )}
      </div>
      <style>{`@keyframes slideUp { from { transform:translateY(100%); opacity:0; } to { transform:translateY(0); opacity:1; } }`}</style>
    </div>
  );
}

// ── Root ──────────────────────────────────────────────────────────────────────
export default function App() {
  const [tab, setTab]               = useState("builder");
  const [showSplash, setShowSplash] = useState(true);
  const [showLead,   setShowLead]   = useState(false);

  const TABS = [
    { id: "builder", icon: "⚡", label: "בנה סוללה" },
    { id: "info",    icon: "📋", label: "מידע" },
    { id: "chat",    icon: "🤖", label: "יועץ AI" },
    { id: "reviews", icon: "⭐", label: "ביקורות" },
  ];

  return (
    <div dir="rtl" style={{ minHeight: "100dvh", background: C.bg, color: C.white, fontFamily: "'Segoe UI', system-ui, sans-serif", overflowX: "hidden" }}>
      <style>{`
        * { box-sizing: border-box; margin: 0; padding: 0; }
        input[type=range] { -webkit-appearance:none; appearance:none; background:transparent; }
        input[type=range]::-webkit-slider-thumb {
          -webkit-appearance:none; width:22px; height:22px; border-radius:50%;
          background: linear-gradient(135deg,${C.cyan},${C.blue});
          border: 2px solid ${C.bg};
          box-shadow: 0 0 12px ${C.cyan}99; cursor:pointer;
        }
        input[type=range]::-moz-range-thumb { width:22px; height:22px; border-radius:50%; background:${C.cyan}; border:2px solid ${C.bg}; cursor:pointer; }
        button { transition: all .15s; }
        button:active { transform: scale(0.97); opacity: 0.85; }
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-track { background: ${C.bg}; }
        ::-webkit-scrollbar-thumb { background: ${C.cardBord}; border-radius: 2px; }
      `}</style>

      {/* SPLASH */}
      {showSplash && <SplashScreen onDone={() => setShowSplash(false)} />}

      {/* LEAD MODAL */}
      {showLead && <LeadModal onClose={() => setShowLead(false)} />}

      {/* FLOATING "ייעוץ חינם" BUTTON */}
      {!showSplash && (
        <button onClick={() => setShowLead(true)} style={{
          position: "fixed", bottom: 22, left: 16, zIndex: 800,
          padding: "11px 18px", borderRadius: 50, border: "none", cursor: "pointer",
          background: `linear-gradient(135deg, ${C.cyan}, ${C.blue})`,
          color: C.bg, fontSize: 13, fontWeight: 900, fontFamily: "inherit",
          boxShadow: `0 4px 20px ${C.cyan}55, 0 0 0 3px ${C.bg}`,
          display: "flex", alignItems: "center", gap: 7, whiteSpace: "nowrap",
          animation: "floatPulse 3s ease-in-out infinite",
        }}>
          🎯 ייעוץ חינם
        </button>
      )}

      {/* HEADER */}
      <div style={{ background: `${C.card}f8`, backdropFilter: "blur(12px)", borderBottom: `1px solid ${C.cardBord}`, padding: "12px 16px", display: "flex", alignItems: "center", gap: 12, position: "sticky", top: 0, zIndex: 100 }}>
        <div style={{ position: "absolute", inset: 0, backgroundImage: `repeating-linear-gradient(90deg, ${C.cyan}05 0px, transparent 1px, transparent 40px), repeating-linear-gradient(${C.cyan}05 0px, transparent 1px, transparent 40px)`, pointerEvents: "none" }} />

        {/* Logo mark in header */}
        <div style={{ position: "relative", flexShrink: 0, zIndex: 1 }}>
          <div style={{
            width: 36, height: 36, borderRadius: 9, overflow: "hidden",
            border: `1.5px solid ${C.cyan}66`,
            background: `linear-gradient(135deg, #0d1520, #0a0f18)`,
            display: "flex", alignItems: "center", justifyContent: "center",
            boxShadow: `0 0 14px ${C.cyan}44`,
          }}>
            <div style={{ position: "relative", width: 22, height: 12, borderRadius: 3, border: `1.5px solid ${C.cyan}`, background: `linear-gradient(90deg, ${C.cyan}22, ${C.blue}22)` }}>
              <div style={{ position: "absolute", left: -4, top: "50%", transform: "translateY(-50%)", width: 3, height: 7, borderRadius: "1px 0 0 1px", background: C.cyan }} />
              <div style={{ display: "flex", alignItems: "center", height: "100%", padding: "2px", gap: 1.5 }}>
                {[0,1,2].map(i => <div key={i} style={{ flex: 1, height: "100%", borderRadius: 1.5, background: C.cyan, opacity: 0.9 }} />)}
              </div>
            </div>
          </div>
        </div>

        <div style={{ flex: 1, zIndex: 1 }}>
          <div style={{ fontSize: 18, fontWeight: 900, color: C.white, letterSpacing: -0.5 }}>Y.R Battery</div>
          <div style={{ fontSize: 9, color: C.gray, fontFamily: "monospace", letterSpacing: 3 }}>CUSTOM LITHIUM · 21700 · GRADE A</div>
        </div>
        <a href="tel:0549252094" style={{ textDecoration: "none", zIndex: 1 }}>
          <div style={{ padding: "7px 12px", borderRadius: 9, border: `1px solid ${C.green}44`, background: C.green + "0d", color: C.green, fontSize: 11, fontWeight: 700 }}>📞 חייג</div>
        </a>
      </div>

      {/* TAB BAR */}
      <div style={{ background: C.card, borderBottom: `1px solid ${C.cardBord}`, display: "flex", position: "sticky", top: 60, zIndex: 99 }}>
        {TABS.map(t => (
          <button key={t.id} onClick={() => setTab(t.id)} style={{
            flex: 1, padding: "13px 6px", border: "none", background: "transparent",
            color: tab === t.id ? C.cyan : C.gray,
            fontFamily: "inherit", fontSize: 12, fontWeight: tab === t.id ? 700 : 400,
            cursor: "pointer", transition: "color .2s",
            borderBottom: `2px solid ${tab === t.id ? C.cyan : "transparent"}`,
            display: "flex", flexDirection: "column", alignItems: "center", gap: 3,
          }}>
            <span style={{ fontSize: 16 }}>{t.icon}</span>
            <span>{t.label}</span>
          </button>
        ))}
      </div>

      {tab === "builder" && <BuilderTab onOpenLead={() => setShowLead(true)} />}
      {tab === "info"    && <InfoTab />}
      {tab === "chat"    && <ChatTab />}
      {tab === "reviews" && <ReviewsTab />}

      <style>{`
        @keyframes floatPulse {
          0%, 100% { box-shadow: 0 4px 20px ${C.cyan}55, 0 0 0 3px ${C.bg}; }
          50%       { box-shadow: 0 4px 28px ${C.cyan}99, 0 0 0 3px ${C.bg}; transform: translateY(-2px); }
        }
      `}</style>
    </div>
  );
}
