<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Password Generator</title>
    <style>
      :root {
        --accent: #3b82f6;
        --accent-hover: #60a5fa;
        --text: #e5e7eb;
        --muted: #94a3b8;
      }
      * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
        font-family: system-ui, Arial, sans-serif;
      }
      body {
        min-height: 100vh;
        display: grid;
        place-items: center;
        padding: 16px;
        color: var(--text);
        overflow: hidden;
        position: relative;
        background: linear-gradient(135deg, #0b1220, #111c33 55%, #0f2547);
      }
      body::before,
      body::after {
        content: "";
        position: absolute;
        border-radius: 50%;
        filter: blur(90px);
        z-index: 0;
      }
      body::before {
        width: 320px;
        height: 320px;
        background: #2563eb;
        top: 8%;
        left: 12%;
        opacity: 0.4;
      }
      body::after {
        width: 360px;
        height: 360px;
        background: #0ea5e9;
        bottom: 6%;
        right: 10%;
        opacity: 0.3;
      }
      .card {
        position: relative;
        z-index: 1;
        width: 100%;
        max-width: 400px;
        padding: 28px;
        background: rgba(255, 255, 255, 0.07);
        border: 1px solid rgba(255, 255, 255, 0.18);
        border-radius: 20px;
        backdrop-filter: blur(18px) saturate(150%);
        -webkit-backdrop-filter: blur(18px) saturate(150%);
        box-shadow:
          0 8px 32px rgba(0, 0, 0, 0.4),
          inset 0 1px 0 rgba(255, 255, 255, 0.2);
      }
      h1 {
        font-size: 1.3rem;
        text-align: center;
        margin-bottom: 18px;
        font-weight: 600;
      }
      .label {
        font-size: 0.85rem;
        color: var(--muted);
        margin-bottom: 8px;
      }
      .levels {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 8px;
        margin-bottom: 18px;
      }
      .levels button {
        padding: 10px;
        border-radius: 10px;
        cursor: pointer;
        font-weight: 600;
        font-size: 0.9rem;
        background: rgba(255, 255, 255, 0.08);
        color: var(--text);
        border: 1px solid rgba(255, 255, 255, 0.15);
        transition: 0.2s;
      }
      .levels button:hover {
        background: rgba(255, 255, 255, 0.14);
      }
      .levels button.active {
        background: var(--accent);
        border-color: var(--accent);
        color: #fff;
      }
      .output {
        display: flex;
        align-items: center;
        gap: 8px;
        padding: 6px;
        margin-bottom: 18px;
        background: rgba(0, 0, 0, 0.25);
        border: 1px solid rgba(255, 255, 255, 0.15);
        border-radius: 12px;
      }
      .output input {
        flex: 1;
        min-width: 0;
        background: transparent;
        border: none;
        outline: none;
        color: #fff;
        font-size: 1rem;
        font-family: monospace;
        padding: 8px;
      }
      .copy {
        background: var(--accent);
        color: #fff;
        border: none;
        border-radius: 8px;
        padding: 8px 14px;
        font-weight: 600;
        cursor: pointer;
      }
      .len {
        display: flex;
        justify-content: space-between;
        font-size: 0.9rem;
        margin-bottom: 6px;
      }
      input[type="range"] {
        width: 100%;
        accent-color: var(--accent);
        margin-bottom: 12px;
      }
      .opt {
        display: flex;
        align-items: center;
        gap: 10px;
        margin: 10px 0;
        font-size: 0.95rem;
        cursor: pointer;
      }
      .opt input {
        accent-color: var(--accent);
        width: 16px;
        height: 16px;
      }
      .strength-row {
        display: flex;
        justify-content: space-between;
        font-size: 0.8rem;
        color: var(--muted);
        margin-top: 16px;
      }
      .strength {
        height: 6px;
        background: rgba(255, 255, 255, 0.12);
        border-radius: 3px;
        margin: 6px 0 18px;
        overflow: hidden;
      }
      #bar {
        height: 100%;
        width: 0;
        transition: 0.3s;
      }
      #generate {
        width: 100%;
        padding: 12px;
        background: var(--accent);
        color: #fff;
        border: none;
        border-radius: 12px;
        font-weight: 600;
        font-size: 1rem;
        cursor: pointer;
        transition: 0.2s;
      }
      #generate:hover,
      .copy:hover {
        background: var(--accent-hover);
      }
    </style>
  </head>
  <body>
    <div class="card">
      <h1>Password Generator</h1>

      <div class="label">What type of password do you need?</div>
      <div class="levels">
        <button data-level="easy">Easy</button>
        <button data-level="medium" class="active">Medium</button>
        <button data-level="strong">Strong</button>
      </div>

      <div class="output">
        <input type="text" id="password" readonly />
        <button class="copy" id="copy">Copy</button>
      </div>

      <div class="len">
        <span>Password Length</span><strong id="lenVal">12</strong>
      </div>
      <input type="range" id="length" min="4" max="32" value="12" />

      <label class="opt"
        ><input type="checkbox" id="upper" /> Uppercase Letters</label
      >
      <label class="opt"
        ><input type="checkbox" id="lower" /> Lowercase Letters</label
      >
      <label class="opt"><input type="checkbox" id="numbers" /> Numbers</label>
      <label class="opt"><input type="checkbox" id="symbols" /> Symbols</label>

      <div class="strength-row">
        <span>Strength</span><span id="strengthText"></span>
      </div>
      <div class="strength"><div id="bar"></div></div>

      <button id="generate">Generate Password</button>
    </div>

    <script>
      const sets = {
        upper: "ABCDEFGHIJKLMNOPQRSTUVWXYZ",
        lower: "abcdefghijklmnopqrstuvwxyz",
        numbers: "0123456789",
        symbols: "!@#$%^&*()-_=+[]{};:,.<>?",
      };
      const presets = {
        easy: {
          length: 8,
          upper: false,
          lower: true,
          numbers: false,
          symbols: false,
        },
        medium: {
          length: 12,
          upper: true,
          lower: true,
          numbers: true,
          symbols: false,
        },
        strong: {
          length: 20,
          upper: true,
          lower: true,
          numbers: true,
          symbols: true,
        },
      };
      const $ = (id) => document.getElementById(id);

      function randInt(max) {
        const a = new Uint32Array(1);
        const limit = Math.floor(0x100000000 / max) * max;
        do {
          crypto.getRandomValues(a);
        } while (a[0] >= limit);
        return a[0] % max;
      }

      function applyPreset(level) {
        const p = presets[level];
        $("length").value = p.length;
        $("lenVal").textContent = p.length;
        ["upper", "lower", "numbers", "symbols"].forEach(
          (k) => ($(k).checked = p[k]),
        );
        document
          .querySelectorAll(".levels button")
          .forEach((b) =>
            b.classList.toggle("active", b.dataset.level === level),
          );
        generate();
      }

      function generate() {
        const active = Object.keys(sets).filter((k) => $(k).checked);
        if (!active.length) {
          $("password").value = "";
          showStrength(0, 0);
          return;
        }
        const len = +$("length").value;
        const pool = active.map((k) => sets[k]).join("");
        const chars = active.map((k) => sets[k][randInt(sets[k].length)]);
        while (chars.length < len) chars.push(pool[randInt(pool.length)]);
        for (let i = chars.length - 1; i > 0; i--) {
          const j = randInt(i + 1);
          [chars[i], chars[j]] = [chars[j], chars[i]];
        }
        $("password").value = chars.slice(0, len).join("");
        showStrength(len, active.length);
      }

      function showStrength(len, types) {
        const score = len + types * 4;
        const bar = $("bar"),
          txt = $("strengthText");
        if (!len) {
          bar.style.width = "0";
          txt.textContent = "";
        } else if (score < 20) {
          bar.style.width = "33%";
          bar.style.background = "#f59e0b";
          txt.textContent = "Easy";
        } else if (score < 30) {
          bar.style.width = "66%";
          bar.style.background = "#3b82f6";
          txt.textContent = "Medium";
        } else {
          bar.style.width = "100%";
          bar.style.background = "#10b981";
          txt.textContent = "Strong";
        }
      }

      document
        .querySelectorAll(".levels button")
        .forEach((b) =>
          b.addEventListener("click", () => applyPreset(b.dataset.level)),
        );
      $("length").addEventListener("input", (e) => {
        $("lenVal").textContent = e.target.value;
        generate();
      });
      ["upper", "lower", "numbers", "symbols"].forEach((id) =>
        $(id).addEventListener("change", generate),
      );
      $("generate").addEventListener("click", generate);
      $("copy").addEventListener("click", async () => {
        const v = $("password").value;
        if (!v) return;
        await navigator.clipboard.writeText(v);
        $("copy").textContent = "Copied!";
        setTimeout(() => ($("copy").textContent = "Copy"), 1200);
      });
      applyPreset("medium");
    </script>
  </body>
</html>
