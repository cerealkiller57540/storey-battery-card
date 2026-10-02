<div align="center">

# 🔋 Storey Battery Card

**A home-battery card for Home Assistant: a 3D battery stack whose joints crackle with electricity, rendered in WebGL.**

[![HACS Custom][hacs-badge]][hacs-url]
[![Release][release-badge]][release-url]
[![Validate][validate-badge]][validate-url]
[![License: MIT][license-badge]][license-url]

[![Open your Home Assistant instance and open this repository in HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=cerealkiller57540&repository=storey-battery-card&category=plugin)

<img src="https://raw.githubusercontent.com/cerealkiller57540/storey-battery-card/main/images/charge.gif" alt="Storey Battery Card while charging: electric arcs run along the module joints" width="448">

</div>

The stack is drawn from the real footprint of a Sunology STOREY battery, from 1 to 4 modules. The joints between modules carry a live electric arc (yellow when charging, blue when discharging; cyan and magenta in Neo Tokyo mode), with sparks, a crackling filament, and a trace that runs in the direction of the power flow. The dot-matrix panels show the state of charge, the power and the direction.

It works with **any** home battery that exposes a state-of-charge sensor and a power sensor. The look is Storey's; the data can come from anywhere.

<img src="https://raw.githubusercontent.com/cerealkiller57540/storey-battery-card/main/images/discharge.png" alt="Storey Battery Card while discharging" width="400">

## ✨ Features

- **Two cards in one install**
  - `storey-battery-card-gl`: electric joints rendered by a WebGL shader (recommended).
  - `storey-battery-card`: lighter version, same look, arcs drawn in SVG.
- **1 to 4 modules**, fixed in the config or read from a sensor. Capacity is shown as stored / total kWh (2.2 kWh per module).
- **Dot-matrix panels** for SOC, power and direction, with an animated transition when a value changes.
- **Full battery animation** above a configurable SOC threshold.
- **Neo Tokyo mode** (cyan / magenta palette) or your own accent and background colours.
- **Error state**: if the status sensor goes `unavailable`, the whole card turns red instead of showing stale numbers.
- **Full visual editor**: every arc setting is a slider, no YAML needed.
- Releases its WebGL context when removed (Android WebViews cap a page at 8 contexts), and falls back to SVG if WebGL is not available.

## 📦 Installation

### HACS (recommended)

1. Click the **Open in HACS** button above, or add this repository as a custom repository in HACS (category **Dashboard**): `https://github.com/cerealkiller57540/storey-battery-card`.
2. Download **Storey Battery Card**.
3. Reload your browser.

HACS registers one resource, `storey-battery-card.js`. It loads the WebGL variant on its own, so **do not** add `storey-battery-card-gl.js` as a second resource.

### Manual

1. Copy both files from [`dist/`](dist) to `config/www/storey-battery-card/`.
2. Add a dashboard resource: URL `/local/storey-battery-card/storey-battery-card.js`, type **JavaScript module**.

## 🚀 Usage

Add a card from the dashboard editor and search for **Storey**, or use YAML:

```yaml
type: custom:storey-battery-card-gl
modules: 2                                # extra modules on top of the base: 0–3
soc_entity: sensor.battery_soc            # %
power_entity: sensor.battery_power        # W, positive = charging
master_status_entity: sensor.battery_status   # optional
cyberpunk_mode: true
header:
  title: POWER CELL
  icon: mdi:battery-charging
```

### How the direction is decided

1. If `|power|` is below `power_threshold`, the battery is **idle**.
2. Otherwise, if `master_status_entity` is set, its state decides: `CHARGING` = charging, `OFF` = idle, anything else = discharging.
3. Otherwise the sign of `power_entity` decides: positive = charging.

`master_status_entity` is optional. Leave it out if your integration has no status sensor; the power sign is enough.

## ⚙️ Options

| Option | Type | Default | Description |
|---|---|---|---|
| `soc_entity` | string | — | State of charge sensor (%) |
| `power_entity` | string | — | Battery power sensor (W) |
| `master_status_entity` | string | — | Status sensor, see above |
| `modules` | number | `0` | Extra modules (0–3), so 1 to 4 in the stack |
| `modules_entity` | string | — | Sensor holding the module count; overrides `modules` when it holds an integer |
| `module_0_entity` … `module_3_entity` | string | — | Optional per-module sensor |
| `module_0_unit` … `module_3_unit` | string | — | Unit shown with it |
| `power_threshold` | number | `50` | Watts under which the battery counts as idle (steadies the arrow) |
| `soc_full_threshold` | number | `97` | SOC from which the "full" animation plays |
| `renderer` | string | `auto` | `auto` (WebGL, then SVG), `webgl`, `svg` — WebGL card |
| `cyberpunk_mode` | bool | `false` | Neo Tokyo palette |
| `color_accent` / `color_bg` | colour | `#edff00` / `#181818` | Accent and background (Neo Tokyo: `#00fff9` / `#0d0d1a`) |
| `card_mod_bg` | bool | `false` | Transparent background, to inherit the one from your theme or card-mod |
| `glow_enabled` | bool | `false` | Glow along the module joints |
| `neon_glow` | bool | `false` | Neon glow on the panels |
| `elec_color_charge` / `elec_color_discharge` | colour | auto | Arc colour per direction |
| `glitch_cat` / `glitch_color` | bool / colour | `true` / accent | A small glitching cat hides in the card |
| `panel_anim` | string | `fade` | Dot-matrix transition: `off`, `fade`, `flip` |
| `panel_ms` / `panel_stagger` | number | `500` / `30` | Transition length and per-column delay (ms) |
| `header` | object | — | `title`, `icon`, `color`, `font`, `title_size`, `icon_size`, `glow`, `glow_color`, `glow_size`, `gradient`, `gradient_from`, `gradient_to`, `letter_spacing`, `font_weight` |

The arc itself has about twenty `elec_*` settings (crackle speed, spark rate and amplitude, field range, filament, halo, breathing, flow speed and density, flow indexed on the watts…). They are easiest to tune from the visual editor, where each one is a slider with its range.

## ❓ FAQ

**My battery is not a Storey.** That is fine. The drawing is a Storey, but any SOC + power pair works.

**The arrow flickers between charging and discharging.** Raise `power_threshold`: small readings around zero then count as idle.

**Some cards go blank on my Android phone.** Android WebViews keep at most 8 WebGL contexts per page. This card uses one, and gives it back when it leaves the screen. If you run many WebGL cards on one view, use `storey-battery-card` (SVG) on some of them.

**Which theme is in the screenshots?** Neo Tokyo, the author's own dark theme (not published). The card works with any theme.

## 🌃 More neon cards

This card is part of a family. See the full collection at [**Home-Assistant-Neon-Cards**](https://github.com/cerealkiller57540/Home-Assistant-Neon-Cards).

---

## 🐾 Support this project

If you enjoy these cards, please consider donating to **Quatre Pattes**, an animal rescue organization.

[![Sauver des animaux](https://img.shields.io/badge/🐾%20Sauver%20des%20animaux-Faire%20un%20don-ff69b4?style=for-the-badge)](https://don.quatre-pattes.org/s/?_jtsuid=70083177244599792679303)

> 💛 No need to support me — just help the animals. Thank you!

---

## 🤝 Contributing

1. Fork the repo
2. Create your branch: `git checkout -b feature/my-card`
3. Commit and push
4. Open a Pull Request

---

## 📄 License

[MIT License][license-url]

[hacs-badge]: https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge
[hacs-url]: https://hacs.xyz
[release-badge]: https://img.shields.io/github/v/release/cerealkiller57540/storey-battery-card?style=for-the-badge
[release-url]: https://github.com/cerealkiller57540/storey-battery-card/releases
[validate-badge]: https://img.shields.io/github/actions/workflow/status/cerealkiller57540/storey-battery-card/validate.yml?branch=main&label=HACS&style=for-the-badge
[validate-url]: https://github.com/cerealkiller57540/storey-battery-card/actions/workflows/validate.yml
[license-badge]: https://img.shields.io/github/license/cerealkiller57540/storey-battery-card?style=for-the-badge
[license-url]: LICENSE
