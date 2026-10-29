# Project OpenField: Team 101 Report

Team report website for **RAS 304, Fall 2026** (Arizona State University).

OpenField is an open-source, affordable autonomous robot that marks construction layouts directly onto new home foundations.

**Live site:** https://ras4-f26-2028-team-1.github.io/RAS304-F26-team1.github.io/

## Team

| Member | Subsystem |
| --- | --- |
| Immanwelle (Mimi) Nadutey | Motor Driver |
| Owen Callaway | Master |
| Landon Kuhman | LiDAR |
| Tan Thanh Huynh | Spray |
| Jaedyn Harris | Cliff Sensor |

## Repository Layout

- `docs/`: website pages (Markdown)
- `docs/image/`: images, diagrams, diagram source files, and video
- `mkdocs.yml`: site settings and page order
- `.github/workflows/pages.yml`: builds and publishes the site on every push to `main`

## Building Locally

```
pip install -r requirements.txt
mkdocs serve
```
