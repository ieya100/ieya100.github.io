<style>
.theme-toggle {
  position: fixed;
  top: 15px;
  right: 15px;
  background: #222;
  color: #fff;
  border-radius: 8px;
  padding: 6px 10px;
  cursor: pointer;
  font-size: 14px;
  z-index: 9999;
  user-select: none;
}
.light-mode .theme-toggle {
  background: #ddd;
  color: #000;
}
</style>

<div class="theme-toggle" onclick="toggleTheme()">🌙 Dark Mode</div>

# Hardware Investigation Archive

Добро пожаловать в личный архив технических исследований и кейсов, связанных с
железом: SSD, NVMe-контроллерами, ноутбуками, перегревом, нестабильным поведением
при пробуждении, электронными сбоями и другими реальными случаями из практики.

Здесь сохранены материалы, которые часто удаляются на Reddit, форумах или в соцсетях, 
но представляют ценность для инженеров, энтузиастов и тех, кто сталкивается с редкими проблемами оборудования.

## Разделы

### 🔧 Исследования и кейсы
- [Lexar NM790 — зависания после сна, загрузка 100%](cases/lexar-nm790-sleep-freeze.md)
- ASUS ProArt — перегоревший разъём питания (в разработке)
- Razer Blade 14 — особенности Modern Standby и SSD (в разработке)
- NVMe Maxio MAP1602 — ошибки контроллера и поведение под нагрузкой (в разработке)

### ℹ О сайте
- [О проекте](about.md)

---

Материалы обновляются по мере появления новых кейсов.

<script>
function applyTheme(theme) {
  document.documentElement.className = theme;
  localStorage.setItem("theme", theme);

  // Update Utterances iframe theme
  const utterances = document.querySelector("iframe.utterances-frame");
  if (utterances) {
    utterances.contentWindow.postMessage(
      { type: "set-theme", theme: theme === "dark-mode" ? "github-dark" : "github-light" },
      "https://utteranc.es"
    );
  }

  // Update toggle button text
  const btn = document.querySelector(".theme-toggle");
  if (btn) {
    btn.innerHTML = theme === "dark-mode" ? "☀️ Light Mode" : "🌙 Dark Mode";
  }
}

function toggleTheme() {
  const current = localStorage.getItem("theme") || "light-mode";
  const next = current === "light-mode" ? "dark-mode" : "light-mode";
  applyTheme(next);
}

// Load saved theme on startup
applyTheme(localStorage.getItem("theme") || 
           (window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark-mode" : "light-mode"));
</script>
<script src="https://utteranc.es/client.js"
        repo="ieya100/ieya100.github.io"
        issue-term="pathname"
        theme="github-dark"
        crossorigin="anonymous"
        async>
</script>
