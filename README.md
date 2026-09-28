# Caso Prático: Hipoglicemia no HUF — Da Prescrição Eletrônica à Razão de Chances

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![Pandas](https://img.shields.io/badge/Library-Pandas-150458.svg)](https://pandas.pydata.org/)
[![Status](https://img.shields.io/badge/Status-Concluído-brightgreen.svg)]()

Repositório da atividade prática da disciplina de **Epidemiologia** (INF06001), do curso de **Informática Biomédica** da **Universidade Federal de Ciências da Saúde de Porto Alegre (UFCSPA)**.

> **Docente:** Profa. Dra. Andréia Turmina Fontanella  
> **Tema:** Estudos Caso-Controle, Farmacoepidemiologia, Prontuário Eletrônico do Paciente (PEP/RWD) e Análise de Associação (Odds Ratio).  
> *Nota: Os dados do Hospital Universitário Fictício (HUF) foram gerados para fins didáticos.*

---

## 📋 Sumário
1. [Contextualização do Caso Clínico](#-contextualização-do-caso-clínico)
2. [Estrutura do Repositório](#-estrutura-do-repositório)
3. [Delineamento Epidemiológico](#-delineamento-epidemiológico)
4. [Instalação e Execução](#-instalação-e-execução)
5. [Resumo das Questões e Resultados](#-resumo-das-questões-e-resultados)
   - [Parte 1 — Reconhecimento (Q1 e Q2)](#parte-1--reconhecimento)
   - [Parte 2 — Exposição e Razão de Chances (Q3 e Q4)](#parte-2--exposição-e-razão-de-chances)
   - [Parte 3 — O Impacto da Dimensão Temporal (Q5)](#parte-3--o-impacto-da-dimensão-temporal)
   - [Parte 4 — Confundimento e Vieses em Dados Eletrônicos (Q6 e Q7)](#parte-4--confundimento-e-vieses-em-dados-eletrônicos)
6. [Folha de Respostas Sintética](#-folha-de-respostas-sintética)
7. [Conclusões para a Farmacovigilância](#-conclusões-para-a-farmacovigilância)
8. [Referências](#-referências)

---

## 🏥 Contextualização do Caso Clínico

A emergência do Hospital Universitário Fictício (HUF) identificou um aumento expressivo no atendimento de pacientes com **Diabetes Mellitus Tipo 2 (DM2)** apresentando quadros graves de **hipoglicemia**. A Comissão de Farmacovigilância do hospital solicitou um estudo para investigar se o uso de **sulfonilureias** está associado ao aumento desse desfecho.

A comissão disponibilizou os registros estruturados de um **estudo caso-controle aninhado**, contendo as variáveis clínicas básicas dos pacientes e todas as prescrições médicas de antidiabéticos orais extraídas do prontuário eletrônico.

---

## 📁 Estrutura do Repositório

```text
Caso-controle-atividade/
├── atividade-caso-controle.ipynb   # Jupyter Notebook com a resolução e análises em Python
├── base_caso_controle.csv          # Base do estudo (1.270 pacientes: 254 casos e 1.016 controles)
├── prescricoes.csv                 # Extração de prescrições hospitalares (24.101 registros)
├── Dicionario_de_Dados_HUF_aula.xlsx # Metadados, dicionário de variáveis e códigos ATC
├── Enunciado_Caso_HUF_INF06001.pdf # Documento oficial com as orientações do trabalho
└── README.md                       # Documentação completa do projeto
```

### Detalhes das Bases de Dados
* **`base_caso_controle.csv`** (1.270 linhas):
  - `id_obs`: Identificador da observação.
  - `id_paciente`: Identificador único do paciente.
  - `grupo`: Condição clínica no estudo (`caso` = atendimento por hipoglicemia; `controle` = paciente sem o desfecho).
  - `data_indice`: Data de ocorrência do evento de emergência (para casos) ou data de amostragem pareada (para controles).
  - `sexo`: Sexo biológico (`M` / `F`).
  - `idade`: Idade do paciente em anos.
  - `drc`: Presença de Doença Renal Crônica diagnosticada (`1` = Sim, `0` = Não).
* **`prescricoes.csv`** (24.101 linhas):
  - `id_prescricao`: Identificador da prescrição médica.
  - `id_paciente`: Identificador do paciente.
  - `data_prescricao`: Data em que o medicamento foi prescrito no sistema.
  - `codigo_atc`: Código da classificação Anatômica Terapêutica Química (*Anatomical Therapeutic Chemical*).
  - `medicamento`: Denominação comum do fármaco prescrito.
  - `dias_tratamento`: Duração estipulada do tratamento na prescrição.

---

## 🔬 Delineamento Epidemiológico

* **Tipo de estudo:** Estudo caso-controle aninhado (*nested case-control study*) com amostragem por densidade de incidência.
* **Pareamento temporal:** Cada caso foi pareado com 4 controles (proporção 1:4) presentes no banco na mesma `data_indice`.
* **Desfecho primário:** Atendimento em serviço de emergência hospitalar devido a episódio de hipoglicemia aguda.
* **Exposição de interesse:** Prescrição ativa recente de sulfonilureias (Glibenclamida, Gliclazida ou Glimepirida) na janela de **90 dias que antecedem a data-índice** (`[data_indice - 90 dias, data_indice)`).

---

## 🚀 Instalação e Execução

### Pré-requisitos
* Python 3.10 ou superior
* Gerenciador de pacotes `pip`

### 1. Clonar ou Acessar o Diretório
```bash
cd "Caso-controle-atividade"
```

### 2. Instalar as Dependências
Recomenda-se a criação de um ambiente virtual:
```bash
python -m venv .venv
# No Windows PowerShell:
.venv\Scripts\Activate.ps1

# Instalação dos pacotes necessários:
pip install pandas numpy jupyter
```

### 3. Abrir o Jupyter Notebook
```bash
jupyter notebook atividade-caso-controle.ipynb
```

---

## 📊 Resumo das Questões e Resultados

### Parte 1 — Reconhecimento

#### Q1. Códigos ATC de Sulfonilureias e Volume de Prescrições
Consultando a classificação ATC no dicionário de dados (`Dicionario_de_Dados_HUF_aula.xlsx`), as sulfonilureias correspondem ao grupo `A10BB`:
* **`A10BB01`**: Glibenclamida (1.137 prescrições)
* **`A10BB09`**: Gliclazida (1.000 prescrições)
* **`A10BB12`**: Glimepirida (898 prescrições)

> **Total no banco de dados:** **3.035** prescrições de sulfonilureias.

---

#### Q2. Comparação entre Casos e Controles (Idade e DRC)

| Grupo | N | Idade Média (± DP) | Pacientes com DRC ($n$) | Proporção com DRC (%) |
| :--- | :---: | :---: | :---: | :---: |
| **Casos** | 254 | 61,53 ± 13,10 anos | 55 | **21,65%** |
| **Controles** | 1.016 | 63,30 ± 13,38 anos | 108 | **10,63%** |

* **Idade:** Os dois grupos apresentam distribuição etária muito próxima (cerca de 61 a 63 anos), o que afasta a idade como principal fator de confusão residual descontrolado.
* **Doença Renal Crônica (DRC):** A prevalência de DRC é **o dobro** entre os casos em relação aos controles (21,65% vs. 10,63%). Pacientes renais crônicos possuem depuração plasmática reduzida de hipoglicemiantes orais e neoglicogênese renal prejudicada, o que os torna intrinsecamente mais suscetíveis a hipoglicemias severas.

---

### Parte 2 — Exposição e Razão de Chances

#### Q3. Definição da Variável de Exposição Temporal
A exposição foi operacionalizada considerando se o paciente possuía pelo menos uma prescrição de sulfonilureia no intervalo temporal de 90 dias prévios ao evento:
$$\text{data\_indice} - 90 \text{ dias} \le \text{data\_prescricao} < \text{data\_indice}$$

* **Prevalência de exposição entre os Casos:** **30,71%** (78 / 254)
* **Prevalência de exposição entre os Controles:** **12,50%** (127 / 1.016)

---

#### Q4. Tabela 2×2, Razão de Chances (OR) e Intervalo de Confiança

##### Tabela de Contingência 2×2:
| Condição | Casos ($n = 254$) | Controles ($n = 1.016$) | Total |
| :--- | :---: | :---: | :---: |
| **Expostos** ($a, b$) | **78** ($a$) | **127** ($b$) | 205 |
| **Não Expostos** ($c, d$) | **176** ($c$) | **889** ($d$) | 1.065 |
| **Total** | 254 | 1.016 | 1.270 |

##### Cálculos:
$$\text{OR}_{\text{bruta}} = \frac{a \cdot d}{b \cdot c} = \frac{78 \cdot 889}{127 \cdot 176} = \frac{69.342}{22.352} \approx \mathbf{3,10}$$

$$\text{EP}(\ln \text{OR}) = \sqrt{\frac{1}{78} + \frac{1}{127} + \frac{1}{176} + \frac{1}{889}} \approx 0,1658$$

$$\text{IC 95\%} = \exp\left(\ln(3,10) \pm 1,96 \times 0,1658\right) = \mathbf{[2,24 \,;\, 4,29]}$$

> **Frase de Interpretação para a Comissão:**  
> *"Pacientes com diabetes mellitus tipo 2 que tiveram prescrição de sulfonilureia nos 90 dias anteriores apresentaram uma chance 3,10 vezes maior de atendimento na emergência por hipoglicemia em comparação àqueles não expostos (OR = 3,10; IC 95%: 2,24 a 4,29), demonstrando uma associação positiva, robusta e estatisticamente significante."*

---

### Parte 3 — O Impacto da Dimensão Temporal

#### Q5. Exposição em Qualquer Momento (Ignorando a Janela Temporal)
Redefinindo a exposição como *ter qualquer prescrição de sulfonilureia registrada no histórico eletrônico*:
* Casos expostos: $a = 103$ | Casos não expostos: $c = 151$
* Controles expostos: $b = 299$ | Controles não expostos: $d = 717$

$$\text{OR}_{\text{ever}} = \frac{103 \cdot 717}{299 \cdot 151} = \frac{73.851}{45.149} \approx \mathbf{1,64} \quad (\text{IC 95\%: } [1,23 \,;\, 2,17])$$

#### Por que a OR caiu expressivamente de 3,10 para 1,64?
1. **Causalidade Reversa e Desprescrição Pós-Evento:** Ao desconsiderar o tempo e incluir prescrições posteriores à data-índice, ignora-se a conduta médica habitual: após um paciente sofrer um episódio grave de hipoglicemia na emergência, o médico assistente **suspende ou troca a sulfonilureia** por segurança (desprescrição).
2. **Diluição da Associação Aguda:** O risco de hipoglicemia por sulfonilureia é farmacologicamente agudo/recente. Ao tratar o uso remoto no passado como "exposição ativa", indivíduos sem circulação ativa da droga são classificados como expostos, diluindo o efeito e enviesando a OR em direção à nulidade ($\text{OR} \to 1$).

---

### Parte 4 — Confundimento e Vieses em Dados Eletrônicos

#### Q6. A DRC pode confundir a associação?
Para atuar como fator de confusão clássico, a DRC deve:
1. **Estar associada ao desfecho:** Confirmado — pacientes com DRC têm prevalência duas vezes maior entre os casos (21,65%) do que entre controles (10,63%).
2. **Estar associada à exposição:** Confirmado — a prescrição recente de sulfonilureia é menor nos pacientes com DRC (10,43%) do que nos sem DRC (16,98%), pois a diretriz clínica contraindica ou impõe cautela no uso dessas drogas em renais crônicos (*confounding by contraindication*).
3. **Não ser um intermediário biológico na cadeia causal entre a droga e o evento:** Confirmado (a DRC pré-existe à prescrição da sulfonilureia).

##### Análise Estratificada por DRC:
* **Estrato Sem DRC ($\text{DRC} = 0$):**
  $$a = 69, \, b = 119, \, c = 130, \, d = 789 \implies \mathbf{\text{OR}_{\text{sem DRC}} = 3,52} \quad (\text{IC 95\%: } [2,48 \,;\, 4,99])$$
* **Estrato Com DRC ($\text{DRC} = 1$):**
  $$a = 9, \, b = 8, \, c = 46, \, d = 100 \implies \mathbf{\text{OR}_{\text{com DRC}} = 2,45} \quad (\text{IC 95\%: } [0,89 \,;\, 6,74])$$
* **OR Bruta:** $\mathbf{3,10}$

**Conclusão Epidemiológica:**  
A presença de DRC atua como um fator de confusão negativo (atenuador da associação bruta). Ao remover a interferência da DRC por estratificação, o risco atribuível ao uso de sulfonilureia entre pacientes com função renal preservada é ainda maior ($\text{OR} = 3,52$). Em ambos os grupos, a associação com a sulfonilureia permanece clinicamente relevante.

---

#### Q7. Fontes de Viés Típicas em Dados Secundários de Prontuário Eletrônico (PEP)

1. **Viés de Aferição/Informação da Exposição (Prescrição $\neq$ Ingestão):**
   * Os dados capturam o ato de *prescrever* o fármaco no sistema eletrônico hospitalar, mas não garantem a *dispensação*, muito menos a adesão posológica real do paciente (*compliance*). Ademais, pacientes podem obter medicamentos fora da rede do HUF (ex.: Farmácia Popular, compra direta em farmácias comerciais), gerando erro de classificação não-diferencial ou diferencial da exposição.
2. **Viés de Seleção e Subnotificação do Desfecho (Critério Hospitalar):**
   * O estudo captura apenas episódios de hipoglicemia graves o suficiente para motivar atendimento em pronto-socorro e que foram atendidos especificamente no HUF. Casos leves/moderados revertidos em domicílio ou atendidos em Unidades Básicas de Saúde (UBS) e UPAs não entram na base de casos, o que pode restringir a validade do estudo aos casos graves ou selecionar uma população com pior suporte social/familiar.

---

## 📝 Folha de Respostas Sintética

| Questão | Campo | Resultado |
| :--- | :--- | :--- |
| **Q1** | Códigos ATC de Sulfonilureias | `A10BB01`, `A10BB09`, `A10BB12` |
| | Nº total de prescrições | **3.035** |
| **Q2** | Idade média | Casos: **61,53** anos \| Controles: **63,30** anos |
| | Proporção com DRC | Casos: **21,65%** \| Controles: **10,63%** |
| **Q3** | Prevalência de exposição recente (90d) | Casos: **30,71%** \| Controles: **12,50%** |
| **Q4** | Matriz 2×2 | $a = 78$, $b = 127$, $c = 176$, $d = 889$ |
| | Razão de Chances (OR) e IC 95% | $\mathbf{OR = 3,10}$ (IC 95%: **2,24 a 4,29**) |
| **Q5** | Matriz 2×2 (qualquer época) | $a = 103$, $b = 299$, $c = 151$, $d = 717$ |
| | Nova OR | $\mathbf{OR = 1,64}$ (IC 95%: **1,23 a 2,17**) |
| **Q6** | Estratificação por DRC | $\text{OR}_{\text{sem DRC}} = \mathbf{3,52}$ \| $\text{OR}_{\text{com DRC}} = \mathbf{2,45}$ \| $\text{OR}_{\text{bruta}} = \mathbf{3,10}$ |

---

## 💡 Conclusões para a Farmacovigilância

1. O uso de sulfonilureias nos últimos 90 dias confere um aumento substancial e clinicamente relevante na probabilidade de quadros de hipoglicemia aguda atendidos na emergência.
2. Recomenda-se a revisão periódica de planos terapêuticos de pacientes com DM2, priorizando alternativas com menor propensão à hipoglicemia em pacientes de risco.
3. É imperativa a cautela redobrada ou substituição de sulfonilureias em pacientes com declínio de função renal (DRC), dada a alta frequência deste grupo entre os casos de emergência.

---

## 📚 Referências

* **HULLEY, S. B. et al.** *Delineando a Pesquisa Clínica.* 5. ed. Porto Alegre: Artmed. Cap. 9 (Estudos Caso-Controle).
* **MEDRONHO, R. A. et al.** *Epidemiologia.* 2. ed. São Paulo: Atheneu, 2009. Caps. 9 (Viés e Confundimento) e 11.
* **FLETCHER, R. H. et al.** *Epidemiologia Clínica: Elementos Essenciais.* 6. ed. Porto Alegre: Artmed, 2021. Cap. 7.
