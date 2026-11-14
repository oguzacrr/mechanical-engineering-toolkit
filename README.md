# AI-Assisted Strength of Materials Calculations for Machine Elements

Open-source Python tools for strength-of-materials calculations on machine elements
(beams, shafts, and more), validated against hand calculations and later extended with AI.

> **Status: in development.** The project plan is public; the first module is being built.

## Why this project

Mechanical engineering calculations (bending, shear, torsion, deflection) are repetitive and
error-prone by hand. This project turns them into small, tested, readable Python functions, then
explores how AI can assist engineers: design optimization, fast surrogate models, and
natural-language assistants.

## Roadmap

| Module | Scope | Status |
|---|---|---|
| 1. Beams | Simply supported beam: reactions, shear force and bending moment diagrams, max bending stress, max deflection | Planning |
| 2. Shafts | Torsion, combined bending and torsion stress | Planned |
| 3. Other machine elements | Bolts, springs, keys: strength checks | Planned |
| 4. AI layer | Optimization, surrogate models, LLM assistant | Planned |

Details: [docs/PLAN.md](docs/PLAN.md) (written in Turkish for now).

## Principles

- **Verified results:** every function is tested against a hand-solved textbook case.
- **SI units** internally; clear sign conventions.
- **Small steps:** one function, one test, one commit.

## Getting started

```bash
git clone https://github.com/oguzacrr/mechanical-engineering-toolkit.git
cd mechanical-engineering-toolkit
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt
```

## Author

Oğuzhan Acar, mechanical engineer working at the intersection of mechanical engineering and AI.
[GitHub profile](https://github.com/oguzacrr)

## License

See [LICENSE](LICENSE).
