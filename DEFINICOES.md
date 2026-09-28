# Definições — Finanças da família (Orlando e Mariana)

> Escrito durante a análise de `analise.ipynb`. Toda métrica citada na síntese
> tem entrada aqui. Janela de referência: **dez/2025 a ago/2026, 9 meses
> completos** (nov/2025 e set/2026 são parciais e ficam fora de toda média).

## Grão

Cada linha dos arquivos `dados/marido/` e `dados/esposa/` é **um lançamento na
fatura de um cartão** — uma compra à vista, **uma parcela** de uma compra
parcelada, ou um crédito. Não é uma compra: uma compra em 10× gera 10 linhas,
em 10 meses diferentes.

Três números diferentes convivem no mesmo dado:

- **fluxo do mês** — o que caiu na fatura naquele mês. É o que a análise usa em
  todas as métricas abaixo, salvo aviso.
- **decisão** — a compra inteira contada no mês em que foi feita. Não usado.
- **compromisso futuro** — as parcelas que ainda vão cair. Usado só na esteira
  de dívidas.

## Mês de referência

`mes` = mês da coluna `date`, que é a **data da compra**, não a do vencimento.
O nome do arquivo (`Nubank_2026-01-03.csv`) é a data da fatura e contém compras
do mês anterior — **nunca** usado para agrupar.

## destino (nome normalizado)

Derivado de `title`: remove o prefixo `Pix no Crédito - `, remove o sufixo de
parcela ` - N/M` ou ` - Parcela N/M`, remove acentos, colapsa espaços, sobe para
maiúscula.

Obrigatório porque **o mesmo destinatário aparece em três formatos** —
`OTAVIO GITURU MEDEIROS FIGUEIREDO`, `Otavio Gituru Medeiros Figueiredo` e
`Pix no Crédito - Otavio Gituru Medeiros Figueiredo`. Agrupar por `title` cru, ou
filtrar pelo prefixo, perde parte do valor: no caso do repasse ao pai, um terço.
Empresas também: a distribuidora de energia tem três grafias e a Starlink duas.

## Conta do pai (conta neutra, fora do gasto da família)

Duas partes, e ambas são **conta neutra** — o pai reembolsa, confirmado pelo
casal fora dos dados:

1. **Pix aos dois nomes confirmados:** `destino` em
   `MARIANA ARAGAO MEDEIROS` ou `VITOR VELOSO MEDEIROS`.
2. **Compras identificadas pelo casal**, revisadas item por item:
   `BURITIAZUL COMERCIO DE`, `CANELACLARO LO AGRO`, `JL VEREDAPORTO NUTRICAO A`,
   `COOPERATIVA AGROINDUSTRIAL DOS VEREDAIPE CHAPADAAZUL DO CHAPADAPITANGA PEDRACANELA` e
   **`LUZFORTE ENERGIA`** — a conta de luz, informada pelo casal na última rodada.
   O dado sustenta: ela só aparece em **5 dos 9 meses** da fatura, o que nunca fez
   sentido para a luz da própria casa; a da casa está na linha `AGUA E LUZ` da
   planilha, paga fora do cartão.

Total na janela: `R$ 48.906,51`, ou `R$ 5.434,06`/mês.

3. **Outras contas de terceiro**, também informadas pelo casal e com o mesmo
   tratamento (entra e sai): `CAMPOBONITO SERRAPALMA DE G`, `HOSPITAL TUCANOVEREDA PALMABURITI`,
   `CANELABONITO VELOSO SEIXAS`. Os três lançamentos caem em **set/2026**, fora da
   janela: impacto nos números da janela é **zero**. O impacto está no
   **compromisso futuro** — dois são `Parcela 1/2`, então a exclusão tira
   `R$ 300` da esteira de out/2026.

**Ficaram com a família** (o casal revisou e excluiu da conta do pai):
`PEDREIRA LAGOABELO RIOLESTE`, `PB*BRK AGRO`, `MANGALCAJU PRODUTOS AGRO`,
`CERRADOPITANGA` e `JATOBAALTO VET CLINICA` (animal de estimação).

> **Suposição declarada:** a parte 2 depende inteiramente de informação externa
> aos dados — não existe marcador na fatura que distinga uma compra do pai de
> uma da família. Se houver outras compras dele não identificadas, o gasto da
> família está **superestimado** (e o saldo, subestimado).

## Fatura líquida da família

Soma de `valor` de todos os lançamentos do mês, nos dois cartões,
**excluindo**:

- `destino == "PAGAMENTO RECEBIDO"` — é o casal quitando o cartão,
  movimentação de caixa, não gasto (63 lançamentos, `R$ 252.571,43` em 11 meses);
- tudo que cai na **conta do pai** (acima).

**Estornos, créditos de estabelecimento e "IOF de volta" permanecem, com sinal
negativo** — abatem a compra original. São `R$ 1.313,27` na janela, `R$ 146`/mês.
É exatamente a divergência entre esta análise e os números pré-apurados no
brief, que não abatiam estorno.

Média na janela: **`R$ 20.401,40`/mês**.

## Teto de fatura

`renda − contas fixas`. Duas versões, ambas reportadas:

- **teto fixo:** `29.500 − 8.860 = R$ 20.640`. É o número da planilha, usado
  para confirmar a tabela do brief.
- **teto reconstruído:** `29.500 − fixas do mês`, onde as fixas de cada mês são
  recalculadas pelos contadores de parcela (ver abaixo). Vale `R$ 22.355` em
  dez/25–jan/26, `R$ 21.055` em fev–jun/26 e `R$ 20.640` de jul/26 em diante.

A planilha rotula os `R$ 20.640` como "SALDO", o que é errado: não é sobra, é o
limite que a fatura pode ter. Todo o gasto de vida da família passa no cartão.

## Saldo do mês

`teto − fatura líquida da família`. Positivo = o mês fechou no azul.

- Com teto fixo: média `R$ 238,60`/mês, **5 de 9 meses positivos**.
- Com teto reconstruído: média `R$ 850,27`/mês, os **mesmos** 5 positivos.

## Contas fixas por mês (reconstruídas)

A planilha é de **set/2026 apenas**. Cinco itens têm contador de parcela, que
dá o calendário inteiro — se em set/26 o item está na parcela `n` de `tot`, ele
começou em `set/26 − (n−1)` e termina em `set/26 + (tot − n)`:

| item | valor/mês | início | última |
|---|---|---|---|
| EMPRESTIMO 20/36 | `R$ 1.000` | out/2024 | **ago/2027** |
| EMPRESTIMO 34/36 | `R$ 1.900` | dez/2023 | nov/2026 |
| CARRO 08/14 | `R$ 1.300` | fev/2026 | mar/2027 |
| CHEQUE MARINA 59/60 | `R$ 1.300` | nov/2021 | out/2026 |
| MERCADO PAGO 3/10 | `R$ 415` | jul/2026 | abr/2027 |

Os outros nove itens somam `R$ 2.945`/mês e entram como constantes (não têm
prazo declarado).

> **Correção de dado, informada pelo casal:** a planilha rotula o primeiro item
> como `20/36`, mas o contador real em set/2026 é **`24/35`**. A planilha estava
> desatualizada; o contrato ganha (mesma regra do Passo 1). O rótulo da linha foi
> mantido para casar com a planilha, mas o cálculo usa `24/35` — o que antecipa o
> fim da dívida de jan/2028 para **ago/2027**. **Os outros quatro contadores não
> foram conferidos** e definem o calendário inteiro da esteira.

> **Suposição declarada:** os nove itens sem contador são tratados como
> constantes nos nove meses. A planilha é de um mês só e não há como verificar.

## Renda

`R$ 29.500`/mês, soma dos três salários extraídos do rodapé em texto da planilha
(`MARI R$ 21.000` + `ORLANDO CAMARA R$ 5.000` + `ORLANDO UNIFIMES R$ 3.500`).

> **Suposição declarada, e ela é material:** a renda vem da **mesma planilha de
> set/2026 apenas** e é tratada como constante nos nove meses (e, na projeção,
> até mar/2027 — a queda de `R$ 9.500` em abr/2027 foi informada pelo casal) — a mesma limitação
> declarada para as fixas, mas sem reconstrução possível, porque salário não tem
> contador de parcela. Com a margem de `R$ 238,60`, **uma variação de 1% na renda
> (`R$ 295`) inverte o sinal do saldo médio**, e a maior fonte (`R$ 21.000`) é
> plausivelmente variável.

## Categoria

Derivada de `destino` por mapa de regras explícito (célula `REGRAS` do
notebook), **primeira regra que casa vence**, exceções antes das gerais. Duas
armadilhas encontradas e corrigidas: `RESEND` (serviço) engolia o sobrenome
`BEZERRA`, e `AMIL` engolia `BURITIARARA`. Ambas resolvidas com limite de palavra.

> Os números desta seção são calculados sobre `compras` (só lançamentos positivos,
> sem estornos). A base com estornos dá valores ~1% menores — `pessoa_fisica`
> `R$ 2.059,46`, `nao_classificado` `R$ 1.167,12`. Cada um está certo na sua base.

- **`pessoa_fisica`** (`R$ 2.059,46`/mês, **10,1%** do gasto): heurística de
  formato — sem dígito, sem asterisco, sem termo de empresa, e duas palavras ou
  mais. O casal identificou como **mão de obra e serviços contratados** (faxina,
  unha, sobrancelha, depilação). Tem impureza declarada: um fornecedor pequeno
  que recebe no CPF cai aqui.

  > **A categoria é heterogênea e não deve ser lançada inteira como custo fixo.**
  > São 208 lançamentos em 158 destinos, e só **2 aparecem em 3 meses ou mais**,
  > somando `R$ 119,86`/mês (**6%** da categoria) — esse é o único pedaço que se
  > comporta como custo fixo; os outros 156 são pagamentos esporádicos. Um terceiro
  > destino que a frequência marcaria como fixo (`OTAVIO GITURU MEDEIROS FIGUEIREDO`) o
  > casal confirmou ser avulso. Além disso, **32% da categoria tem forma de
  > transferência** (Pix ou caixa alta), podendo ser mais repasse. E `ORLANDO NOGUEIRA PEIXOTO`
  > (`R$ 75`/mês) é Pix do próprio Orlando para si — mantido por conservadorismo, já
  > que retirá-lo melhoraria o saldo.
  >
  > **Sete "pessoas" que não eram pessoa.** A heurística captura estabelecimento
  > cujo nome vem num token só com 12+ caracteres. Reclassificados nesta rodada:
  > `PALMASERRA` (curso), `A-ARARACAJU` (saúde), `SERRAALTO` (pet),
  > `IPECERRADO` (mercado), `MONTEIPE DO RIOFORTE` (restaurante),
  > `S.O.S BURITISERRA` (veículo) e `PALMAVEREDA RIONOVO` (vestuário).
  >
  > E os dois maiores da categoria, identificados pelo casal, eram outra coisa:
  > **`SUSITA CORREIA`** (`R$ 233`/mês) eram as parcelas 6/12 a 12/12 de um
  > **notebook**, encerradas em jun/26 → `outros_compras`; e **`JEQUITIBABELO`**
  > (`R$ 200`/mês) são **compras de comida fiado**, acertadas no fim do mês →
  > `mercado`. Somando todas as reclassificações, `R$ 1.179`/mês saíram de
  > `pessoa_fisica`: ela caiu de `R$ 3.238` para `R$ 2.059`/mês, **36% da categoria**.
- **`nao_classificado`** (`R$ 1.167,12`/mês, **5,7%** do gasto): 168 lançamentos
  em 105 destinos distintos, o maior deles 8,9% do resíduo. Abaixo do limite de
  10% acordado no brief.

## Recorrente

Origem cujas linhas são o mesmo evento repetido: ao menos 3 ocorrências,
coeficiente de variação do valor ≤ 0,10 e do intervalo ≤ 0,25. São 29 origens,
`R$ 2.474,05`/mês.

> ⚠️ **O critério captura parcelamento, não só assinatura** — valor igual em
> intervalo regular é a assinatura exata de uma compra em N×. **Não use os
> `R$ 2.474,05` como gasto cancelável.** A separação:
>
> - **20 das 29 são 100% parcelamento** (`R$ 1.884,44`/mês). `MANGALSOL LAGOAJATOBA`
>   é `Parcela 1/10` a `9/10`; `CERRADOSOL*IPEPEDRA CLINICA` é `Parcela 1/10` a `10/10`.
>   Já contratado, não se cancela.
> - **11 das 29 encerraram** antes de ago/2026 (`R$ 858,87`/mês). `SUSITA
>   CORREIA DE` é `Parcela 10/12`, terminou em jun/26.

Marcado no código por `so_parcela` (todos os lançamentos com sufixo `N/M`) e
`vivo` (último lançamento em ago/2026 ou depois).

## Assinatura cancelável

**`R$ 1.818,51`/mês = `R$ 21.822,12`/ano.** (em out/26, com o Panda Video cancelado e a Vivo do Orlando em `R$ 204,70`) Definição: destino da lista de
recorrentes **ou** da lista declarada `CONFIRMADAS`, cuja cobrança tem preço
estável, medido pelo **valor cobrado em ago/2026** — o último mês completo.

Quatro decisões que a compõem, e cada uma mudou o número:

1. **A régua é o último mês completo, não a média da janela.** Metade destas
   assinaturas não existiu nos nove meses ou mudou de preço. Dividir o total por
   9 daria `R$ 548,34`/mês e subestima; usar ago/26 cru dá o mesmo valor, porque a
   tabela já só contém assinatura confirmada.
2. **"Preço estável" é medido por COBRANÇA, não pela soma do mês.** Se num mês
   caem duas cobranças (porque o anterior não teve nenhuma), a soma dobra e o
   item parece variável sem que o preço tenha mudado — foi o caso da Netflix
   (sempre `R$ 59,90`, cv por cobrança `0,0000`, cv mensal `0,31`) e do Panda
   Video. Critério: `cv por cobrança ≤ 0,10` **ou** `cv dos últimos 3 meses
   ativos ≤ 0,05` (a segunda porta pega quem trocou de plano no meio).
3. **`CONFIRMADAS` é lista declarada, não heurística:** `ANTHROPIC CLAUDE`,
   `GOOGLE ONE`, `APPLE`, `SPOTIFY`, `MAGNIFIC`, `CONTA VIVO` (Apple, Spotify e Vivo
   pelo último preço: `R$ 39,90`, `R$ 23,90` e `R$ 225,66`). Nenhum critério de recorrência
   pega um item com **uma** ocorrência na janela — o `MAGNIFIC` (`R$ 149,39`,
   começou em ago/26) só entrou porque o casal informou que é assinatura.
4. **IOF integralmente estornado NÃO é custo.** Nove serviços em dólar geram uma
   linha `IOF DE "X"` e uma `IOF DE VOLTA DE X` que a anula (líquido `R$ 0,00`).
   Como a tabela só olha lançamentos positivos, a volta sumia e o IOF entrava
   como despesa recorrente inexistente. Os nove ficam de fora (`IOF_ANULADO`);
   `MEE6` e `WHOP`, que não têm estorno, continuam contados.

**Assinatura anual** é caso à parte: cobra uma vez e some, e um ano de dado não
basta para detectá-la. O CapCut (`R$ 187,90` via Apple em ago/26) foi informado
pelo casal, que **não vai renovar** — custo futuro zero. Fica registrado só para
explicar o pico da Apple em ago/26 e para não ser recontado.

**Repartição do total, e ela muda a recomendação:**

| | valor/mês | o que fazer |
|---|---|---|
| já cancelado pelo casal (`JA_DECIDIDO`) | `R$ 362,86` | feito — Wellhub `R$ 149,99`, Resend `R$ 109,98`, Panda Video `R$ 87,90`, um dos dois Google One `R$ 14,99` (cancelamento parcial: o destino segue vivo) |
| serviço essencial | `R$ 492,06` | **renegociar, não cancelar** — Starlink `R$ 249`, Conta Vivo `R$ 204,70` (celular `R$ 54` + internet `R$ 150,70`, out/26) e Claro `R$ 38,36` |
| **decisão ainda em aberto** | **`R$ 963,59`** | `R$ 11.563,08`/ano |

**Cobrança variável no mesmo grupo:** `R$ 0,00`/mês — Apple e Spotify saíram daqui quando
passaram a ser medidos pelo último preço.

## Razão dos 12 primeiros dias

Soma dos lançamentos da família com `data.dt.day <= 12` no mês, dividida pela
média dessa mesma soma nos cinco meses bons (`R$ 7.325,23`).

**Não tem poder discriminante** e não deve ser usada para prever o fechamento:
meses bons ficam entre 0,93× e 1,13×, ruins entre 0,81× e 1,40×, e as faixas se
sobrepõem. Serve só para mostrar que os meses ruins têm **dois modos de falha**
(estouro desde o dia 1 vs. estouro depois do dia 12).

## Orçamento: rotina, fundo de emergência e poupança

**O orçamento tem DUAS réguas, não uma**, e a divisão é medida, não opinada.

**Rotina** — a categoria entra na régua mensal se cumprir as três condições:

1. aparece em **8 ou mais** dos 9 meses;
2. **nenhum mês concentra mais de 30%** do que ela gastou no período;
3. **é fluxo** — ou tem fornecedor que se repete em 5+ meses, ou tem 10+ lançamentos/mês.

A condição 2 separa `restaurante_delivery` (39 destinos diferentes, mas todo mês,
mês mais pesado = 15%) de `casa_construcao` (11 lojas, nenhuma repetida, mês mais
pesado = 37%). A condição 3 derrubou `outros_compras`, que passava nas duas
primeiras sendo um saco de compras avulsas — 24 destinos, nenhum recorrente, e o
mês varia de `R$ 0` a `R$ 1.186`.

Resultado: **11 categorias de rotina** (`R$ 13.501,74`/mês hoje) e **11 de evento**
(`R$ 2.311,36`/mês hoje).

> **Uma correção de dado que a regra expôs.** O mesmo pet shop aparecia com sete
> grafias (`REDE VEREDACERRADO LAGOAJATOBA`, `BARRAMANGAL`, `CAPPTA *REDE VEREDACERRADO PET C`,
> `TRADIO *REDE VEREDACERRADO SERRANOVO`…). Separadas, nenhuma parecia fornecedor recorrente e
> `pet` caía como evento — o que contradizia o casal, que compra ração todo mês.
> Unidas pelo `ALIAS`, a categoria passa na regra sozinha.

**O teto de cada categoria de rotina** é o gasto à vista dela nos **cinco meses
bons** (fev, mar, mai, jun, jul/26) — nível já praticado —, com dois pisos: o
contratual (`utilidades`: Starlink, Vivo e Claro — a luz é do pai) e o de realidade (nenhum teto abaixo do
menor mês já observado, com `np.ceil`). Onde o casal fixou o número, o número do
casal manda (`TETO_CASAL`: mercado `R$ 4.050`, combustível `R$ 1.300`, delivery
`R$ 900`, farmácia `R$ 500`).

> **Mercado inclui o botijão de gás e a água de 20 L** (`CHAPADAREAL GAS` e afins, regra
> `\bGAS\b` antes da de `utilidades`). O casal fixou mercado em `R$ 3.900` antes dessa
> mudança; o teto soma o nível do gás (`GAS_MERCADO = R$ 150`) para não engoli-lo em
> silêncio. **Perfume** (`HNA*OBOTICARIO`, `MP *PRPERFUMES`) é `outros_compras` — compra
> avulsa, sai do fundo. **Wellhub** é academia, em `beleza_academia`.

**Não existe rateio proporcional.** O total do cartão é a **soma** dos tetos, e o
que sobra da renda vira poupança — ao contrário do desenho anterior, em que o
total era fixado antes e apertar uma categoria só afrouxava outra.

**O fundo de emergência** (`FUNDO_EVENTO = R$ 1.600`/mês) é escolha declarada, não
conta. Ele **acumula**: o que importa é cobrir o ano, não o mês. As categorias de
evento custam `R$ 2.311,36`/mês na média dos 9 meses; `R$ 1.600` pede um corte
de 31% no gasto eventual e é a decisão mais dura do
plano. A célula do orçamento imprime a sensibilidade (fundo × poupança × corte
exigido × saldo do fundo em 12 meses).

```
renda pós-choque R$ 20.000 − fixas R$ 2.932 = R$ 17.068 disponíveis
  = rotina R$ 11.381  +  fundo de emergência R$ 1.600  +  poupança R$ 4.087 (20% da renda)

rotina R$ 11.381 = vocês decidem R$ 9.981 (dia a dia R$ 6.750 + no mês R$ 3.231)
                 + sai sozinho no cartão R$ 1.400 (assinaturas R$ 900 + internet e celular R$ 500)

as duas linhas automáticas ficam no preço de hoje, arredondado para cima em R$ 10:
internet e celular R$ 492,06 → R$ 500 e assinaturas ativas R$ 893,60 → R$ 900. O que as
contas baixaram em out/26 (Panda Video cancelado, Vivo do Orlando em R$ 204,70 e a da Mariana
em R$ 62) virou limite de `pessoa_fisica`, por decisão do casal: R$ 1.300 → R$ 1.448. O teto
do cartão vai a R$ 12.981 — os R$ 13 que a conta fora do cartão liberou — e a sobra de cada
mês não muda.
```

Adotá-lo já em out/2026 tem três razões: não existe transição em abril, os seis
meses de renda cheia viram poupança pura, e o orçamento fica testável enquanto
ainda dá tempo de ajustar.

**O teto é de consumo À VISTA.** No extrato mês a mês a `parcela_cartão` é coluna
separada do `consumo`; o orçamento por categoria usa, portanto, só lançamentos
**sem** sufixo `N/M`. Montar sobre o gasto com parcela dentro comparava um teto à
vista contra um "hoje" que inclui carnê, e produzia dois artefatos: `cursos` (97%
parcela) ganhava teto **maior** que o gasto, e o piso "contratual" reservava
orçamento para **53 planos já encerrados**.

> **Parcela morta na média:** dos `R$ 4.558`/mês de carnê medidos nos nove meses,
> **`R$ 2.518` (55%)** vêm de planos que terminaram antes de ago/2026 — o maior é
> `SUSITA CORREIA` (`R$ 233`/mês, notebook em 12×, última parcela em jun/26).

**Exequibilidade:** `meses_que_cabem` conta em quantos dos 9 o gasto à vista coube
no teto — **nenhuma categoria de rotina ficou em 0 ou 1 de 9**, e nenhuma leva
corte de 50% ou mais. As de evento ficam fora do teste: não têm teto mensal. O
fundo cobriu o gasto eventual em 4 dos 9 meses; no mês mais pesado faltariam
`R$ 2.426,24` — é para isso que ele acumula.

**Regra semanal derivada:** as quatro categorias de decisão diária (mercado,
combustível, delivery, farmácia) somam `R$ 6.750,00`/mês à vista =
**`R$ 1.558,89`/semana** = `R$ 225,00`/dia. Hoje somam `R$ 7.696,02`/mês.

**Os quatro bolsos** (estrutura de lançamento, não de análise): 1. dia a dia
`R$ 6.750`; 2. no mês — faxina, unha, academia, ração, pagamentos a pessoas,
compras pequenas `R$ 3.083`; 3. automático — assinaturas e internet/celular `R$ 1.535`;
4. fundo de emergência `R$ 1.600`. As 22 categorias continuam existindo para analisar; os bolsos
existem para lançar.

**Efeito na projeção:** o corte que este orçamento pede (`R$ 2.832,11`/mês) fica
entre o cenário médio e o agressivo. Depois do choque ele deixa `R$ 3.573,34`/mês
de sobra média, 0 de 12 meses negativos, pior mês `R$ 2.443,28`, e tolera
`R$ 2.425`/mês de estouro antes de algum mês fechar negativo — se o parcelamento
parar. Sem parar de parcelar, o mesmo orçamento fica negativo nos 12 meses.

**A rotina somada nunca coube inteira.** Cada teto de categoria já foi praticado,
mas a soma das 11 de rotina nunca ficou abaixo de `R$ 11.616,95` (mar/26); a média dos
meses bons foi `R$ 12.026,15`, `R$ 658,15` acima do teto. A folga acima absorve isso.

**Parcela por fora:** `R$ 3.635,61`/mês de carnê vivo em ago/26 cai **além** deste
teto. Vai a zero até set/2027 se nenhum plano novo for assumido.

> **O teto POR PESSOA foi calculado e descartado.** Seria Orlando `R$ 9.861,14` e
> Mariana `R$ 8.509,93` (meta do cenário médio rateada pela participação atual
> de cada cartão). Ele **não é operável**: 99% da conta do pai (`R$ 5.352,98` dos
> `R$ 5.434,06`/mês) está no cartão da Mariana, cuja fatura **bruta** roda
> `R$ 14.803,40`/mês — o número que o aplicativo mostra nunca seria o dela. O
> cartão virtual do pai continua necessário mesmo com teto familiar, para que a
> soma das duas faturas signifique o gasto do casal sem cálculo manual.

> **Nome de terceiro nos artefatos:** o `analise.html` e este arquivo contêm
> nomes de prestadores de serviço e de conhecidos. É decisão consciente — é o
> dado do próprio casal, eles precisam reconhecer a quem pagam, e os dois
> arquivos vão só para eles. **O painel, que é o artefato que circula, está
> limpo** (nenhum nome, CPF, e-mail ou telefone; os planos do Wellhub aparecem
> como "Wellhub (plano de R$ X)").

## Esteira de dívidas (obrigação futura)

Para cada mês de out/2026 a mar/2028: `fixas do mês` (pelo calendário de
parcelas acima) mais as parcelas de cartão ainda a cair.

As parcelas de cartão são projetadas do último `N/M` visto até `M`, agrupando
por `pessoa + destino + total de parcelas + índice da repetição`. **O que separa
dois planos paralelos no mesmo estabelecimento é a REPETIÇÃO do número da
parcela** (a 2ª vez que "1/3" aparece é outro carnê), não o valor — parcelas do
mesmo plano diferem por centavos, e usar o valor na chave partiria um carnê em
dois. Sem essa separação o caso `Palmaserra` colapsava e a projeção caía de
`R$ 8.384,37` para `R$ 5.439,76` — 35% a menos.

Depois disso, a varredura de estornos (`parcelamentos_cancelados`) tirou **4
planos cancelados** que estavam contados como obrigação futura sem existir. O
total final é **`R$ 5.673,37`** em **15 planos com parcela a cair de out/26 em
diante** — foram 84 planos vistos na janela (sem contar cancelados), a maioria já
encerrada. O inventário conta 20 planos e `R$ 7.823,58` porque parte de set/26; a
diferença de `R$ 2.150,21` é exatamente o que vence naquele mês. Dos `R$ 5.673,37`,
`R$ 4.924,45`
(**87%**) vencem antes de abr/2027 e só `R$ 748,92` atravessam o choque.

> **Duas fragilidades declaradas do detector de cancelamento:** (a) ele exige
> valor exato dentro de 45 dias, então estorno parcial, estorno do valor cheio da
> compra ou crédito tardio não casam — nesses casos o compromisso futuro fica
> **superestimado**, o que é o lado conservador; (b) ele casa por destino +
> valor, não por identificador de plano, então dois carnês de mesma parcela no
> mesmo estabelecimento podem trocar de atribuição — o total não muda, mas a
> **data de término** de um deles pode sair errada.

- Liberação total das fixas: **`R$ 5.915`/mês** entre out/2026 e mar/2028.
- Teto de fatura: sobe de `R$ 20.640` para **`R$ 23.840`** (dez/26–mar/27) e **cai
  para `R$ 15.640` em abr/27**, estabilizando em `R$ 17.055` de set/27. A esteira dá
  `R$ 5.915` e o choque tira `R$ 9.500`: ela **amortece, não compensa** — sem ela o
  teto pós-choque seria `R$ 11.140`. Com o consumo congelado no nível médio
  (`R$ 20.401,40`), nenhum dos 12 meses pós-choque fecha positivo.

  > ⚠ Uma versão anterior desta entrada dizia "sobe para `R$ 26.555`". Esse número
  > usava `RENDA` fixo nos 18 meses, ignorando o choque — o mesmo erro que a
  > projeção de poupança tinha. `R$ 26.555` só existiria se a renda não caísse.
- `R$ 3.200`/mês chegam entre nov e dez/2026 (`CHEQUE MARINA` + `EMPRESTIMO 34/36`).

> **Contra-evidência medida:** na janela o casal iniciou **63 parcelamentos
> novos (7/mês)**, criando `R$ 996,13`/mês de nova parcela mensal e
> `R$ 4.566,53`/mês de novo compromisso total. É por isso que o parcelado ficou
> estável em ~`R$ 4.500`/mês: as parcelas vencem e são repostas na mesma
> velocidade. A projeção só vale se isso parar.

## Deduplicação planilha × fatura

**Não existe.** Três itens pareciam contar duas vezes, e os três caíram:

- **`VIVO`** — são duas contas de duas pessoas: `R$ 75` é o celular da Mariana, pago
  fora do cartão; os `R$ 209`/mês de `CONTA VIVO` na fatura são o celular e a
  internet do Orlando, em débito automático. **Nome igual não é conta igual.**
- **`PLANO SAUDE`** — o `MANUAL SAUDE BRASIL` da fatura (`R$ 74`/mês) não é o plano de
  `R$ 1.300` da planilha.
- **`LUZFORTE`** — a última a cair, e a maior. Não é dupla contagem porque **não é
  conta do casal**: é do pai (ver §Conta do pai). Saiu do gasto do casal, e com ela
  saiu a ressalva de "dupla contagem declarada e mantida" que esta análise carregou
  por várias rodadas.

Falta uma linha na planilha, descoberta pelo mesmo cruzamento: `CLARO`,
`R$ 38,36`/mês, em débito automático no cartão do Orlando e sem registro nenhum.

## Mês bom / mês ruim

Classificação pelo sinal do saldo com teto fixo, e ela **não muda** com o teto
reconstruído:

- **bons (5):** fev, mar, mai, jun, jul/2026 — média `R$ 17.526,49`
- **ruins (4):** dez/2025, jan, abr, ago/2026 — média `R$ 23.995,03`

## Cenários de corte

Todos calculados como "trazer a categoria ao nível médio dos **meses bons**",
que é nível já praticado, não hipótese — e **descontando a fração já parcelada**
de cada categoria, porque parcela contratada não responde a decisão de corte.

| cenário | libera/mês | % da renda | o que faz |
|---|---|---|---|
| leve | `R$ 1.103,50` | 3,7% | trava os episódicos no nível bom + metade das assinaturas vivas |
| médio | `R$ 2.019,85` | 6,8% | leve + traz as categorias correntes ao nível bom |
| agressivo | `R$ 3.884,64` | 13,2% | reproduz fev–mar/2026 (`R$ 16.516,76`/mês) |

> **`frac_parc` é o que separa corte pedido de corte possível.** O corte bruto
> pedido pelas categorias correntes é `R$ 1.645,05`/mês; descontada a parcela já
> contratada, o **realmente discricionário** é `R$ 1.258,49` (o cenário médio soma
> a isso a parte cortável das assinaturas vivas, chegando a `R$ 2.019,85`). `veiculo_manutencao`
> é o caso extremo: **90,3% da categoria é parcela**, e o corte de `R$ 170` que a
> primeira versão somava não existe.

**Os cenários se combinam com uma segunda alavanca, e ela é maior:** a carga de
parcelas cai de `R$ 3.635,61` (ago/26) para `R$ 624,06` (dez/26) sozinha —
`R$ 3.011,55`/mês — **desde que as compras parem**, não só que deixem de ser
parceladas. Pagar à vista o mesmo consumo não reduz a fatura do mês.

Por isso toda projeção traz **dois ramos**: `saldo_se_parar` e
`saldo_se_ritmo_continuar` (o ritmo medido de 7 planos novos/mês). Num mês no
nível do pior medido (dez/25), o cenário leve sobra `R$ 2.947,78` no primeiro ramo
e **estoura `R$ 63,77`** no segundo; o médio sobra `R$ 3.864,13` e `R$ 852,58`.
O agressivo sobrevive aos dois (`R$ 5.718,44` e `R$ 2.706,89`) — **mas só em dez/26**:
depois do choque, sem parar de parcelar, ele fica negativo em 5 de 12 meses.
**Os cenários medem o tamanho do esforço; o plano é o orçamento por categoria.**

> **A carga de parcela do ramo pessimista tem DUAS bases, e elas divergem no
> veredito do cenário médio.** A célula dos dois ramos usa a de **ago/26** (`R$ 3.635,61`), a
> mais recente medida; o extrato mês a mês e os cenários de poupança usam a
> **média** dos nove meses (`R$ 4.588,29`), porque lá o cenário "nada muda"
> projeta o comportamento médio. Na base de ago/26 o médio sobrevive por
> `R$ 852,58`; na base média ele **estoura `R$ 100,10`** (e o leve, `R$ 1.016,46`). Citar uma base como se fosse a
> outra muda a alavanca em `R$ 953` — sempre diga qual está em uso.

Episódicos: `veiculo_manutencao`, `vestuario_calcado`, `casa_construcao`,
`viagem`. Correntes: `mercado`, `combustivel`, `farmacia`,
`restaurante_delivery`, `marketplace`, `beleza_academia`,
`assinaturas_digitais`.

## Quitação: custo e momento

O **custo de quitar uma dívida fixa depende de quando se quita**, e a soma
nominal das parcelas é o **teto** (a quitação antecipada tem desconto
proporcional de juros por lei). Para o `EMPRESTIMO` de `R$ 1.000`/mês, que
termina em ago/2027:

| quitando em | parcelas restantes | custo nominal (teto) |
|---|---|---|
| out/2026 | 11 | `R$ 11.000` |
| dez/2026 | 9 | `R$ 9.000` |
| **mar/2027** | **6** | **`R$ 6.000`** |
| abr/2027 | 5 | `R$ 5.000` |

Como o nominal é 1:1, quitar cedo não dá vantagem financeira — só antecipa a
saída de caixa. **Momento recomendado: mar/2027**, imediatamente antes do choque
de renda.

**Antecipar parcela de cartão não vale**, e a razão é de custo por unidade de
alívio: `R$ 1,00` liberado por `R$ 1,00` gasto no empréstimo (que tem juros a
economizar) contra `R$ 0,61` no cartão (parcelado de loja, sem juros — antecipar
troca rendimento por nada). Além disso, **87% do carnê vence sozinho antes de
abr/2027**; o que sobra são `R$ 748,92` em três planos de curso.

## Sobra mensal projetada e acumulado (a métrica do painel)

Para cada mês `p` de out/2026 a mar/2028:

```
sobra(p) = renda_do_mes(p) − fixas_do_mes(p) − max(à vista − corte, 0) − parcela(p)
renda_do_mes(p) = R$ 29.500 até mar/2027, R$ 20.000 de abr/2027 em diante
```

- **`à vista`** = `R$ 15.813,11`/mês (média dos 9 meses, só lançamentos sem `N/M`).
- **`corte`** = o do cenário (0 · `R$ 1.103,50` · `R$ 2.832,11` do orçamento · `R$ 3.884,64`; ver Cenários).
- **`parcela(p)`** = a projeção da esteira se o cenário **para** de parcelar; a
  carga média (`R$ 4.588,29`) se não para.
- **Cenário A** soma ainda `R$ 190,56`/mês **cumulativos** de dívida fixa nova, que
  é o ritmo medido (carro em fev/26 + Mercado Pago em jul/26, os dois em 9 meses).

Acumulado em 18 meses: A `R$ -62.391` · B `R$ 47.110` · C `R$ 66.973` ·
D `R$ 98.088` · E `R$ 117.033`.

> **A renda do mês não é opcional nesta fórmula.** Duas versões desta análise
> usaram `RENDA` fixo aqui e na esteira, e cada uma inflou o resultado em
> `R$ 9.500` por mês projetado além de abr/2027 — `R$ 114.000` no acumulado de 18
> meses. `CHOQUE` e `renda_do_mes` estão no bloco de constantes, junto com `RENDA`,
> justamente para que nenhuma projeção nova esqueça.

## O que os dados não respondem

- **Quanto do consumo restante ainda é do pai.** Uma compra dele num
  supermercado é idêntica a uma da família na fatura. Nenhuma heurística foi
  inventada; o valor da família é um **teto**.
- **Se há tarifa de Pix no Crédito.** Zero lançamentos de tarifa ou taxa em 11
  meses. Não estimado, conforme o brief. Exigiria o extrato de conta corrente.
- **Juros e saldo devedor das dívidas.** Sem os contratos, não há como ordenar
  a quitação por custo.
- **Receita rural**, se houver. Não está nestes dados.

## Atualizações informadas pelo casal depois do fechamento

A planilha e os preços envelhecem; quando o casal informa um valor novo, ele ganha —
mesma regra do contador do empréstimo. Registradas em out/2026:

- **Panda Video cancelado** (`R$ 87,90`/mês).
- **Vivo do Orlando** (no cartão): celular de `R$ 75` para `R$ 54`; com a internet de
  `R$ 150,70`, a Conta Vivo passa de `R$ 225,66` para `R$ 204,70` (`PRECO_CORRENTE`).
- **Vivo da Mariana** (fora do cartão, linha `VIVO` da planilha): `R$ 75` → `R$ 62`
  (`ATUALIZA_PLANILHA`, válido de out/26 em diante — a reconstrução dos meses da janela
  continua com o valor antigo, que é o que foi pago).
- **Destino do que sobrou:** por pedido do casal, virou limite de `pessoa_fisica`
  (R$ 1.300 → R$ 1.448), e não poupança. A sobra mensal projetada não muda.

## Varredura final: custo escondido e cobranças a conferir

Última passada nas 20 faturas (inclui set/26), só gasto do casal. Não altera nenhum
número do plano; gera itens de conferência.

- **Custo do Pix no Crédito** — não aparece como tarifa na fatura; vem embutido no valor.
  Evidência: 3% dos Pix no Crédito terminam em `,00` contra 31% das outras compras, e o
  único reembolso da janela (CAJUALTO) devolveu `R$ 34,80` de um Pix de `R$ 37,84` (8,7%).
  Estimativa: `volume do casal/mês × 2%` a `× 8,7%` = `R$ 17,72` a `R$ 77,38`/mês.
  **Estimativa, não medida** — o valor exato de cada Pix está no app.
- **Google One em dobro** (`R$ 49,99` e `R$ 14,99`): o de `R$ 14,99` foi cancelado em set/26.
- **Conferidos pelo casal, sem erro:** Wellhub `R$ 149,99` de 05/09/26 foi a última
  cobrança; os dois carnês Asimov são cursos diferentes; a Conta Vivo de `R$ 75` no cartão
  é a linha do Orlando e o `VIVO` da planilha é a da Mariana, fora do cartão.
