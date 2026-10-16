# 📈 Simulador de Investimentos - FINANCIAL HP

Planilha para simular o crescimento do patrimônio com aportes mensais, estimar a renda passiva com dividendos e sugerir como distribuir o aporte entre tipos de fundos conforme o perfil do investidor.

> ⚠️ **Aviso:** esta ferramenta é uma simulação educacional. Os resultados dependem das taxas informadas e não representam garantia de rentabilidade.

---

## 🖼️ Visão geral

![Tela da planilha do Simulador de Investimentos](docs/tela.png)

*Exemplo com salário de R$ 15.000,00 e perfil Conservador. O gráfico de pizza da distribuição fica logo abaixo da última tabela e não aparece neste recorte.*

---

## 🎯 O que a ferramenta faz

- Sugere o valor do aporte mensal com base no salário e no perfil do investidor.
- Projeta o patrimônio acumulado com esse aporte, usando juros compostos.
- Estima o rendimento mensal em dividendos para cada cenário.
- Divide o aporte entre tipos de fundos de investimento e mostra o resultado em um gráfico de pizza.

---

## 🗂️ Estrutura do arquivo

O arquivo tem duas abas:

| Aba | Função |
|---|---|
| **Ferramenta** | Tela principal: entradas, cenários simulados, distribuição por perfil e gráfico |
| **Apoio** | Tabelas auxiliares: percentual de aporte de cada perfil e percentual sugerido por tipo de fundo |

---

## 🖥️ Aba Ferramenta

### ⚙️ Configurações da carteira

| Célula | Campo | Descrição |
|---|---|---|
| `D5` | Salário atual do investidor | **Entrada.** Renda mensal usada como base dos cálculos |
| `D6` | Rendimento da minha carteira | **Entrada.** Rendimento de dividendos sobre o patrimônio (hoje 0,9%) |
| `D7` | Perfil do investidor | **Entrada.** Lista suspensa: `AGRESSIVO`, `MODERADO` ou `CONSERVADOR`. É o **único seletor de perfil** da planilha |
| `D8` | Sugestão de investimento fixa | `=D5*30%` |

### 💸 Investimento mensal

| Célula | Campo | Descrição |
|---|---|---|
| `D12` | Quanto quero investir mensalmente? | `=C35`: usa o aporte sugerido para o perfil. Para simular outro valor, digite-o por cima da fórmula |
| `D13` | Por quanto tempo? | **Entrada.** Prazo em anos |
| `D14` | Taxa de rendimento mensal | **Entrada.** Hoje 1,093% |
| `D15` | Patrimônio total no período | `=FV(D14,D13*12,D12*-1)` |
| `D16` | Rendimento de dividendos mensal | `=D15*$D$6` |

### 📝 Cenários simulados

Patrimônio (coluna C) e dividendos mensais (coluna D) para **1, 5, 10, 15, 20, 25, 30, 35, 40, 45 e 50 anos**, usando o aporte e a taxa definidos acima:

```
Patrimônio:  =FV($D$14, anos*12, $D$12*-1)
Dividendos:  =C20*$D$6
Rótulo:      ="Quanto terei em "&A22&IF(A22=1," ano"," anos")&" ?"
```

Os prazos em anos ficam na **coluna A** (`A22:A32`). As fórmulas de `C20:C30` e os rótulos de `B20:B30` apontam para eles, então alterar um prazo atualiza o cálculo e o texto.

### 🧩 Simulação de distribuição de carteira por perfil

| Célula | Campo | Fórmula / descrição |
|---|---|---|
| `C34` | Perfil do investidor | `=D7` (acompanha o perfil escolhido no topo) |
| `D35` | Salário do investidor | `=D5` |
| `C35` | Aporte mensal sugerido | `=IFERROR(D35*VLOOKUP(C34,Apoio!$I$4:$J$6,2,FALSE),0)` |
| `C37:C42` | Percentual sugerido por fundo | `=VLOOKUP($C$34&"-"&B37,Apoio!$A:$D,3,FALSE)` |
| `D37:D42` | Valor por fundo | `=$C$35*C37` |

O aporte sugerido é uma porcentagem do salário, conforme o perfil. Os percentuais ficam na aba Apoio (`I3:J6`):

| Perfil | % do salário sugerido como aporte |
|---|---|
| Agressivo | 50% |
| Moderado | 40% |
| Conservador | 30% |

Esse aporte é dividido entre os tipos de fundos abaixo:

- **Tijolo**: fundos imobiliários de imóveis físicos
- **Papel**: fundos imobiliários de títulos de crédito (CRIs e similares)
- **FOF's**: fundos de fundos
- **Híbridos**: fundos que combinam tijolo e papel
- **Renda fixa**
- **Desenvolvimento**

| Tipo de fundo | Agressivo | Moderado | Conservador |
|---|---|---|---|
| Tijolo | 50% | 40% | 10% |
| Papel | 30% | 20% | 50% |
| FOF's | 10% | 10% | 5% |
| Híbridos | 10% | 10% | 5% |
| Renda fixa | 0% | 20% | 30% |
| Desenvolvimento | 0% | 0% | 0% |
| **Total** | **100%** | **100%** | **100%** |

### 📊 Distribuição da carteira por investimento

Gráfico de pizza que usa os tipos de fundo (`B37:B42`) e seus percentuais (`C37:C42`), e por isso acompanha o perfil escolhido em `D7`.

---

## 🗃️ Aba Apoio

| Intervalo | Conteúdo |
|---|---|
| `I3:J6` | Tabela de **% do salário usado como aporte** por perfil (Agressivo, Moderado, Conservador) |
| `A3:D11` | Tabela do perfil **Agressivo**: aporte, percentual sugerido por fundo e valores |
| `A12:D20` | Tabela do perfil **Moderado** |
| `A21:D29` | Tabela do perfil **Conservador** |

A aba Ferramenta consulta as tabelas por uma **chave** no formato `PERFIL-TIPO` (por exemplo, `CONSERVADOR-PAPEL`), montada na coluna A:

```
Chave:          =$C$3&"-"&B6                         → "AGRESSIVO-TIJOLO"
Valor:          =$C$4*C6                             → aporte do perfil × percentual
Aporte perfil:  =D4*VLOOKUP(C3,$I$4:$J$6,2,FALSE)    → salário × % do perfil
```

Para mudar a distribuição de um perfil, edite os percentuais na coluna C da tabela correspondente (`C6:C11`, `C15:C20` e `C24:C29`). Para mudar o percentual de aporte de um perfil, edite `J4:J6`.

> ⚠️ Os nomes de perfil em `C3`, `C12` e `C21` formam as chaves usadas pela busca e **não devem ser alterados**. Se algum for modificado, a distribuição na aba Ferramenta retorna erro. Há um aviso sobre isso na célula `A1`.

---

## 🏷️ Intervalos nomeados

| Nome | Célula | Conteúdo |
|---|---|---|
| `salario_carteira` | `Ferramenta!D5` | Salário |
| `rendimento_carteira` | `Ferramenta!D6` | Rendimento de dividendos |
| `sugestao_carteira` | `Ferramenta!D8` | Sugestão fixa (30% do salário) |
| `aporte_mensal` | `Ferramenta!D12` | Aporte mensal |
| `periodo_aporte` | `Ferramenta!D13` | Prazo em anos |
| `taxa_mensal` | `Ferramenta!D14` | Taxa de rendimento mensal |
| `patrimonio_total` | `Ferramenta!D15` | Patrimônio no período |
| `dividendos_mensal` | `Ferramenta!D16` | Dividendos mensais |

---

## 🚀 Como usar

1. Informe o **salário** (`D5`) e o **rendimento de dividendos** (`D6`).
2. Escolha o **perfil do investidor** em `D7`. A distribuição, o aporte sugerido e o gráfico se atualizam.
3. Defina o **prazo** em anos (`D13`) e a **taxa mensal** (`D14`). O aporte mensal (`D12`) já vem do perfil.
4. Consulte os **cenários simulados** para ver o patrimônio e os dividendos em cada horizonte.
5. Se quiser testar um aporte diferente do sugerido, digite o valor em `D12`. Para voltar ao automático, restaure a fórmula `=C35`.

### Exemplos

Com salário de **R$ 15.000,00**:

| Perfil | Aporte sugerido | Distribuição |
|---|---|---|
| Agressivo (50%) | R$ 7.500,00 | Tijolo R$ 3.750,00, Papel R$ 2.250,00, FOF's R$ 750,00, Híbridos R$ 750,00 |
| Moderado (40%) | R$ 6.000,00 | Tijolo R$ 2.400,00, Papel R$ 1.200,00, FOF's R$ 600,00, Híbridos R$ 600,00, Renda fixa R$ 1.200,00 |
| Conservador (30%) | R$ 4.500,00 | Papel R$ 2.250,00, Renda fixa R$ 1.350,00, Tijolo R$ 450,00, FOF's R$ 225,00, Híbridos R$ 225,00 |

**Projeção (perfil Conservador):** com aporte de **R$ 4.500,00/mês**, taxa de **1,093% ao mês** e prazo de **5 anos**, o patrimônio projetado é de aproximadamente **R$ 378.708,23**, com cerca de **R$ 3.408,37/mês** em dividendos.

---

## ⚠️ Premissas e limitações

- Os cálculos usam **taxa de rendimento constante**, sem considerar inflação, impostos, taxas de administração ou variações de mercado.
- Os aportes são **mensais, fixos e sem saldo inicial**, e o `FV` os considera pagos ao fim de cada mês.
- Os dividendos são um **percentual fixo do patrimônio** (`D6`), não a distribuição real de cada fundo.
- Os percentuais por perfil são **sugestões genéricas** e podem ser ajustados nas tabelas da aba Apoio.
- Rentabilidade passada ou projetada não garante resultados futuros.

---

## 🔄 Alterações recentes

- **Perfil único:** o seletor de perfil agora é `D7`; `C34` apenas acompanha esse valor.
- **Aporte ligado ao perfil:** `D12` passou a usar o aporte sugerido (`=C35`), de modo que a projeção e a distribuição partem do mesmo valor.
- **Percentuais fora da fórmula:** os 50%, 40% e 30% saíram da fórmula de `C35` e ficam na tabela `Apoio!I3:J6`.
- **Aba Apoio protegida contra erro:** os nomes de perfil que formam as chaves deixaram de ser listas suspensas e ganharam um aviso.
- **Rótulos dos cenários dinâmicos:** os textos "Quanto terei em X anos?" agora seguem os prazos da coluna A.
- **Formatação:** `D6` exibe uma casa decimal (0,9%) e `D14` exibe três (1,093%), mostrando os valores realmente usados nos cálculos.

---


