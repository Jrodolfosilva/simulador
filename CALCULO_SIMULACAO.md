# Cálculo da Simuladora INSS de Obra

**Base legal:** IN RFB nº 2.021/2021 · **VAU:** Jun/2026 · **Mínimo:** 100 m²

---

## INSS sem planejamento (V0)

```
RMT = Área × VAU[estado][dest] × Equivalência[dest] × FatorSocial × Alíquota_MO
V0  = RMT × Taxa_INSS
```

**Equivalência por destinação:** uni 89% · multi 90% · com 86% · gal 95% · pop/conj 98%

**Alíquota MO:** alvenaria 20% · madeira/mista 15%

**Taxa INSS:** PF e PJ 36,8% · PJ Simples 31%

**Fator Social (só PF) — % de mão de obra por faixa de área:**

| Área | Fator |
|---|---|
| ≤ 100 m² | 20% |
| 101–200 m² | 40% |
| 201–300 m² | 55% |
| 301–400 m² | 70% |
| > 400 m² | 90% |

> PJ: fator fixo 100%

---

## Decadência (opcional — se período informado)

Meses com mais de 5 anos da conclusão perdem direito de cobrança (Art. 173 CTN).

```
V0 = V0_base × (meses_no_prazo / total_meses)
```

Decadência total → INSS = zero.

---

## INSS com planejamento (V3)

```
V3 = V0 × razão_redução
```

| Área | Razão | Paga | Economiza |
|---|---|---|---|
| ≤ 350 m² | 27,17% | ~27% | ~73% |
| > 350 m² | 38,04% | ~38% | ~62% |

---

## Encargos por mora (só se obra já iniciada)

Ativo apenas quando data de início é passada.

```
Multa    = V0 × 25%
Correção = V0 × 25% × SELIC_acumulada_composta
Mora     = meses × R$ 100
Total    = V0 + Multa + Correção + Mora
```

SELIC: API Banco Central em tempo real · fallback 25% a.a.

---

## Pontos para revisão

- [ ] Faixas do Fator Social (20/40/55/70/90%) — corretas pela IN vigente?
- [ ] Razão de redução 27,17% / 38,04% — confirmar com laudos reais
- [ ] Taxa INSS 36,8% — composição exata (empresa + RAT + outros)?
- [ ] Multa 25% — percentual correto para notificação fiscal?
- [ ] Mora R$100/mês — tem base legal ou deve usar só SELIC?
- [ ] Tabela VAU Jun/2026 — está na versão mais recente (SERO/Alpha GT)?
