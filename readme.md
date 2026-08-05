from pathlib import Path
from PIL import Image, ImageDraw, ImageFont, ImageFilter
import random
import zipfile
import textwrap

root = Path("/mnt/data/thivakar-github-readme")
assets = root / "assets"
assets.mkdir(parents=True, exist_ok=True)

# ---------- Create the custom monochrome banner ----------
W, H = 1400, 400
img = Image.new("RGB", (W, H), "#f3f0e8")
px = img.load()
random.seed(7)

# Subtle paper-like texture
for _ in range(45000):
    x = random.randrange(W)
    y = random.randrange(H)
    base = random.choice([(229, 226, 217), (246, 243, 235), (218, 216, 209)])
    alpha = random.randint(5, 18)
    old = px[x, y]
    px[x, y] = tuple(int((old[i] * (255-alpha) + base[i] * alpha) / 255) for i in range(3))

draw = ImageDraw.Draw(img)

title_font_path = "/usr/share/fonts/opentype/tlwg/Purisa-BoldOblique.otf"
subtitle_font_path = "/usr/share/fonts/opentype/inter/Inter-SemiBold.otf"
small_font_path = "/usr/share/fonts/opentype/inter/Inter-Regular.otf"

title_font = ImageFont.truetype(title_font_path, 150)
subtitle_font = ImageFont.truetype(subtitle_font_path, 30)
small_font = ImageFont.truetype(small_font_path, 22)

# Rough brush-style title: layered slightly shifted strokes
title = "THIVAKAR!"
tx, ty = 95, 90
for dx, dy in [(-4, 1), (3, -2), (1, 3), (0, 0)]:
    draw.text((tx + dx, ty + dy), title, font=title_font, fill="#090909")

# Hand-drawn underline
points = [(95, 276), (230, 286), (410, 279), (625, 290), (810, 278)]
draw.line(points, fill="#111111", width=8)
draw.line([(110, 291), (320, 299), (560, 294), (780, 300)], fill="#111111", width=3)

# Tagline
draw.text((105, 320), "SOFTWARE  /  AI  /  GAME DEV  /  3D ART", font=subtitle_font, fill="#171717")

# Right-side abstract wireframe / digital sculpture
cx, cy = 1120, 190
rings = [
    (160, 125, 0),
    (125, 100, 9),
    (90, 73, -8),
    (55, 45, 14),
]
for rw, rh, rot in rings:
    layer = Image.new("RGBA", (W, H), (0,0,0,0))
    ld = ImageDraw.Draw(layer)
    box = (cx-rw, cy-rh, cx+rw, cy+rh)
    ld.ellipse(box, outline="#101010", width=4)
    layer = layer.rotate(rot, center=(cx,cy), resample=Image.Resampling.BICUBIC)
    img = Image.alpha_composite(img.convert("RGBA"), layer)
    draw = ImageDraw.Draw(img)

# Polygonal lines
poly = [(990, 210), (1065, 92), (1178, 66), (1270, 155), (1235, 278), (1105, 320), (1000, 250)]
draw.line(poly + [poly[0]], fill="#111111", width=5)
for a, b in [
    ((990,210),(1178,66)), ((1065,92),(1235,278)), ((1270,155),(1105,320)),
    ((1000,250),(1270,155)), ((1105,320),(1178,66))
]:
    draw.line([a,b], fill="#2b2b2b", width=2)

# Decorative particles / coding marks
for x, y, s in [(870,80,16),(915,265,11),(1300,75,12),(1340,300,15),(820,210,10)]:
    draw.line([(x-s,y),(x+s,y)], fill="#111111", width=3)
    draw.line([(x,y-s),(x,y+s)], fill="#111111", width=3)

for x, y in [(880,135),(940,330),(1325,190),(830,55),(1260,340)]:
    draw.polygon([(x,y-8),(x+8,y+8),(x-8,y+8)], outline="#111111")

draw.text((1045, 346), "< BUILD / CREATE >", font=small_font, fill="#111111")

banner_path = assets / "banner.png"
img.convert("RGB").save(banner_path, quality=95)

# ---------- Decorative SVG assets ----------
brain_svg = """<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 360 300">
  <rect width="360" height="300" fill="none"/>
  <g fill="#0d1117" stroke="#f0f6fc" stroke-width="7" stroke-linecap="round" stroke-linejoin="round">
    <path d="M96 197c-34-6-55-30-50-59 3-19 16-34 34-42-2-29 18-51 46-55 20-3 39 5 51 21 16-22 46-29 70-17 20 10 32 30 31 52 24 9 39 31 37 55-2 30-27 53-58 54-12 24-39 38-66 32-22 18-54 18-76 0-24 5-49-7-59-29-8-1-15-5-20-12z"/>
    <path d="M123 75c-2 26 12 40 35 43M196 60c-8 19-3 35 14 48M255 94c-25 1-39 15-43 37M78 121c24-1 40 13 46 35M118 201c20-16 42-16 64-2M186 115c-23 12-31 32-25 58M233 151c-24 3-40 17-45 42M95 163c17 2 29 12 36 28M277 136c-17 3-29 13-36 29"/>
    <path d="M135 236c13 17 29 27 48 30 3-11 10-20 21-27"/>
  </g>
</svg>"""

flame_svg = """<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 280 340">
  <rect width="280" height="340" fill="none"/>
  <g fill="#0d1117" stroke="#f0f6fc" stroke-width="7" stroke-linecap="round" stroke-linejoin="round">
    <path d="M143 310c-67 0-112-48-105-111 5-45 34-78 69-109-1 32 11 50 26 62 8-49 35-84 74-119-4 48 20 69 35 98 14 27 22 54 17 85-8 55-52 94-116 94z"/>
    <path d="M143 283c-35 0-59-25-55-59 3-27 21-46 42-65 0 20 8 31 17 38 5-30 21-51 45-73-2 29 12 42 21 60 9 17 13 33 10 51-5 29-33 48-80 48z"/>
    <path d="M147 258c-18 0-30-13-28-30 2-14 11-24 22-34 0 10 4 16 9 20 3-15 11-26 24-37-1 15 6 22 11 31 4 9 6 17 5 26-3 15-17 24-43 24z"/>
  </g>
</svg>"""

divider_svg = """<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 40">
  <rect width="1200" height="40" fill="none"/>
  <path d="M20 20 H500 M700 20 H1180" stroke="#30363d" stroke-width="2"/>
  <circle cx="560" cy="20" r="5" fill="#f0f6fc"/>
  <rect x="592" y="12" width="16" height="16" transform="rotate(45 600 20)" fill="none" stroke="#f0f6fc" stroke-width="2"/>
  <circle cx="640" cy="20" r="5" fill="#f0f6fc"/>
</svg>"""

(assets / "brain.svg").write_text(brain_svg, encoding="utf-8")
(assets / "flame.svg").write_text(flame_svg, encoding="utf-8")
(assets / "divider.svg").write_text(divider_svg, encoding="utf-8")

# ---------- README.md ----------
readme = r"""<!--
╔══════════════════════════════════════════════════════════════════════════════╗
║  SETUP                                                                       ║
║  1. Create a public GitHub repository whose name is exactly your username.  ║
║  2. Upload this README.md and the complete assets/ folder.                   ║
║  3. Replace every YOUR_USERNAME, YOUR_EMAIL and YOUR_RESUME_LINK value.      ║
║                                                                              ║
║  GitHub does not allow custom CSS or page-wide fonts inside a README.        ║
║  The custom typeface is therefore baked into assets/banner.png.             ║
╚══════════════════════════════════════════════════════════════════════════════╝
-->

<p align="center">
  <img src="./assets/banner.png" width="100%" alt="Thivakar — Software, AI, Game Development and 3D Art"/>
</p>

<p align="center">
  <a href="https://github.com/YOUR_USERNAME?tab=followers">
    <img src="https://img.shields.io/github/followers/YOUR_USERNAME?label=FOLLOWERS&style=for-the-badge&logo=github&logoColor=white&labelColor=0d1117&color=21262d" alt="GitHub followers"/>
  </a>
  <a href="https://github.com/YOUR_USERNAME?tab=repositories">
    <img src="https://img.shields.io/badge/REPOSITORIES-EXPLORE-21262d?style=for-the-badge&logo=github&logoColor=white&labelColor=0d1117" alt="Repositories"/>
  </a>
  <a href="https://github.com/YOUR_USERNAME?tab=stars">
    <img src="https://img.shields.io/github/stars/YOUR_USERNAME?affiliations=OWNER&style=for-the-badge&logo=github&logoColor=white&label=STARS&labelColor=0d1117&color=21262d" alt="GitHub stars"/>
  </a>
</p>

<br/>

<h2 align="center">Know About Me</h2>

<img align="left" width="250" src="./assets/brain.svg" alt="Monochrome brain illustration"/>

### Hey there! I’m Thivakar.

**CS Undergrad · Software Developer · Hobby Game Developer · 3D Artist · Machine Learning & AI Engineer**

I’m a detail-oriented engineer with a strong foundation in **software development, systems design, and applied research**. I focus on building scalable, data-driven solutions with an emphasis on reliability and thoughtful design.

Alongside core engineering, I work with immersive technologies, combining creative problem-solving with technical depth to deliver innovative solutions. Always curious, always building, and consistently pushing ideas from concept to execution.

<br clear="left"/>

<p align="center">
  <img src="./assets/divider.svg" width="100%" alt="Section divider"/>
</p>

<h2 align="center">Top Projects</h2>

<img align="right" width="190" src="./assets/flame.svg" alt="Monochrome flame illustration"/>

### ◆ Featured Builds

[![ArcSight 3D](https://img.shields.io/badge/ARCSIGHT_3D-2D_BLUEPRINT_TO_3D-0d1117?style=for-the-badge&logo=blender&logoColor=white)](https://github.com/YOUR_USERNAME/arcsight-3d)  
Computer vision, OCR and Blender automation for transforming architectural blueprints into explorable 3D environments.

[![Code Vulnerability Guardian](https://img.shields.io/badge/CODE_VULNERABILITY_GUARDIAN-LOCAL_SECURITY_SCANNER-0d1117?style=for-the-badge&logo=securityscorecard&logoColor=white)](https://github.com/YOUR_USERNAME/code-vulnerability-guardian)  
A local security scanner combining rule-based checks with LLM-assisted code review and vulnerability explanations.

[![AI Texture Generator](https://img.shields.io/badge/AI_TEXTURE_GENERATOR-BLENDER_×_GENERATIVE_AI-0d1117?style=for-the-badge&logo=blender&logoColor=white)](https://github.com/YOUR_USERNAME/ai-texture-generator)  
An AI-assisted texture-generation pipeline with a Flask backend and direct Blender add-on integration.

[![Digital Twin AI](https://img.shields.io/badge/DIGITAL_TWIN_AI-PERSONA_×_MEMORY-0d1117?style=for-the-badge&logo=probot&logoColor=white)](https://github.com/YOUR_USERNAME/digital-twin-ai)  
A localized intelligent assistant with persona modelling, memory retrieval and decision simulation.

<br clear="right"/>

<p align="center">
  <img src="./assets/divider.svg" width="100%" alt="Section divider"/>
</p>

<h2 align="center">Engineering Stack</h2>

<p align="center">
  <img src="https://skillicons.dev/icons?i=java,spring,python,flask,js,html,css,mysql,postgres,redis,kafka,docker,git,github,opencv,blender,unity&theme=dark&perline=9" alt="Technology stack"/>
</p>

<p align="center">
  <code>Backend Engineering</code>&nbsp;&nbsp;
  <code>Machine Learning</code>&nbsp;&nbsp;
  <code>Computer Vision</code>&nbsp;&nbsp;
  <code>Game Development</code>&nbsp;&nbsp;
  <code>3D Pipelines</code>
</p>

<p align="center">
  <img src="./assets/divider.svg" width="100%" alt="Section divider"/>
</p>

<h2 align="center">Let’s Collaborate</h2>

<p align="center">
  Always open to new challenges and innovative projects.<br/>
  <strong>Let’s build the future of digital experiences.</strong>
</p>

<p align="center">
  <a href="https://github.com/YOUR_USERNAME">
    <img src="https://img.shields.io/badge/GITHUB-0d1117?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/>
  </a>
  <a href="https://www.linkedin.com/in/thivakar-t/">
    <img src="https://img.shields.io/badge/LINKEDIN-0d1117?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/>
  </a>
  <a href="mailto:YOUR_EMAIL">
    <img src="https://img.shields.io/badge/EMAIL-0d1117?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/>
  </a>
  <a href="YOUR_RESUME_LINK">
    <img src="https://img.shields.io/badge/RESUME-0d1117?style=for-the-badge&logo=readme&logoColor=white" alt="Resume"/>
  </a>
</p>

> Code is never finished; it only becomes slightly less terrible over time.
>
> Every commit leaves a small, discoverable trace of the engineer I was—and the engineer I am becoming.

<p align="center">
  <img src="./assets/divider.svg" width="100%" alt="Section divider"/>
</p>

<h2 align="center">Contribution</h2>

<p align="center">
  <img width="96%" src="https://github-readme-activity-graph.vercel.app/graph?username=YOUR_USERNAME&bg_color=0d1117&color=c9d1d9&line=f0f6fc&point=8b949e&area=true&area_color=21262d&hide_border=true&custom_title=Thivakar%27s%20Contribution%20Graph" alt="GitHub contribution graph"/>
</p>

<p align="center">
  <img width="48%" src="https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&hide_border=true&bg_color=0d1117&title_color=f0f6fc&text_color=c9d1d9&icon_color=f0f6fc&rank_icon=github" alt="Thivakar's GitHub statistics"/>
  <img width="48%" src="https://streak-stats.demolab.com?user=YOUR_USERNAME&hide_border=true&background=0D1117&stroke=30363D&ring=F0F6FC&fire=F0F6FC&currStreakNum=F0F6FC&sideNums=C9D1D9&currStreakLabel=F0F6FC&sideLabels=8B949E&dates=8B949E" alt="Thivakar's GitHub streak"/>
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=YOUR_USERNAME&label=PROFILE+VIEWS&style=for-the-badge&color=21262d&labelColor=0d1117" alt="Profile views"/>
</p>

<p align="center">
  <sub>Designed with a monochrome, dark GitHub aesthetic.</sub>
</p>
"""

(root / "README.md").write_text(readme, encoding="utf-8")

setup = """# Setup

1. Create a public GitHub repository with the exact same name as your GitHub username.
2. Upload `README.md` and the whole `assets` folder without changing the folder structure.
3. Replace:
   - `YOUR_USERNAME`
   - `YOUR_EMAIL`
   - `YOUR_RESUME_LINK`
4. Update the four project repository URLs if your repository names differ.
5. Commit the changes. GitHub will automatically display this as your profile README.

Important: GitHub controls the left profile sidebar, navigation, page background and global font.
A README can style only its content area. The custom display font in this package is rendered
inside `assets/banner.png`, so it appears consistently.
"""
(root / "SETUP.md").write_text(setup, encoding="utf-8")

zip_path = Path("/mnt/data/thivakar-github-readme-pack.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    for file in root.rglob("*"):
        if file.is_file():
            z.write(file, file.relative_to(root.parent))

print(f"Created: {root / 'README.md'}")
print(f"Created: {zip_path}")
