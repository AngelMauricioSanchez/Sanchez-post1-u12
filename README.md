# Unidad 12 — Computación Emergente y Tendencias
## Post-Contenido 1: Circuitos Cuánticos con Qiskit

**Arquitectura de Computadores · Ingeniería de Sistemas · UFPS · 2026**

---

## Objetivo

Implementar y simular tres experimentos fundamentales de computación cuántica usando Qiskit y el simulador AerSimulator:

1. **Estado de Bell** — entrelazamiento cuántico
2. **Algoritmo de Deutsch-Jozsa** — primera ventaja cuántica documentada
3. **Algoritmo de Grover** — búsqueda cuántica con aceleración cuadrática

---

## Prerrequisitos

```bash
python --version          # Python 3.9+
pip install qiskit qiskit-aer matplotlib
python -c "import qiskit; print(qiskit.__version__)"
```

> Todos los experimentos usan **AerSimulator** (simulador clásico). No se requiere hardware cuántico real ni cuenta IBM Quantum.

---

## Estructura del Repositorio

```
apellido-post1-u12/
├── README.md
├── src/
│   ├── bell_state.py        # Checkpoint 1
│   ├── deutsch_jozsa.py     # Checkpoint 2
│   └── grover.py            # Checkpoint 3
└── capturas/
    ├── bell_histogram.png
    ├── dj_constante_histogram.png
    ├── dj_balanceada_histogram.png
    ├── grover_00.png
    ├── grover_01.png
    ├── grover_10.png
    └── grover_11.png
```

---

## Paso 1 — Estado de Bell · Checkpoint 1

### Circuito

```
q0: ──H──●──M
         │
q1: ─────X──M
```

| Puerta | Acción |
|--------|--------|
| H (Hadamard) | Crea superposición en q0: `\|0⟩ → (\|0⟩+\|1⟩)/√2` |
| CNOT | Entrelaza q1 con q0 |
| Medición | Colapsa el estado |

### Resultados (1024 shots)

| Estado | Counts | Porcentaje |
|--------|--------|------------|
| \|00⟩ | ~512 | ~50% |
| \|11⟩ | ~512 | ~50% |
| \|01⟩ | 0 | 0% |
| \|10⟩ | 0 | 0% |

### Interpretación

Los únicos resultados posibles son `|00⟩` y `|11⟩`, **nunca** `|01⟩` ni `|10⟩`. Esto evidencia la **correlación perfecta del entrelazamiento cuántico**: medir un qubit en estado 0 colapsa instantáneamente el otro a 0, y viceversa, sin importar la distancia física.

![Bell Histogram](capturas/bell_histogram.png)

---

## Paso 2 — Algoritmo de Deutsch-Jozsa · Checkpoint 2

### ¿Por qué 1 evaluación cuántica vs 3 clásicas (n=2)?

Clásicamente, para distinguir una función constante de una balanceada con **certeza**, se necesita evaluar f en al menos `2^(n-1) + 1 = 3` entradas (peor caso con n=2). El algoritmo cuántico evalúa el **oráculo una sola vez** mediante:

1. **Superposición**: `H⊗n` lleva todos los qubits a superposición uniforme → el oráculo se evalúa sobre todas las entradas simultáneamente (paralelismo cuántico).
2. **Interferencia**: Una segunda capa `H⊗n` provoca interferencia constructiva en `|00⟩` si f es constante, o destructiva (eliminando `|00⟩`) si f es balanceada.

### Circuito (n=2)

```
q0: ──H──[Oráculo]──H──M
q1: ──H──[Oráculo]──H──M
q2: ──X──H──[Oráculo]──── (ancilla, no se mide)
```

### Resultados

| Oráculo | Resultado medido | Clasificación |
|---------|-----------------|---------------|
| Constante f(x)=0 | `\|00⟩` con 100% | CONSTANTE |
| Balanceada | Nunca `\|00⟩` | BALANCEADA |

**Oráculo Constante** (solo `|00⟩`):

![DJ Constante](capturas/dj_constante_histogram.png)

**Oráculo Balanceado** (`|00⟩` ausente por interferencia destructiva):

![DJ Balanceada](capturas/dj_balanceada_histogram.png)

---

## Paso 3 — Algoritmo de Grover · Checkpoint 3

### ¿Por qué 1 iteración es suficiente para n=2?

El número óptimo de iteraciones de Grover es `⌊(π/4)·√(N/M)⌋`, donde N=4 (estados) y M=1 (solución):

```
k = floor( (π/4) · √(4/1) ) = floor( (π/4) · 2 ) ≈ floor(1.57) = 1
```

Con 1 iteración, la probabilidad del estado marcado pasa de 25% a **~100%** (exactamente 1.0 para N=4).

### Circuito

```
q0: ──H──[Oráculo]──H──X──●──X──H──M
                          │
q1: ──H──[Oráculo]──H──X──Z──X──H──M
         (fase -1)    (difusor)
```

### Resultados — Los 4 Objetivos

| Target | Estado amplificado | Probabilidad obtenida | Resultado |
|--------|-------------------|----------------------|-----------|
| `\|00⟩` | `\|00⟩` | ~92.8% | CORRECTO |
| `\|01⟩` | `\|01⟩` | ~92.5% | CORRECTO |
| `\|10⟩` | `\|10⟩` | ~92.6% | CORRECTO |
| `\|11⟩` | `\|11⟩` | ~92.6% | CORRECTO |

![Grover 00](capturas/grover_00.png)
![Grover 01](capturas/grover_01.png)
![Grover 10](capturas/grover_10.png)
![Grover 11](capturas/grover_11.png)

---

## Principios Cuánticos Demostrados

| Principio | Experimento |
|-----------|-------------|
| Superposición | Todos (puerta Hadamard) |
| Entrelazamiento | Estado de Bell (CNOT) |
| Interferencia | Deutsch-Jozsa, Grover |
| Paralelismo cuántico | Deutsch-Jozsa (oráculo evalúa 2^n entradas) |
| Amplificación de amplitud | Grover (difusor) |

