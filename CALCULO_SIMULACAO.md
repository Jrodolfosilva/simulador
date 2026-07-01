# Cálculo da Simuladora INSS de Obra

**Base legal:** IN RFB nº 2.021/2021 · **VAU:** Jun/2026 · **Mínimo:** 100 m²

---

## Resumo em português simples

A Receita Federal presume que toda construção gastou uma certa quantia em mão de obra, mesmo sem nota fiscal — e cobra INSS em cima dessa quantia presumida (o "custo de obra padrão", tabela **CUB/VAU**). O simulador reproduz essa conta:

1. **Quanto vale sua obra "no papel"** — pega a área construída (m²) e multiplica pelo preço oficial do m² (VAU), que muda por estado e por tipo de construção (casa, prédio, comércio, galpão, popular).
2. **Desconta o que já é material/projeto** — desse valor, só uma fatia é considerada "mão de obra" (89% a 98%, dependendo do tipo).
3. **Aplica o fator social (só pessoa física)** — quanto maior a área, maior a fatia presumida de mão de obra contratada (de 20% até 90%). Empresa (PJ) não tem esse desconto, entra sempre com 100%.
4. **Aplica a alíquota da mão de obra** — 20% se é alvenaria, 15% se é madeira/mista.
5. **Isso dá a "remuneração presumida" (RMT)** — o salário presumido de todos os trabalhadores da obra somado.
6. **Aplica a alíquota do INSS** — 36,8% em cima da RMT (31% se PJ do Simples Nacional). Esse é o **V0**, o valor que a Receita cobraria se fosse fazer uma auditoria hoje, sem nenhum planejamento tributário.
7. **Se a obra já tem alguns anos, desconta a decadência** — a Receita só pode cobrar até 5 anos depois do direito de cobrar nascer. O simulador calcula mês a mês da obra: cada mês que passou desse prazo é descontado do V0 proporcionalmente. Se a obra toda já passou do prazo, o INSS zera.
8. **Com planejamento tributário, o valor cai bastante (V3)** — laudos técnicos e retificação da declaração reduzem o V0 pra só 27,17% dele (obras até 350 m²) ou 38,04% (obras maiores). Isso é a diferença entre "pagar o presumido" e "pagar o que documentação comprova".
9. **Se a obra já foi iniciada e está em atraso, soma multa e juros** — 25% de multa, correção pela SELIC acumulada (composta, mês a mês) e R$100 por mês de mora. Isso só aparece se o usuário informou data de início da obra.

Resultado final mostrado: **V0** (sem planejamento) vs **V3** (com planejamento) vs **economia** — e, se aplicável, o total com multa/juros de quem já está devendo.

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

Calculada **mês a mês** (por competência), não pela conclusão da obra como um todo. Para cada mês da obra, o prazo decadencial nasce em 1º de janeiro do ano seguinte e dura 5 anos (Art. 173, I, CTN) — ou seja, um mês de competência caduca quando `1º/jan/(ano do mês + 6) ≤ hoje`.

```
V0 = V0_base × (meses_no_prazo / total_meses)
```

Decadência total (todos os meses caducados) → INSS = zero.
Decadência parcial → V0 proporcional só aos meses ainda dentro do prazo.

---

## INSS com planejamento (V3)

```
V3 = V0 × razão_redução
```

| Área | Razão (exata no código) | Paga | Economiza |
|---|---|---|---|
| ≤ 350 m² | 27,174% | ~27,2% | ~72,8% |
| > 350 m² | 38,043% | ~38,0% | ~61,9% |

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
